+++
title = "e. Start with the AWS CLI (Optional)"
weight = 80
tags = ["tutorial", "cloud9", "aws cli", "s3"]
+++

Your AWS Cloud9 Environment should be ready. We will now get familiar with it, introduce you the AWS CLI then create an Amazon S3 bucket with the AWS CLI. This bucket will be used at the next stage.

#### Your AWS Cloud9 IDE

The AWS Cloud9 IDE is similar to a traditional IDE you can find on virtually any system. It is composed of a file browser, listing the files located on your instances. A series of tabs with *opened files* is located at the top and *terminal tabs* at the bottom. Tabs can be maximized to the internet browser windows size if necessary.

![Cloud9 First Use](/images/introductory-steps/cloud9-first-use.png)

### Update the AWS CLI and Check Existing Amazon EC2 Instances

Before going to the next steps we will update the AWS CLI. AWS Cloud9 should be coming with the latest version but it it always a good practice to verify you are at the latest version.. The [AWS CLI](https://aws.amazon.com/cli/) allow you to manage services using the command line and, should you want it, control them through scripts. Most users will conduct some level of automation with the AWS CLI.

{{% notice tip %}}
Each code section contains a copy button. Use it to your advantage to quickly copy the command(s) to your clipboard.
{{% /notice %}}


1. Go to a Terminal page and type the command below to update the AWS CLI should update itself if necessary. You will see a warning message asking you to upgrade PIP, we won't bother to address it.
```bash
pip-3.6 install awscli -U --user
```

2. Then use the commands below to display the general AWS CLI help, the help related to Amazon EC2 commands, the list of your existing instances with their key characteristics and the list of your registered SSH key-pairs. Type **q** to exit the help pages.
```bash
aws help
```
```bash
aws ec2 help
```
```bash
aws ec2 describe-instances
```
```bash
aws ec2 describe-key-pairs
```

Next we will use the AWS CLI to interact with S3.

{{% notice info %}}
If you are seeing a message telling you to upgrade *PIP*, just ignore it.
{{% /notice %}}
