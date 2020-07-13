---
title: "AWS ParallelCluster"
date: 2019-01-24T09:05:54Z
weight: 30
pre: "<b>III ⁃ </b>"
tags: ["HPC", "Overview"]
---

![hpc_logo](/images/hpc-aws-parallelcluster-workshop/aws-parallelclusterlogo.png)

[AWS ParallelCluster](https://aws.amazon.com/hpc/parallelcluster/) is an AWS-supported open source cluster management tool that helps you to deploy and manage High Performance Computing (HPC) clusters in the AWS Cloud. Built on the open source CfnCluster project, AWS ParallelCluster enables you to quickly build an HPC compute environment in AWS. It automatically sets up the required compute resources and shared filesystem. You can use AWS ParallelCluster with a variety of batch schedulers, such as AWS Batch, SGE, Torque, and Slurm. AWS ParallelCluster facilitates quick start proof of concept deployments and production deployments. You can also build higher level workflows, such as a genomics portal that automates an entire DNA sequencing workflow, on top of AWS ParallelCluster.

{{% notice info %}}
For this lab one will assume that you have an AWS Cloud9 IDE ready. If this is not the case, please run through the first half of *Getting Started with AWS* lab.
{{% /notice %}}

In this lab we will introduce you to [AWS ParallelCluster](https://aws.amazon.com/hpc/parallelcluster/) and how to run your HPC jobs in AWS as you would on-premises. The steps we will be going through are the following:

- Install and configure ParallelCluster on your AWS Cloud9 IDE.
- Create your first cluster.
- Submit a sample job and check what is happening in the background.
- Delete the cluster.
