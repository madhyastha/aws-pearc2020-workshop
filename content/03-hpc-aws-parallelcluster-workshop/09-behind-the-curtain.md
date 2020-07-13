+++
title = "g. Behind the Curtain"
date = 2019-09-18T10:46:30-04:00
weight = 100
tags = ["tutorial", "create", "ParallelCluster"]
+++

Let's now look at what really happens in AWS when you submit a job and reveal a bit of the magic behind your auto-scaling computational resources.

At the heart of AWS ParallelCluster exists an [auto-scaling group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html). It is a logical group of instances that can scale up and scale down based on a series of criteria. In the case of AWS ParallelCluster we have three processes controlling the scaling of the cluster. These processes will:

- Check the queue for any pending job and compute the total number of instances needed to process the queued jobs.
- Check if instances are busy over a cooldown period (600 seconds or 10 mins) and emit a heartbeat if they are not doing anything.
- If no jobs are waiting in the queue and some instances are sitting idle then they will be terminated and the auto-scaling group reduced.

More details can be found in the [documentation](https://docs.aws.amazon.com/parallelcluster/latest/ug/processes.html) of AWS ParallelCluster.

Let's check in more details how this works.

#### Auto-scaling Groups

We will now take a look at auto-scaling groups

1. Open the **AWS Console**, select the **EC2 Dashboard**, then go to [**Auto Scaling Groups**](https://console.aws.amazon.com/ec2/autoscaling) at the bottom left.
2. You should see an auto-scaling group with **Desired** at 0, **Min** at 0 and **Max** at 8. The Min and Max value correspond to the *min_queue_size* and *max_queue_size* of your cluster configuration file.
![ParallelCluster Create](/images/hpc-aws-parallelcluster-workshop/pc-auto-scaling.png)
3. Go back to your AWS Cloud9 environment and launch a new job with the commands below. It will just run a wait for 5 minutes.
```bash
cat > sleep_script.sbatch << EOF
#!/bin/bash
#SBATCH --job-name=hello-world-job
#SBATCH --ntasks=2
#SBATCH --output=%x_%j.out

sleep 300
EOF
sbatch sleep_script.sbatch
```
4. Go back to the **EC2 Dashboard** and **Auto Scaling Groups**, refresh the fields with the circling arrows if necessary. You should see that an instance just appeared on the desired field instead of 0. It corresponds to the 2 physical cores or c4.xlarge equivalent that we requested.
![ParallelCluster Create](/images/hpc-aws-parallelcluster-workshop/pc-auto-scaling-2.png)
5. On the **EC2 Dashboard** click on [**Instances**](https://console.aws.amazon.com/ec2/v2) on the left side of the window. You should see your compute instances labeled as *Compute*.
![ParallelCluster Create](/images/hpc-aws-parallelcluster-workshop/pc-ec2-compute.png)

Now you have a better understanding on how AWS ParallelCluster operates. If you are interested, please look at the [documentation](https://docs.aws.amazon.com/parallelcluster/latest/ug/configuration.html) as there is more to explore.
