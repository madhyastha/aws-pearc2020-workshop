+++
title = "h. Terminate Your Cluster"
date = 2019-09-18T10:46:30-04:00
weight = 110
tags = ["tutorial", "create", "ParallelCluster"]
+++


Now that you are  done with the cluster you can delete it using the command below from your AWS Cloud9 terminal.

```bash
pcluster delete hpclab-yourname
```

The cluster and all its resources will be deleted by CloudFormation. If you like you can check the [CloudFormation Dashboard](https://console.aws.amazon.com) in your AWS Console to how the deletion processes is conducted.
