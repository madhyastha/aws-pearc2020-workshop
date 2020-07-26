+++
title = "c. Connect to the new cluster"
date = 2019-09-18T10:46:30-04:00
weight = 80
tags = ["tutorial", "create", "ParallelCluster"]
+++

Once it’s ready, log into the new ParallelCluster:

```bash
pcluster ssh wrfcluster -i ~/.ssh/lab-3-key
```

The EC2 instance asks for confirmation of the ssh login the first time you log onto the instance. Answer with “yes”. 
![SSH cluster](/images/hpc-aws-parallelcluster-workshop/ec2-ssh-connect.png)

{{% notice tip %}}
**pcluster ssh** is a wrapper around SSH. You can also log into your head-node using ssh and the public or private IP address depending on the case. 
{{% /notice %}}

If you have forgotten the unique cluster name, it can be retrieved with:

```bash
$ pcluster list
```

Once inside the ls command will reveal files added by the post_install
script:

```bash
[ec2-user@ip-172-31-40-92 ~]$ ls
Desktop  FEFFruns  JFEFF  JFEFF.desktop  jfeff_examples
```

And the WRF runtime environment provided by the EBS disk image:
```bash
[ec2-user@ip-172-31-40-92 ~]$ ls /shared
bin         jasper-1.900.1  netcdf-4.1.3  WPS_GEOG        WRFV3.7-mpi-openmp
feff9       libpng          netcdf-4.4.1  wrf             zlib
fftw        libpng-1.2.50   packages      WRF-benchmarks  zlib-1.2.7
fftw-3.3.4  libpng-1.6.23   wps           WRFV3.7         zlib-1.2.8
jasper      netcdf          WPS-3.7       WRFV3.7-mpi
```

All of this will be visible to the compute nodes:
```bash
[ec2-user@ip-172-31-28-77 ~]$ showmount -e
Export list for ip-172-31-28-77:
/opt/sge   172.31.0.0/16
/opt/intel 172.31.0.0/16
/home      172.31.0.0/16
/shared    172.31.0.0/16
```




