+++
title = "b. So how does one preload applications?"
date = 2019-09-18T10:46:30-04:00
weight = 70
tags = ["tutorial", "create", "ParallelCluster"]
+++

Brave soul, one day you may have to start from scratch, install some compilers on a cluster, import the source code and any dependencies, and compile it all.  This can be a lot of work, so it’s best to save the results for the next time you start a new cluster.  First ask yourself, are you the kind of person who will have to install applications for yourself or others?  Or will someone else do it for you?  If the latter, well done; go have a nice cup of coffee while the cluster is created.  If the former, here are various ideas, and you can reflect on what would work best for you:

-	Pack your applications and dependencies in a disk image (“EBS snapshot” in AWS parlance) and save that in your AWS account.  Use that disk image to create a shared disk volume for your next cluster.  You can version disk images, share them with other AWS accounts, and store them indefinitely (e.g. with all the work pertaining to a paper you published).
-	Make a tarball containing everything you need, and store this in Amazon S3.  Again you can version, archive, and share.  When you create a new cluster, you can write a postinstall script that pulls the tarball from S3, copies it to the cluster, and unpacks it.  (Why the tarball?  S3 is an object store and does not conserve Linux file permissions – so protect them inside the tarball.). Copying to/from S3 is fast.  The postinstall script can do additional useful things, like downloading today’s meteorological observations to initialize your forecast.
-	Store it all on a Lustre file system with your HPC cluster.  (We won’t do this today, but something you should look into if your applications need Lustre on-premise.  E.g. https://www.hpcworkshops.com/04-amazon-fsx-for-lustre.html ). The Lustre FS can persist after you delete your cluster, and then you can attach it to a new cluster.  Or you can back up the Lustre FS to an S3 bucket (cheap and safe), and restore it to a new Lustre FS next time you need it.  (Why pay for Lustre storage while you’re on summer break?)
-	Use package managers to pull a fresh copy of your application when you start a new cluster.  For example, Spack is very popular today.  You can add Spack instructions to your postinstall script, or do it manually from the cluster headnode.  See e.g. https://jiaweizhuang.github.io/blog/aws-hpc-guide/ for my favorite tutorial.  If you need to be on the bleeding edge with a dev branch of some package (perhaps as part of a CI/CD system), then you can do a git pull in the postinstall script and compile from there.
-	You can save everything inside a virtual machine image (we call that an “AMI”) that you’ll use for the nodes of your next cluster.  This is a more advanced option and less recommended because you now become responsible for patching the OS, updating drivers, etc.  It’s much easier to always use the latest AMI provided by default by the AWS ParallelCluster service.  But if you have to (for example, because regulations require you to harden your machine images) then we can show you how.
-	Any other ideas?

Task:  
Now that you’ve thought about this, go take a look at the postinstall script for this exercise and try to get a sense of what it does.   In Cloud9 do the following:

```bash
$ wget https://s3-us-west-2.amazonaws.com/kevin-public/cluster-setup-script.sh
```
A couple points we’d like you to notice:

- There’s syntax to only do things on the head node (or compute nodes) – look for “if $MASTER”.
- The script pulls several tar balls from S3 and unpacks them.  It then performs a few housekeeping tasks like writing job scripts, and fixing file permissions.
- The script installs some packages using yum install.
-	The script updates limits.conf.





