+++
title = "h. Terminate Your Cluster"
date = 2019-09-18T10:46:30-04:00
weight = 110
tags = ["tutorial", "create", "ParallelCluster"]
+++

{{% notice note %}}
Note: If you plan on working on the next labs then you will want to clean up the ParallelCluster configuration created during that one. Files stored in the bucket and on cloud9 will incur small charges (less than a dollar a month if the tutorial was followed as written.) The cloud9 IDE will stop (not terminate) 30 minutes after the web tab is closed and can be started again, through the console, when needed.
{{% /notice %}}

Now that we are done with our cluster we can delete it using the command below from your AWS Cloud9 terminal.

```bash
pcluster delete hpclab-yourname
```

The cluster and all its resources will be deleted by CloudFormation. If you like you can check the [CloudFormation Dashboard](https://console.aws.amazon.com) in your AWS Console to how the deletion processes is conducted.
