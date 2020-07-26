+++
title = "d. Access AWS Cloud9"
weight = 70
tags = ["tutorial", "cloud9", "ParallelCluster"]
+++
![Cloud 9](/images/hpc-aws-parallelcluster-workshop/cloud9.png)
[AWS Cloud9](https://aws.amazon.com/cloud9/) is a cloud-based integrated development environment (IDE) that lets you write, run, and debug your code with just a browser. We will be using it to introduce you to the AWS Command Line Interface (CLI) without the need to install any software on your laptop.

AWS Cloud9 contains a collection of tools that let you code, build, run, test, debug, and release software in the Cloud using your internet browser. The IDE offers support for python, pip, the AWS CLI and provides easy access to AWS resources through the IAM user credentials. With the IDE comes with a terminal that includes sudo privileges to the managed  instance that is hosting your development environment and a pre-authenticated CLI. This makes it easy for you to quickly run commands and directly access AWS services.

{{% notice info %}}
After accessing your IDE, please take a moment to get familiar with the Cloud9 environment. You can even take a [quick tour](https://docs.aws.amazon.com/cloud9/latest/user-guide/tutorial.html#tutorial-tour-ide) of Cloud9 if you'd like or even watch [this video](https://www.youtube.com/watch?v=JDHZOGMMkj8).
{{% /notice %}}

#### Create Your Own AWS Cloud9 Instance {#section-2}

You will follow this guide if running this workshop on your own or if this the option selected by the organizers. To access your IDE you will need to go through the following steps.

1. Use the AWS Console search bar by clicking on **Services** on the top banner to locate **Cloud9** and use the **search field** to find it.
![Cloud 9](/images/introductory-steps/cloud9-find.png)
2. Click on  **Create Environment**
3. Name your environment with **MyDevEnv** and click **Next Step**
![Cloud 9](/images/introductory-steps/cloud9-name.png)
4. Use the default settings on the *Configure Settings* page unless told otherwise and click **Next Step**
![Cloud 9](/images/introductory-steps/cloud9-defaults.png)
5. Click on **Create Environment**
6. You will see a review page.

Your AWS Cloud9 instance will be ready in a few minutes!

![Cloud9 Create](/images/introductory-steps/cloud9-create.png)
