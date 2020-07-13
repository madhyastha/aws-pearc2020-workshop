+++
title = "a. Install AWS ParallelCluster"
date = 2019-09-18T10:46:30-04:00
weight = 30
tags = ["tutorial", "install", "ParallelCluster"]
+++

{{% notice info %}}
For this lab one will assume that you have an AWS Cloud9 IDE ready. If this is not the case, please run through the first half of *Getting Started with AWS* lab.
{{% /notice %}}

To return to the AWS Cloud9 instance, click on the AWS logo in the upper left corner. Search for, and select, Cloud9 from the console search bar. Click on "open IDE" for the Cloud9 instance set up previously and wait for the IDE to open. This can take a few moments as AWS Cloud9 will "stop" and "restart" the instance so that the user does not pay compute charges when no longer using the Cloud9 IDE.

#### Install AWS ParallelCluster

In this section, AWS ParallelCluster is installed on the AWS Cloud9 instance. Python and PIP (Python package management tool) are already installed in the Cloud9 environment. Use the pip install command to install AWS ParallelCluster:

```bash
pip install aws-parallelcluster -U --user
```
