+++
title = "c. Batch-Squared Nextflow"
date = 2019-09-18T10:46:30-04:00 
weight = 30 
tags = ["tutorial", "configure", "initialize", "Nextflow"]
+++

## Batch-Squared


Since the master `nextflow` process needs to be connected to jobs to
monitor progress, using a local laptop, or a dedicated EC2 instance
for the master `nextflow` process is not ideal for long running
workflows.  In both cases, you need to make sure that the machine or
EC2 instance stays on for the duration of the workflow, and is
shutdown when the workflow is complete.  If a workflow finishes in the
middle of the night, it could be hours before the instance is turned
off.

The `nextflow` executable is fairly lightweight and can be easily
containerized.  After doing so, you can then submit a job to AWS Batch
that runs `nextflow`.  This job will function as the master `nextflow`
process and submit additional AWS Batch jobs for the workflow.  Hence,
this is AWS Batch running AWS Batch, or Batch-squared!

The benefit of Nextflow on Batch-squared is that since AWS Batch is
managing the compute resources for both the master `nextflow` process
_and_ the workflow jobs, once the workflow is complete, everything is
automatically shut-down for you.

![batch-squared](/images/nextflow/submitting-workflows-batch-squared.png)


### Submitting a Nextflow workflow

Let's submit a Nextflow workflow to the Batch-squared architecture, e.g. the `nextflow run hello` for the "hello" workflow :

1. Go to the AWS Batch Console
2. Click "Jobs"
3. Click "Submit job"
4. Set "Job name" as "nf-workflow-hello"
5. For "Job definition" select "nextflow:1"
6. For "Job queue" select "highpriority".  It is important that the `nextflow` master process not be interrupted for the duration of the workflow, so we need to make sure that the job for the master process is placed on an on-demand instance.
7. In the "Command" field type "hello".  Text here is the same as what would be sent as `...` arguments to a `docker run -it nextflow ...` command
8. Click "Submit job".

To monitor the status of the workflow, navigate back to the AWS Batch Console and click on "Dashboard" in the left sidebar.

You should see 1 job advance from "SUBMITTED" to "RUNNING" in the "highpriority" queue row.

Once the job enters the "RUNNING" state, you should see 4 additional jobs get submitted to the "default" queue.  These are the processes defined in the workflow.

When jobs are complete (either FAILED or SUCCEEDED) you can check the logs generated.

You can also use the AWS CLI to submit workflows.  For example, to run the `nextflow` "hello" workflow, type the following into a bash terminal:

```bash
HIGHPRIORITY_JOB_QUEUE=$(aws --region $AWS_REGION batch describe-job-queues | jq -r .jobQueues[].jobQueueName | grep highpriority)
aws batch submit-job \
  --job-definition nextflow \
  --job-name nf-workflow-hello \
  --job-queue $HIGHPRIORITY_JOB_QUEUE \
  --container-overrides command=hello
```

You should get a response like:

```json
{
    "jobName": "nf-workflow-hello", 
    "jobId": "93e2b96e-9bee-4d67-b935-d31d2c12173a"
}
```

which contains the AWS Batch JobId you can use to track progress of the workflow.  To do this, you can use the Jobs view in the AWS Batch Console.

You can also simplify the command by wrapping it in a bash script that gathers key information automatically.

```bash
#!/bin/bash

# Helper script for submitting nextflow workflows to Batch-squared architecture
# Workflows are submitted to the first "highpriority" Batch Job Queue found in
# in the default AWS region configured for the user.
#
# Usage:
# submit-workflow.sh WORKFLOW_NAME (file://OVERRIDES_JSON | (CONTAINER_ARGS ...))
#
# Examples:
# submit-workflow.sh hello file://hello.overrides.json
# submit-workflow.sh hello hello

WORKFLOW_NAME=$1  # custom name for workflow
shift
PARAMS=("$@")     # args or path to json file for job overrides, e.g. file://path/to/overrides.json

# assume that the default region is set as a global environment variable
# alternatively get it using `aws configure get default.region`
if [ -z "$AWS_REGION" ]; then
    AWS_REGION=`aws configure get default.region`
fi

if [[ ${PARAMS[0]} == file://* ]]; then
    # user provided a file path to an overrides json
    # shift remaining args
    OVERRIDES=${PARAMS[0]}
    shift
else
    # construct a comma separated list for shorthand overrides notation
    OVERRIDES=$(printf "'%s'," ${PARAMS[@]})
    OVERRIDES="command=${OVERRIDES%,}"
fi

# get the name of the high-priority queue
HIGHPRIORITY_JOB_QUEUE=$(aws --region $AWS_REGION batch describe-job-queues | jq -r .jobQueues[].jobQueueName | grep highpriority)

if [ -z "$HIGHPRIORITY_JOB_QUEUE" ]; then
    echo "no highpriority job queue found"
    exit 1
fi

# submits nextflow workflow to AWS Batch
# command is printed to stdout for debugging
COMMAND=$(cat <<EOF
aws batch submit-job \
    --region $AWS_REGION \
    --job-definition nextflow \
    --job-name nf-workflow-${WORKFLOW_NAME} \
    --job-queue ${HIGHPRIORITY_JOB_QUEUE} \
    --container-overrides ${OVERRIDES}
EOF
)

echo $COMMAND
${COMMAND}
```

Output from this script would look like this:

```bash
cd ~/environment/nextflow-workshop
./submit-workflow.sh hello hello

# aws batch submit-job --region us-west-2 --job-definition nextflow --job-name nf-workflow-hello --job-queue highpriority-45e553b0-c840-11e9-bb02-02c3ece5f9fa --container-overrides command='hello'
# {
#     "jobName": "nf-workflow-hello", 
#     "jobId": "ecb9a154-92a9-4b9f-99d5-f3071acb7768"
# }
```

Try submitting `rnatoy` and `blast-example` workflows to the Batch-squared architecture.

#### Getting workflow logs

AWS Batch jobs automatically record logs to CloudWatch Logs.  What is recorded depends on the container called by the job, but generally this is anything that is sent to STDOUT.  These logs are helpful if you want to check on the runtime details of your workflow or inspect error messages if any of the processes in your workflow fails.

To get the logs for an entire workflow:

1. Go to the AWS Batch Console
2. Click on "Jobs"
3. Select "highpriority" in the Queue dropdown
4. Select either "running", "succeeded", or "failed" for the job status table
5. Click on the Job ID of the workflow job you are interested in
6. Scroll to the bottom of the sidebar that opens and click on the link under the "CloudWatch logs" section.  This will open a new tab with the CloudWatch Logstream for the workflow.

!!! note
    To get the logs for a specific job in a workflow, you repeat the above steps, but look for jobs in the "default" job queue instead, as this is where `nextflow` will submit jobs for workflow processes (unless specifically overriden).

### Run a realistic demo workflow

Here is the source code for a demo workflow that converts FASTQ files
to VCF using bwa-mem, samtools, and bcftools.  It uses a public data
set that has been trimmed and only calls variants on chromosome 21 so
that the workflow completes in about 5-10 minutes.

[https://github.com/wleepang/demo-genomics-workflow-nextflow](https://github.com/wleepang/demo-genomics-workflow-nextflow)

To submit this workflow you can use the script you created above:

```bash
cd ~/environment/nextflow-workshop
./submit-workflow.sh demo \
  madhyastha/gatk-nextflow-sample
```

You can check the status of the workflow via the following command line where you replace "JOBID" with the "jobId" output from above:

```bash
aws batch describe-jobs --jobs JOBID --query 'jobs[0].status'
```

#### Resuming a workflow

If your workflow stops midway through, you can restart it from where you left off with the `-resume` flag.  This is useful when developing a workflow, allowing you to "cache" the results of long running steps.

```bash
cd ~/environment/nextflow-workshop
./submit-workflow.sh demo \
  madhyastha/gatk-workflow-sample -resume (<session-name>|<session-id>)
```

You can get the session-name or session-id from the logs for a previous run of the workflow.  The "resumed" workflow should complete in only a few seconds.

### Run an NF-Core workflow

There are many example workflows available via [NF-Core](https://nf-co.re).  These are workflows that are developed using best practices from the Nextflow community.  They are also good starting points to run common analyses such as ATACSeq or RNASeq.

The steps below runs the nf-core/rnaseq workflow against data from the 1000 Genomes dataset.

You can do this directly from the command line with:

```bash
cd ~/environment/nextflow-workshop
./submit-workflow.sh rnaseq \
  nf-core/rnaseq \
    --reads 's3://pwyming-demo-data/secondary-analysis/fastq/demo/NIST7035_R{1,2}_trim_samp-0p1.fastq.gz' \
    --genome GRCh37 \
    --skip_qc
```

There are many parameters for this workflow, and setting all via the command line can be cumbersome.  For more complex configurations, it is best to package the container overrides into a JSON file.  To do this for the above workflow configuration:

Create a json file called `rnaseq.parameters.json` with the following contents:

```bash
cd ~/environment/nextflow-workshop
cat <<EOF > rnaseq.parameters.json
{
    "command": [
      "nf-core/rnaseq",
      "--reads", "s3://pwyming-demo-data/secondary-analysis/fastq/demo/NIST7035_R{1,2}_trim_samp-0p1.fastq.gz",
      "--genome", "GRCh37",
      "--skip_qc"
    ]
}
EOF
```

Submit the workflow using:

```bash
cd ~/environment/nextflow-workshop
./submit-workflow.sh rnaseq file://rnaseq.parameters.json
```

This workflow takes approximately 20min to complete.

Try running several instances of this workflow to see how AWS Batch scales:

```bash
cd ~/environment/nextflow-workshop
for ((i=0;i<20;i++)); do ./submit-workflow.sh rnaseq-${i} file://rnaseq.parameters.json; done
```

## Finished!

If you've reached this point without issue, CONGRATULATIONS! You have successfully run several Nextflow workflows with scalable, AWS Batch-Squared, architecture!
