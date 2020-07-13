+++
title = "d. Build an HPC Cluster"
date = 2019-09-18T10:46:30-04:00
weight = 70
tags = ["tutorial", "create", "ParallelCluster"]
+++

Now we will create a cluster based on the specifications defined in the configuration file. To create a cluster we will use the command [**pcluster create**](https://docs.aws.amazon.com/parallelcluster/latest/ug/pluster.create.html) and the [**--config**](https://docs.aws.amazon.com/parallelcluster/latest/ug/pluster.create.html#pluster.create.namedarg) (or **-c**) option to use another configuration file other than the default one.

{{% notice tip %}}
If you were to create your cluster without using the**--config** (or **-c**) option then AWS ParallelCluster will use the default configuration with the minimum requirements to get a cluster running. For example your head and compute nodes would be *t2.micro* instances instead of *c4.xlarge*.
{{% /notice %}}


Run the following command in your AWS Cloud9 terminal to create a cluster. Ensure that the configuration file path is correct.

```bash
pcluster create hpclab-yourname -c my-cluster-config.conf
```

Your cluster will take a few minutes to be built. The creation status will be displayed on your terminal. Once ready you should see a result similar to the one shown in the image below.
![ParallelCluster Create](/images/hpc-aws-parallelcluster-workshop/pc-create.png)

{{% notice tip %}}
There can be only one cluster of a given name at any time one your account.
{{% /notice %}}


#### What's Happening in the Background

When the **pcluster create** command is executed, AWS ParallelCluster will generate an [AWS CloudFormation](https://aws.amazon.com/cloudformation/) template to generate an infrastructure in AWS. The bulk of the work is done in AWS and once the create is launched you don't need to keep AWS ParallelCluster running. If your are interested to see AWS CloudFormation in action please follow this [link](https://console.aws.amazon.com/cloudformation/). You should see something similar to what is shown in the image below.

![ParallelCluster CloudFormation](/images/hpc-aws-parallelcluster-workshop/pc-cloudformation.png)
