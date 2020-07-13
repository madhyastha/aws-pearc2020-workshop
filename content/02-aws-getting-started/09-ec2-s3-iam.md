+++
title = "h. IAM and Roles (Optional)"
weight = 110
tags = ["tutorial", "cloud9", "aws cli", "ec2", "iam"]
+++

{{% notice info %}}
**You will encounter an issue now!** But that's normal and we will explain why.
{{% /notice %}}

{{% notice warning %}}
Access to resources by users and services is controlled by [Identity and Access Management](https://aws.amazon.com/iam/) (IAM). For example, IAM permissions can be added to a policy then a role which can be attached to a user user, group role (admins, devops) or a service (Amazon EC2 to access Amazon S3, Amazon Lambda to access Amazon SQS). For an in depth introduction, please review the [IAM Documentation](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html.)
{{% /notice %}}

Now that you created an EC2 instance and logged into it using SSH, we will get instance to access the Amazon S3 bucket created previously.

```bash
aws s3 ls s3://bucket-${BUCKET_POSTFIX}/
```

![EC2 SSH](/images/introductory-steps/ec2-iam-deny.png)

**It appears that your instances has not been granted permission to access your Amazon S3 storage.** In the present, your AWS Cloud9 has been [granted permissions](https://docs.aws.amazon.com/cloud9/latest/user-guide/credentials.html) to some services. However, your new Amazon EC2 instance has not been given any permissions and we will now grant it access to S3.


#### Create an IAM Role for Amazon EC2

We will now create a role so your Amazon EC2 instance can access your S3 bucket.

1. On the AWS Console, click on **Services** on the top bar. **Search** and open the **IAM Dashboard**, click on **Roles** on the left side of the Dashboard, then click on the blue **Create Role** button.
![IAM Dashboard](/images/introductory-steps/iam-dashboard.png)
2. Select **AWS Service** in *Select type of trusted entity*, then chose **EC2** in *Choose the service that will use this role*, then click on **Next: Permissions**.
3. In the search field input *S3* and **click on the box next** to the policy **AmazonS3FullAccess** to provide full Amazon S3 access for Amazon EC2 instance. Then click **Next: Tags**.
4. Leave the tags as is then click **Next: Review**.
5. Input a *Role Name* such as **S3FullAccessForEC2** then click **Create Role**.

Your role is now created. Search for your role **S3FullAccessForEC2**. Click on its name to take a detailed look at the new role and policy. Take a first look at the *Trust Relationships* tab and you will see that Amazon EC2 is one of the trusted policies (meaning it can use this role).

![IAM Dashboard](/images/introductory-steps/iam-trust.png)

The take a look at the **Permissions** tab and expand the **AmazonS3FullAccess** policy then select **{}JSON**. You should see the permissions below. These are quite open as they allow your Amazon EC2 instances to conduct any action on Amazon S3. However, we could also restrict these permissions for only some actions, such as List or Put, to be conducted on a particular Amazon S3 bucket.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:*",
            "Resource": "*"
        }
    ]
}
```

However, we could also restrict these permissions for only some actions, such as List or Put, to be conducted on a particular Amazon S3 bucket. For example:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:Get*",
                "s3:List*"
            ],
            "Resource": [
                "arn:aws:s3:::bucket-myname",
                "arn:aws:s3:::bucket-myname/*"
            ]
        }
    ]
}
```
{{% notice info %}}
Please note that full access to Amazon S3 is fine in the context of this workshop but fine grained control is highly recommended for anything else than temporary sandbox testing.
{{% /notice %}}

IAM is a great way to control who and what can access to which resources at a fine level of granularity. Please see [this page](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_testing-policies.html) If you are want to know more about IAM policies and the IAM Policy Simulator.

#### Attach the IAM Role to an Amazon EC2 Instance

Now that you have created a new IAM role, we will assign it to our EC2 instance:

1. Go to the *EC2 Dashboard* then *Instances*.
2. Select your instance, then click on **Actions**, select **Instance Settings** and **Attach/Replace IAM Role**.
3. Select the newly created IAM Role, click **Apply**
4. Your role should be attached and visible on your EC2 instance details and it is now allowed to access S3.
![EC2 IAM Role](/images/introductory-steps/ec2-role.png)
5. Go back to your AWS Cloud9 IDE, connect back to the instance with SSH and run the following commands (don't forget to change the bucket name to yours!). This will list your Amazon S3 bucket content then download the file downloaded previously.
```bash
aws s3 ls s3://bucket-${BUCKET_POSTFIX}/
```
```bash
aws s3 cp s3://bucket-${BUCKET_POSTFIX}/SEG_C3NA_Velocity.sgy .
```
```bash
ls -l
```

If everything went right you should see the a result similar to the one shown in the image below.
![EC2 IAM Role](/images/introductory-steps/ec2-s3-ls.png)
