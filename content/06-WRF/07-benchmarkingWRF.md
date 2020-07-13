+++
title = "f. Benchmarking WRF on the ParallelCluster"
date = 2019-09-18T10:46:30-04:00
weight = 110
tags = ["tutorial", "AMI", "ParallelCluster"]
+++

People often want to benchmark their HPC application.  The benchmark tells us how long it takes to finish a given job on the chosen compute platform.  This is useful for a few reasons.  First, you can compare different platforms that way.  For example, maybe the AWS cluster is 20% faster than your old on-premise cluster.  Second, since on AWS you pay exactly for the time you use the EC2 instances, the benchmark also tells you the cost of each simulation.  For example, if each instance costs $1 per hour, and your job takes 1.5 hours on 8 instances, then the cost per job is roughly $1 x 1.5 x 8 = $12.  (“Roughly” because there is also the cost of the head node and some (usually very minor) costs related to storage etc.)  Finally, during the benchmark you typically try out several configurations, and the results allow you to choose the configuration that works best for you.  

To do a benchmark you repeat the same job on clusters of increasing
size and see how much faster it gets.  For example: 1 node: 8 minutes,
2 nodes: 4 minutes, 4 nodes: 2:10 minutes,
8 nodes: 1:12 minutes.  

Normally it gets faster as you use more cores and nodes – up to a point.  But you’ll already notice from this made-up example that the speedup isn’t completely linear.  There are several reasons for this, including Amdahl’s Law (every application contains non-parallellizable code that limits the theoretically achievable speedup) and infrastructure limitations (the speed of light, network latency, memory access etc. - keep speedup below the theoretical limit).  

If you are doing this in your own account for a serious benchmark, you’d want to get the largest possible server size (with one VM using the entire physical node).  So, you’d use the 18xlarge “t-shirt size”.  If you revisit https://aws.amazon.com/ec2/instance-types/c5/, you’ll see that these servers have 36 cores each (72 vCPUs) and 100 Gbps network bandwidth.  For example, if you also set the maximum cluster size to 30 nodes, you’d have just over 1,000 cores to play with.  The fact that the cores are grouped on a small number of large servers cuts down on network communications.  The best benchmark results we’ve shown you in the presentation today were obtained with

```bash
compute_instance_type = c5n.18xlarge
```

However, we’re using special “demo accounts” today that only allow us to use up to 4xlarge.  (Sorry.). That’s why we asked you to use in your cluster config:

```bash
compute_instance_type = c5.4xlarge
```

These have 8 physical cores each, so an 8-node cluster will give you up to 64 cores to play with.  These medium-size c5.4xlarge instances will eventually be overwhelmed on a tightly-coupled parallel MPI benchmark, as there will be lots of out-of-node communications and each node will have lowish bandwidth available.  But they will do for a quick exercise.

We will use a provided job script “wrfjob.sh” to submit each WRF job.  You will need to edit this script to specify the correct number of cores to use for the job.  Please check out appendix 3.9 for the solution before submitting the job.  After submitting the first job, you’ll have to wait a couple minutes to get compute nodes.  It will go faster after that.  The qhost command will reflect how many nodes are ready to use at any given time.  

Task:

```bash
[ec2-user@ip-172-31-28-77 ~]$ cd /shared/WRF-benchmarks/CONUS12km
```

This directory contains a weather forecast.   We will use it to benchmark the WRF weather model on our cluster.  Adapt the job file to run the forecast multiple times:
- Run on 8 nodes (edit wrfjob.sh ; qsub wrfjob.sh)
- Run on 4 nodes (edit wrfjob.sh ; qsub wrfjob.sh)
- Run on 2 nodes (edit wrfjob.sh ; qsub wrfjob.sh)
- Run on 1 node (edit wrfjob.sh ; qsub wrfjob.sh)


Note that you won’t be able to submit all jobs at once to the scheduler, unless you make copies in separate subdirectories.  Otherwise the jobs will run on top of each other reading and writing the same files!  (The scheduler isn’t *that* clever.). You’re welcome to work in a group and divide the work.  The whole sequence can be done in about ~10 minutes.  You can do “tail rsl.error.0000” to see the job work its way through the time steps.

(For a small job like this, I’d normally do a 1-core run as well, and use that as the calibration point of the scaling curve.  But it would take ~20 min, so you probably don’t want to wait for it just now.)


Each time, write down the runtime it took to complete the compute.  The output should tell you this as “compute 87.34252s” or so.  Make a table or plot of the result, with the number of cores on the x-axis, and 1 over the runtime on the y-axis.  This is what’s called a “speedup curve”.  It tells you how much the application speeds up as you increase the number of nodes.  (See example below – your results will look different.)

![submit WRF](/images/wrf-workshop/wrfjob.png)


Each time, write down the runtime it took to complete the compute.
The output should tell you this as “compute 87.34252s” or so.  Make a
table or plot of the result, with the number of cores on the x-axis,
and 1 over the runtime on the y-axis.  This is what’s called a
“speedup curve”.  It tells you how much the application speeds up as
you increase the number of nodes.  (See example below – your results
will look different.) Orange is the speedup vs the number of
cores. Blue is the turnaround time vs the number of cores.

![scaling curve](/images/wrf-workshop/scalingcurve.png)



If you are doing this in an account where you can use c5.18xlarge or c5n.18xlarge, you can go to the “../CONUS2.5km” directory instead.  This was the official benchmark for the “V3” series of WRF, so it’s a little more interesting.  You’ll want to run this on hundreds to thousands of cores.  There is an even newer benchmark for the “V4” series of WRF, which we’ve run on AWS up to 15,000 cores (and it can probably go bigger than that.)

While you wait for these jobs to complete, you can read ahead and work on “cost” and “visualization”.  (Or have another coffee.)

Cost: How would you calculate cost?  Most of the cost will be that of the EC2 instances that make up our cluster.  That cost is metered by the second for how long you use the instance.  (There can be a cost for storage elements, too, and many additional line items that are usually insignificant.).

Task: 

Look up the cost of the EC2 instances you used on the AWS website.
Look for the “on-demand” and “spot instances” price.  (Spot will be
much cheaper.).  Now change the plot or table, and instead plot cost
in $ on the x-axis and time on the y-axis.  This is the same
information as in the previous graph, but it shifts the attention away
from infrastructure to real-life decisions.  “How fast do I want this
result, and how much am I willing to spend to get it that fast?”

For example, what point on this graph would you choose to do research
for a conference in the fall, and what point would you choose for
forecasts where human lives depend on it?  (Like knowing when the wind
is about to change direction and push a forest fire towards
firefighters.)  For example (your results will look different):

![cost vs turnaroundtime](/images/wrf-workshop/costvsturnaroundtime.png)

A couple pro tips for the dedicated reader:
-	Our test is a very small weather forecast, and it doesn’t have much predictive power for normal-size or large weather forecasts.  Always try to run a benchmark that’s representative of what you’re interested in.  Don’t stretch tiny benchmarks out to huge clusters: there’s just “not enough meat on the bone” and it’s not a very relevant exercise.
-	For serious work with a tightly coupled application you’d use the “18xlarge” size and never the “4xlarge” or “xlarge” size.
-	We could improve the performance of this test substantially above what you produced today.  For example, you would find better performance if you combine MPI and OpenMP.  (Use OMP_NUM_THREADS=9 and use 4 MPI tasks per c5.18xlarge instance.)
-	The “c5” is a previous-generation compute server.  For the most large-scale, tightly-coupled HPC applications we’d use the newest “c5n” server type, which has newer CPUs and an HPC-specific instance interconnect that provides a significant jump in scalability.  We could also use a Lustre high performance file system if the problem is I/O bound.

Visualization:

Task:  

Let’s end on a cheerful note with a pretty picture.  Let’s visualize the weather forecast on a map.
First, let’s take advantage of the “DCV” virtual desktop that you can run from the head node of your cluster.  Type

```bash
pcluster dcv connect mycluster -k ~/.ssh/lab-3-key
```

On most computers this will open your default browser.  You’ll likely be faced with a warning about a self-signed certificate that can’t be verified.  If you trust this tutorial, you can accept to visit the website.  This will open a virtual desktop.  Let’s open a Terminal window on the head node

![terminalheadnode](/images/wrf-workshop/terminalheadnode.png)

Install a package for visualizing netcdf files (including the output file of the WRF simulation):

```bash
sudo yum install ncview
```

Plot the WRF output file in the `ncview` application

```bash
ncview wrfrst_d01_2001_10_25_00_00_00 wrfrswwafdsadft_d01_2001_10_25_00_00_00
```
For example, select the 2D-variable “UST”.  You’ll see something similar to this:

![ncview](/images/wrf-workshop/ncview.png)

You can play around and visualize any variable you like.

Let’s imagine for a moment that you’re actually interested in these
weather forecasts (or is that just me?!).  You probably want to save
the results for later analysis or sharing with collaborators.  The
compute cluster is ephemeral in AWS: you’ll soon delete it.  The data
can live forever – in Amazon S3.  (The local storage on the cluster
will get deleted, too.)

Task: 

Tar and zip up all the output in one big tar file.  Copy it to a
sensible S3 location.  Also, download it to your laptop. (And then
promptly delete it, if I really can’t convince you to care!)

(You could also create an updated version of the EBS disk image (“EBS
snapshot”).  Can you think of how you would do that?  You could do it
in the AWS web console in the browser; or using aws cli commands from
the terminal.)


