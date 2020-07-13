+++
title = "g. Terminate Your Cluster"
date = 2019-09-18T10:46:30-04:00
weight = 110
tags = ["tutorial", "terminate", "ParallelCluster"]
+++

Please remember to save valuable software setups and data before deleting any ephemeral cluster. For example, data can be saved directly to S3; snapshots can be created from EBS volumes, such as of the shared file directory (created by default with AWS ParallelCluster); and Amazon Machine Images, AMI’s, can be made of instance set ups. For this lab, there is no need to save any data.
From a terminal window in the IDE.  List the running ParallelClusters:

```bash
pcluster list
```

Delete the ParallelCluster:

```bash
pcluster delete mycluster
```

Clusters take several minutes to delete.

You still have a Cloud9 instance running.  And probably some files stored in S3.  Will these continue to cost you money if you don’t delete them?


