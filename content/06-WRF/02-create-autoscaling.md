+++
title = "a. Create an autoscaling cluster wtih preloaded HPC applications."
date = 2019-09-18T10:46:30-04:00 
weight = 30 
tags = ["tutorial", "configure", "initialize", "ParallelCluster"]
+++

{{% notice info %}}
For this lab we assume that you have an AWS Cloud9 IDE ready and are familiar with AWS ParallelCluster. If this is not the case, please run through the first half of *Getting Started with AWS* lab.
{{% /notice %}}

To return to the AWS Cloud9 instance, click on the AWS logo in the upper left corner. Search for, and select, Cloud9 from the console search bar. Click on "open IDE" for the Cloud9 instance set up previously and wait for the IDE to open. This can take a few moments as AWS Cloud9 will "stop" and "restart" the instance so that the user does not pay compute charges when no longer using the Cloud9 IDE.


#### Create an autoscaling cluster with preloaded HPC applications.

We’re going to run the WRF model today – the most widely used code for weather forecasting.  We’re going to need a cluster again, but this time we’re going to design it differently.


{{% notice tip %}}
Your current cluster config is listed here:
https://aws-pearc2020.org/03-hpc-aws-parallelcluster-workshop/04-configure-pc.html 
And you’re going to want to have the documentation at hand for a list of all available options:
https://docs.aws.amazon.com/parallelcluster/latest/ug/configuration.html 
Not all options are interesting, but try to look up one or two that you’re curious about.
For more details about the configuration options of AWS ParallelCluster please review the [user guide](https://docs.aws.amazon.com/parallelcluster/latest/ug/what-is-aws-parallelcluster.html).
{{% /notice %}}

In your Cloud9 editor please try to make the following changes to the config.  (But go and check against the provided solution before you actually create the new cluster.)
1. allow a minimum of 0 and a maximum of 8 compute nodes, with 2 initial compute nodes
2. use the SGE scheduler
3. disable hyperthreading
4. use Amazon Linux 2 as the base OS (instead of Amazon Linux (1))
5. All instances should be of the “c5.4xlarge” type, a newer generation of servers and CPUs than the c4.xlarge we previously used.  If you’re interested, you can see the hardware specifics at https://aws.amazon.com/ec2/instance-types/c5/ .  Let’s get one painful detail out of the way: cloud providers express compute power in “vCPUs”, which is the same as hyperthreads.  In HPC we typically disable hyperthreading and count physical cores instead.  c5.4xlarge is listed as having 16 vCPUs each, and that equals 8 physical cores.
6. Add a “post_install script”, which is a user provided script that will be executed on all nodes on first boot.  Use this provided script: https://s3-us-west-2.amazonaws.com/kevin-public/cluster-setup-script.sh
7. Make the shared EBS volume 400GB large, and instead of asking for an empty volume, ask for a copy of the provided disk image snap-09b113b79d6a3d2b5


Now please check your solution below.  You could have slight
variations, e.g. if you spell out the default setting of some option.
Please ask an instructor if you’re not 100% sure of something.  (If
you have a mistake, and it causes cluster creation to fail so you have
to start over, you’ll lose 10-15 min. of time.) You’ll notice there’s one section we didn’t tell you about, called
[dcv lab-dcv].  We’ll explain later, but please copy/paste that into
your config file, too.

```bash
cd ~/environment
IFACE=$(curl --silent http://169.254.169.254/latest/meta-data/network/interfaces/macs/)
SUBNET_ID=$(curl --silent http://169.254.169.254/latest/meta-data/network/interfaces/macs/${IFACE}/subnet-id)
VPC_ID=$(curl --silent http://169.254.169.254/latest/meta-data/network/interfaces/macs/${IFACE}/vpc-id)
AZ=$(curl http://169.254.169.254/latest/meta-data/placement/availability-zone)
REGION=${AZ::-1}

cat > my-wrf-config.conf << EOF
[global]
update_check = true
sanity_check = true
cluster_template = default
[aws]

aws_region_name = ${REGION}

[cluster default]
vpc_settings = public
base_os = alinux2
ebs_settings = myebs
scheduler = sge
key_name = lab-3-your-key
#compute_instance_type = c5.xlarge
compute_instance_type = c5.4xlarge
master_instance_type = c5.xlarge
cluster_type = ondemand
placement_group = DYNAMIC
placement = compute
disable_hyperthreading = true 
max_queue_size = 8
initial_queue_size = 2
post_install = https://s3-us-west-2.amazonaws.com/kevin-public/cluster-setup-script.sh
s3_read_write_resource = *
dcv_settings = lab-dcv

[vpc public]
vpc_id = ${VPC_ID}
master_subnet_id = ${SUBNET_ID}

[ebs myebs]
volume_type = gp2
volume_size = 400
ebs_snapshot_id = snap-07a0ceae2171a4c98

[dcv lab-dcv]
enable = master

[aliases]
ssh = ssh {CFN_USER}@{MASTER_IP} {ARGS}
EOF

```

Now choose a name (here we call it wrfcluster) and endow your cluster with the breath of life: 

```bash
$ pcluster create wrfcluster -c my-wrf-config.conf
```

As before you’ll have to wait 5-10 minutes.  Let’s put that time to
good use with some reading in the next step.

