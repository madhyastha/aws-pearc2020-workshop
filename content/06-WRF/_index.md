---
title: "Running WRF"
date: 2019-01-24T09:05:54Z
weight: 300
pre: "<b>IV ⁃ </b>"
tags: ["WRF", "Overview"]
---

So, we took our first steps in the hpcworkshops.com lab.  You started
your first cluster (using the ParallelCluster tool inside the Cloud9
environment), and we got the “hello world” bit out of the way.  Now we
will teach you some additional skills to get you ready for “real work”
such as running a weather forecast on an AWS cluster.

In the first step we’re going to think about
-  How can I bring my HPC application (e.g. a weather code) onto the cluster?  How can I bring in data?  We will work with “postinstall scripts” and “disk snapshots” and we’ll list a few other ways to consider for your own work.
-  How do I make the cluster automatically adjust its size to the amount of work I give it?  We will use “autoscaling” to vary the number of compute nodes when there are tasks in the queue.

In the second step we will worry about additional issues:
-  How do I test the performance of my HPC job on AWS?  We will do a quick scaling study as an example.
-  How much does my HPC job cost on AWS?

In day-to-day application, it is possible to create many clusters
quickly. In-house you probably have one big cluster that stays the
same for years and is shared by many people.  On AWS you may be
starting and deleting clusters all the time, and customize them to the
needs of one person or one project.  It is common to create one
cluster per application, such as one cluster for LS-Dyna and one
cluster for ANSYS Fluent. Or one for production and one for proof of
concept work.  It’s okay if you have trouble remembering the name of
that cluster you created yesterday, or was it the day before?  The
more flexible you are and make the infrastructure adapt to your
research and operational needs instead of the other way around, the
more you are taking advantage of the Cloud.

{{% notice info %}}
For this lab we  assume that you have an AWS Cloud9 IDE ready. If you do not, please run through the first half of *Getting Started on the Cloud* lab. We also assume that you have gone through the general tutorial on *AWS ParallelCluster* and are familiar with configuring and building a cluster.
{{% /notice %}}

