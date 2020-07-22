+++
title = "b. Initialization"
date = 2019-09-18T10:46:30-04:00
weight = 40
tags = ["tutorial", "initialize", "ParallelCluster"]
+++


Next, you will configure AWS ParallelCluster.

To configure AWS ParallelCluster, you could use the command [**pcluster configure**](https://docs.aws.amazon.com/parallelcluster/latest/ug/getting-started-configuring-parallelcluster.html) in a terminal and provide the requested information such as the AWS Region, Scheduler and EC2 Instance Type. **However, today we will take a shortcut by creating a basic configuration file, then customizing this file to include HPC specific options.**

The commands below generate a new keypair, query the EC2 metadata to get the Subnet ID, VPC ID, and lastly write a config to `~/.parallelcluster/config`. You can always edit this config file to add and change [configuration options](https://docs.aws.amazon.com/parallelcluster/latest/ug/configuration.html).

```bash
# generate a new key-pair if it doesn't already exist
[ -f ~/.ssh/lab-3-key ] || aws ec2 create-key-pair --key-name lab-3-your-key --query KeyMaterial --output text > ~/.ssh/lab-3-key
chmod 600 ~/.ssh/lab-3-key

IFACE=$(curl --silent http://169.254.169.254/latest/meta-data/network/interfaces/macs/)
SUBNET_ID=$(curl --silent http://169.254.169.254/latest/meta-data/network/interfaces/macs/${IFACE}/subnet-id)
VPC_ID=$(curl --silent http://169.254.169.254/latest/meta-data/network/interfaces/macs/${IFACE}/vpc-id)

mkdir -p ~/.parallelcluster
cat > ~/.parallelcluster/config << EOF
[aws]
aws_region_name = us-east-1

[cluster default]
key_name = lab-3-your-key
vpc_settings = public

[vpc public]
vpc_id = ${VPC_ID}
master_subnet_id = ${SUBNET_ID}

[global]
cluster_template = default
update_check = false
sanity_check = true

[aliases]
ssh = ssh {CFN_USER}@{MASTER_IP} {ARGS}
EOF
```

Now check the content of this file using:

```bash
cat ~/.parallelcluster/config
```

You now have a configuration file allowing you to create a simple cluster with the minimum required information. A default configuration file is good to have for testing purposes. Next, we build a configuration to generate an optimized cluster to run typical "tightly coupled" HPC applications.
