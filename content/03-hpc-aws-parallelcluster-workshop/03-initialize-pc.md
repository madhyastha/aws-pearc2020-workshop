+++
title = "b. Initialization"
date = 2019-09-18T10:46:30-04:00
weight = 40
tags = ["tutorial", "initialize", "ParallelCluster"]
+++


To configure AWS ParallelCluster, you would normally use the command
[**pcluster
configure**](https://docs.aws.amazon.com/parallelcluster/latest/ug/getting-started-configuring-parallelcluster.html)
in a terminal and provide the requested information such as the AWS
Region, Scheduler and EC2 Instance Type. This would create for you a file called `~/.parallelcluster/config` with default cluster settings. **However, today we will cut right to the chase by creating a specific configuation file to include HPC specific options.**

The commands below generate a new keypair and query the EC2 metadata to get the Subnet ID and VPC ID. We will use these variables and set them again in scripts throughout the lab. 

```bash
# generate a new key-pair if it doesn't already exist
[ -f ~/.ssh/lab-3-key ] || aws ec2 create-key-pair --key-name lab-3-your-key --query KeyMaterial --output text > ~/.ssh/lab-3-key
chmod 600 ~/.ssh/lab-3-key

IFACE=$(curl --silent http://169.254.169.254/latest/meta-data/network/interfaces/macs/)
SUBNET_ID=$(curl --silent http://169.254.169.254/latest/meta-data/network/interfaces/macs/${IFACE}/subnet-id)
VPC_ID=$(curl --silent http://169.254.169.254/latest/meta-data/network/interfaces/macs/${IFACE}/vpc-id)

```

Now that we have set these variables, we build a 
configuration to generate an optimized cluster to run typical "tightly
coupled" HPC applications. 
