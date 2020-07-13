+++
title = "e. Autoscaling the ParallelCluster (READING)"
date = 2019-09-18T10:46:30-04:00
weight = 80
tags = ["tutorial", "create", "ParallelCluster"]
+++

Often you will put much more work in the queue than can fit all at
once in the cluster at its initial size.  If you wish, you can allow
the cluster to adjust its size accordingly: “adding” more compute
nodes when there are lots of jobs, and “terminating” compute nodes
when they are idle.  We call that “autoscaling”.

You can use the commands `qstat` and `qhost` to see this in action.  A few minutes after creating a “scaling event” (i.e. adding the new jobs to the queue), the EC2 console will show new instances being created.  (If not, wait a little while longer.). Checking the `qhost` command again you’ll see the new hosts appear in the cluster.  

The autoscaling mechanism can only operate between the minimum and
maximum boundaries that we’ve set.  That is, we cannot add more than 8
nodes.  If you’re familiar with AWS, there is an AutoScaling Group
(ASG) at work behind the scenes. It is very common to use a minimum of
0 nodes, so that all compute nodes disappear and the cost of the
cluster goes to a minimum when there is no work left in the job queue;
you still keep (and pay for) the head node until you delete the
cluster.  The total cost of your work will be in the volume of core
hours used, so it isn’t more expensive to run all the members of a
statistical ensemble (e.g. weather forecasting) or high-throughput
case all at the same time by allowing the cluster to autoscale.





