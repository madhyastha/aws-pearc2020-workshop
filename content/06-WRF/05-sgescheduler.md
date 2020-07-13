+++
title = "d. The SGE scheduler (READING)"
date = 2019-09-18T10:46:30-04:00
weight = 80
tags = ["tutorial", "create", "ParallelCluster"]
+++

The SGE scheduler is managed by the user. Useful SGE commands include `qhost` and `qstat`. Use the Linux SGE man pages to learn more about the SGE scheduler.

`qstat`: The qstat command gives information on the submitted jobs, including the state of the job. Initially the job will be in the “qw” state (queue, wait). This is followed by the “r” state (run). The job id number is provided. The task is removed from the queue when a job is completed.

```bash
[ec2-user@ip-172-31-40-93 ~]$ qstat
state submit/start at	queue	slots job-ID  prior	name	user			ja-task-ID					
--------------------------------------------------------------------------------------------------------
1 0.55500 hpcg.qsub	ec2-user	r	12/08/2018 00:49:37 all.q@ip-172-31-45-182.us-west	4
```

`qhost`: The qhost command lists the compute node status and id’s. Note that the ip address can be used to log onto the compute nodes.

```bash
[ec2-user@ip-172-31-40-93 ~]$ qhost
NCPU NSOC NCOR NTHR  LOAD  MEMTOT  MEMUSE  SWAPTO  SWAPUS HOSTNAME	ARCH	
----------------------------------------------------------------------------------------------

global	                 -	    -	-	-	-	   -	   -	     -      -	-
ip-172-31-44-191	lx-amd64	2	1	2   2	0.24	7.4G	328.6M 0.0	0.0
ip-172-31-45-182	lx-amd64	2	1	2	2	0.22	7.4G	328.7M	0.0	0.0
```

`qdel`: You can use `qdel` to delete a job from the queue.






