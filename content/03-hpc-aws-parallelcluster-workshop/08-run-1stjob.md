+++
title = "f. Submit your first HPC job"
date = 2019-09-18T10:46:30-04:00
weight = 90
tags = ["tutorial", "create", "ParallelCluster"]
+++

{{% notice note %}}
The steps here can also be executed on any cluster running SLURM. There may be some variations depending on your configuration but that is just to share how generic this procedure is. Except that you are doing it in the cloud.
{{% /notice %}}

We will be running your first *"hello world"* job to introduce your to the mechanisms of AWS ParallelCluster. We will have to go through some preparatory steps before.

#### Preparatory Steps

{{% notice info %}}
Please ensure that you need to go run the following commands on your AWS Cloud9 terminal and logged in the head-node.
{{% /notice %}}

##### Creating our Hello World Application

Let's first build and compile our MPI *hello world* application. Go back to your AWS Cloud9 terminal then run the commands below to create and build our *hello world* binary.

```bash
cat > mpi_hello_world.c << EOF
#include <stdio.h>
#include <stdlib.h>
#include <mpi.h>
#include <unistd.h>

int main(int argc, char **argv){
  int step, node, hostlen;
  char hostname[256];
  hostlen = 255;

  MPI_Init(&argc,&argv);
  MPI_Comm_rank(MPI_COMM_WORLD, &node);
  MPI_Get_processor_name(hostname, &hostlen);

  for (step = 1; step < 5; step++) {
    printf("Hello World from Step %d on Node %d, (%s)\n", step, node, hostname);
    sleep(2);
  }

 MPI_Finalize();
}
EOF

module load intelmpi
mpicc mpi_hello_world.c -o mpi_hello_world
```

If you like you can test your application locally on the head-node for testing.

```bash
mpirun -n 4 ./mpi_hello_world
```

Your application is compiled and ready to be executed. Let's build a batch submission script to submit it to SLURM.

##### Create a Submission Script

We will create submission script that will 4 MPI processes and generate an output file for STDOUT.

```bash
cat > submission_script.sbatch << EOF
#!/bin/bash
#SBATCH --job-name=hello-world-job
#SBATCH --ntasks=4
#SBATCH --output=%x_%j.out

mpirun ./mpi_hello_world
EOF
```

Once done let's submit our fist job!

#### Submit your First Job

Submitted jobs will be immediately processed if not job is in the queue and a sufficient number of compute nodes exist.


However, what happens if no or not enough compute nodes can satisfy the computational requirements of the job such as the number of cores? Well, in this case AWS ParallelCluster will create new instances to satisfy the requirements of the jobs sitting in the queue. But remember that your minimum and maximum number of nodes were determined when creating the cluster. Not enough? No worries, we can update our cluster later to change those parameters.

So let's submit our first job as follows when on the head-node.

```bash
sbatch submission_script.sbatch
```

Then check the status of the queue using the command **squeue**. The job will be first marked as pending (*PD* state) because resources are being created (or in a down/drained state). If you check the [EC2 Dashboard](https://console.aws.amazon.com/ec2) nodes should be booting up. When ready and registered your job will be processed and you will see a similar status as below.

```bash
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
    2   compute hello-wo ec2-user  R       0:05      1 ip-10-0-1-154
```

We can also check the number of nodes available in your cluster **sinfo**. Do not hesitate to refresh it, nodes will generally take less than 1 min to appear. In the case shown below we have one node.

```bash

sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
compute*     up   infinite      1  alloc ip-10-0-1-154
```

Once the job has been processed you should see a similar results as here in the *.out* file:

```text
Hello World from Step 1 on Node 1 (ip-10-0-1-154)
Hello World from Step 1 on Node 2 (ip-10-0-1-154)
Hello World from Step 1 on Node 3 (ip-10-0-1-154)
Hello World from Step 1 on Node 0 (ip-10-0-1-154)
...
Hello World from Step 4 on Node 1 (ip-10-0-1-154)
Hello World from Step 4 on Node 2 (ip-10-0-1-154)
Hello World from Step 4 on Node 0 (ip-10-0-1-154)
Hello World from Step 4 on Node 3 (ip-10-0-1-154)
```

Voila, it is easy, isn't it !

After a few minutes, your cluster will scale down unless there are more job to process. We will now take a look at what is really happening behind the curtain.
