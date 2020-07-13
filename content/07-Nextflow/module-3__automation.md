+++
title = "d. Automation"
date = 2019-09-18T10:46:30-04:00 
weight = 30 
tags = ["tutorial", "configure", "initialize", "Nextflow"]
+++


In [Module 1](./module-1__running-nextflow.html) and [Module 2](./module-2__aws-resources.html) you created the infrastructure you needed to run Nextflow on AWS from scratch.  If you need to reproduce these resources in another account or create multiple versions in the same account, you don't need to do so by hand each time.

All of what you created can be automated using AWS Cloudformation which allows you to describe your infrastructure as code using Cloudformation templates.

You can use the CloudFormation templates available at the link below to setup this infrastructure in your own environment.

[Genomics Workflows on AWS - Nextflow Full Stack](https://docs.opendata.aws/genomics-workflows/orchestration/nextflow/nextflow-overview/#full-stack-deployment)

The source code is also open source and [available on Github](https://github.com/aws-samples/aws-genomics-workflows/) - so you can customize the architecture to suit special use cases.  Also, if you have suggestions on how to improve the infrastructure feel free to submit a pull request!
