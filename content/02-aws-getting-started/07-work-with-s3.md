+++
title = "f. Using Amazon S3 (Optional)"
weight = 90
tags = ["tutorial", "cloud9", "s3"]
+++

Now that we have access to the AWS CLI, we will use it to create an S3 bucket then upload a file to this bucket. While those steps could be done using the AWS Console, they will give you a good idea of what can be done with the AWS CLI. In addition if interested, you can find more details about S3 on the related [documentation page](https://docs.aws.amazon.com/s3/index.html).

{{% notice info %}}
Every bucket must have a unique name. If this is not the case Amazon S3 will **not allow you** to create one.
{{% /notice %}}

1. Go to an AWS Cloud9 terminal and list existing buckets with the command below. You should see several to no buckets.
```bash
aws s3 ls
```
2. Make an Amazon S3 Bucket with the command shown below. Please keep in mind that bucket names should be unique so either use a random prefix or postfix or append your name. The bucket name must start by *s3://*.
```bash
BUCKET_POSTFIX=$(uuidgen --random | cut -d'-' -f1)
aws s3 mb s3://bucket-${BUCKET_POSTFIX}

cat << EOF
***** Take Note of Your Bucket Name *****
Bucket Name = bucket-${BUCKET_POSTFIX}
*****************************************
EOF


```
3. Download a file from the internet to your AWS Cloud9 instance, for example lets retrieve [a synthetic subsurface model](https://wiki.seg.org/wiki/SEG_C3_45_shot) generally used to test Seismic Imaging algorithms. The file will be downloaded on your AWS Cloud9 Instance, not your computer.
```bash
wget http://s3.amazonaws.com/open.source.geoscience/open_data/seg_eage_salt/SEG_C3NA_Velocity.sgy
```
<!-- I tested both the aws s3 cp and wget versions. Results are as follows: cp = 9 sec, wget = 7 sec -->

4. Upload the file to your Amazon S3 bucket using
```bash
aws s3 cp ./SEG_C3NA_Velocity.sgy s3://bucket-${BUCKET_POSTFIX}/SEG_C3NA_Velocity.sgy
```
5. List the content of your bucket using the command below, alternatively you can use the AWS Console and select [the S3 Dashboard](https://console.aws.amazon.com/s3/) the go into your newly created bucket to see the file.
```bash
aws s3 ls s3://bucket-${BUCKET_POSTFIX}/
```
6. Once done, you can delete the local version of the file using the command *rm* or the AWS Cloud9 IDE interface.
```bash
rm SEG_C3NA_Velocity.sgy
```
See the process in Cloud9 (click on the image to increase its size).
![Cloud9 AWS CLI](/images/introductory-steps/cloud9-aws-cli.png)

And the result in the AWS Console:
![Cloud9 S3 AWS Console](/images/introductory-steps/cloud9-s3.png)


{{% notice warning %}}
Please keep note of your bucket name as it has been randomly generated, see the [Amazon S3 Dashboard](https://s3.console.aws.amazon.com/s3/home) if you are unsure in the AWS console
{{% /notice %}}
