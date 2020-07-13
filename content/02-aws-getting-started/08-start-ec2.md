+++
title = "g. Create an EC2 Instance (Optional)"
weight = 100
tags = ["tutorial", "cloud9", "aws cli", "ec2", "s3"]
+++

Now that you are getting familiar with the AWS Console and AWS CLI we will raise the bar and do the following: create an SSH key-pair on you AWS Cloud9 instance, create an instance then access it.

#### Generate an SSH Key-pair

SSH is [commonly](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) used to connect to Amazon EC2 instances. To allow you to connect to your instances we will generate a key-pair using the AWS CLI on your AWS Cloud9 instance as shown below. We will use the key name *mykey* but feel free to choose yours.

```bash
aws ec2 create-key-pair --key-name lab-2-your-key --query KeyMaterial --output text > ~/.ssh/id_rsa
chmod 600 ~/.ssh/id_rsa
```

If you like, you can check if your key is registered using the command:

```bash
aws ec2 describe-key-pairs
```

#### Create a New Amazon EC2 Instance

We will now create a new EC2 instance but first we will need to check into which Virtual Private Cloud (or VPC) we will place it. We will select the same VPC as our AWS Cloud9 instance and ideally the same subnet.

1. Grab the Subnet ID and VPC ID from the Cloud 9 instance, we'll use this in the next step to launch an instance:

```bash
MAC=$(curl -s http://169.254.169.254/latest/meta-data/network/interfaces/macs/)
cat << EOF
***********************************************************************************
Subnet ID = $(curl -s http://169.254.169.254/latest/meta-data/network/interfaces/macs/$MAC/subnet-id)
VPC ID = $(curl -s http://169.254.169.254/latest/meta-data/network/interfaces/macs/$MAC/vpc-id)
************************************************************************************
EOF
```
2. Go to the Amazon EC2 Dashboard then to the **Instances** section
3. Click on the blue **Launch Instance** button.
![EC2 Dashboard](/images/introductory-steps/ec2-details.png)
4. Select the Amazon Linux 2 AMI and click **Select**.
5. Choose a *t2.micro* instance then click **Next: Configure Instance Details**.
6. On the *Network* section select the VPC ID and same Subnet ID from your AWS Cloud9 Instance.
7. click on **Next: Add Storage** and leave the *Storage* section as is, click **Next: Add Tags**.
8. Click **Add Tag** then as a **Key** input **Name** (literally, not your name!). As *Value* add **[Your Name]'s Instance** or any significant name. This will be displayed as the name of your instance. Then click on **Next: Configure Security Groups**.
![EC2 Tags](/images/introductory-steps/ec2-tags.png)
9. Select the tick **Create a new security group**, change the Security Group name if you want. The type should be *ssh*, protocol *TCP*, port range *22* and the source *0.0.0.0/0*. Click **Review and Launch**. Ignore the warnings should you see any.
10. You will see a review page with the instance specifications. Click on **Launch** then select the **lab-2-your-key** key-pair created earlier.
![EC2 Tags](/images/introductory-steps/ec2-key.png)

Your instance is being launched and ready in a few seconds, go to the *Instances* section of the *EC2 Dashboard* to check its state.

#### Connect to Your Instance

{{% notice warning %}}
**Do you have issues to connect to your instance?** Access the AWS Console and the [EC2 Dashboard](https://console.aws.amazon.com/ec2). Select your instance and review its details.
{{% /notice %}}

Once the instance is running, go back to your AWS Cloud9 Environment, access a terminal and go through the following steps:

1. List running instances and display their names, type, private IP address and public IP address. Here the information will be filtered to only keep certain details (hence the complex command). The same information will be displayed on the [EC2 Dashboard](https://console.aws.amazon.com/ec2).
```bash
aws ec2 describe-instances --query 'Reservations[*].Instances[*].[Tags[?Key==`Name`]| [0].Value,InstanceType, PrivateIpAddress, PublicIpAddress]' --filters Name=instance-state-name,Values=running --output table
```
2. Connect to your instances with *SSH* using either the public or private **IP address** and the username **ec2-user** which is the default use for Amazon Linux. Type **yes** when asked if you want to connect to the instance.
```bash
# don't forget to use your OWN ip address
# keep the username to ec2-user as is, don't use your name!
ssh ec2-user@10.0.1.6
```
3. Ping the internet to test the outbound connectivity.
```bash
ping www.wikipedia.org
```

{{% notice warning %}}
Do not forget to change the IP address of the instance you are trying to connect to! This must be yours not the one you are seeing above!
{{% /notice %}}

You now have an functional instance that can communicate with the outside world! Go to the next section to see what else we can do.

{{% notice warning %}}
If you cannot connect to your instances, please check that the security groups are set correctly. If needed, go to the *EC2 Dashboard*, *Instances*, select your instances and review the *inbound* and *outbound* rules. If you need to modify the security groups, select your instance, click on **Actions**, then select **Networking** then click on **Security Groups**. In addition, you may want to check that you are using the correct key-pair.
{{% /notice %}}

![EC2 SSH](/images/introductory-steps/ec2-ssh.png)
