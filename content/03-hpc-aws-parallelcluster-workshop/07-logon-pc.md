+++
title = "e. Log in to Your Cluster"
date = 2019-09-18T10:46:30-04:00
weight = 80
tags = ["tutorial", "create", "ParallelCluster"]
+++

{{% notice tip %}}
**pcluster ssh** is a wrapper around SSH. You can also log into your head-node using ssh and the public or private IP address depending on the case.
{{% /notice %}}

Existing clusters can be listed using the command below. It is a convenient way to find the name of a cluster in case your forget it.

```bash
pcluster list --color
```

Now that your cluster has been created you can log into the head-node using the following command in your AWS Cloud9 terminal:

```bash
pcluster ssh hpclab-yourname -i ~/.ssh/lab-3-key
```

The EC2 instance asks for confirmation of the ssh login the first time you log onto the instance. Answer with “yes”.
![SSH cluster](/images/hpc-aws-parallelcluster-workshop/ec2-ssh-connect.png)

#### Getting to Know your Cluster

Now that you are connected to the head-node you may want to familiarize yourself with your cluster structure by running the set of commands below.

##### SLURM

{{% notice tip %}}
[SLURM](https://slurm.schedmd.com) from SchedMD is one of the batch schedulers that you can use in AWS ParallelCluster. You will find an overview of the SLURM commands in this [quickstart](https://slurm.schedmd.com/quickstart.html). The commands we will run now are the following
{{% /notice %}}

- List existing partitions and nodes per partition. You should see two nodes if your run this command after creating your cluster and zero nodes if running it 10 minutes after creation (default cooldown period for AWS ParallelCluster, you don't pay for what you don't use).
```bash
sinfo
```
- List jobs in the queues or running. Obviously there won't be any since we did not submit anything...yet!
```bash
squeue
```

##### Module Environment

[Lmod](https://lmod.readthedocs.io/en/latest/) is a fairly standard tool used to dynamically change your environment.

- List available modules with:
```bash
module av
```
- Load a particular module. In this case this will load IntelMPI in your environment and check the path of *mpirun*.
```bash
module load intelmpi
which mpirun
```

##### NFS Shares

A few volumes are shared by the head-node and will be mounted on compute instances when they boot up. You can check the list of shares using the command below. As you will see both */shared* and */home* will be accessible by all nodes.
```bash
showmount -e localhost
```

Next, we will run our first job!
