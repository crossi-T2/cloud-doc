How to export a Terradue's Developer Cloud Sandbox to AWS EC2
=============================================================

Intended audience
-----------------

Anyone has the technical background to interact with *nix systems, BASH language, command line interfaces (CLI) and common network communication protocols.

Prerequisites
-------------

* To have root/admin permissions on the Sandbox,
* To have an Amazon Web Services account,
* To have installed the [qemu-img](http://wiki.qemu.org/Main_Page) tool for manipulating disk images.

Step 1. Preparation of the Sandbox
----------------------------------

This step prepares your Sandbox in terms of OS configuration. Furthermore it makes the OS and the Application disks a single one disk (i.e. it copies all the content of the */application* mount point in the */* mount point). Before to start, please make sure that:

- The */application* contains only the needed stuff to be executed in Cluster mode (i.e. it is clean), 
- The */* has enough space to contain all the data of the */application* (If not, please contact the Operations Support  Team at Terradue).

If the previous facts are verified, proceed with the following steps:

* Login into the Sandbox,
* Copy the content of the folder *resources/scripts* in a directory you prefer,
* Type:
```bash
$ sh ./resources/scripts/ciop-sandbox-prepare.sh
```

Step 2. Snapshot of the OS disk
-------------------------------

* Login into the Cloud Controller,
* Make an **Hot** snapshot of the OS disk.

![Snaphsot of the OS disk](resources/images/ccb_hot_snapshot.png "Snaphsot of the OS disk")

Step 3. Restore of the Sandbox
------------------------------

This step reverts the changes done after a successful *Step 1*. It is important if you want to use the Sandbox as Development environment after the snapshot done at *Step 2*.

* Login into the Sandbox,
* Copy the content of the folder *resources/scripts* in a directory you prefer,
* Type:
```bash
$ sh ./resources/scripts/ciop-sandbox-restore.sh
```

Step 4. Converting the disk before the upload
---------------------------------------------

* Download the disk from the Cloud Controller (or from a location made available from the Operations Support Team at Terradue),
* Convert the disk in a format suitable for ec2-import-instance tool:

```bash
$ qemu-img convert -O vmdk <source-disk> <destination-disk-type0>
$ vmware-vdiskmanager -r <destination-disk-type0> -t 5 <destination-disk-type5>
```

Step 5. Upload of the disk on AWS
---------------------------------

* From your client machine, perform the following command:

```bash
$ ec2-import-instance -o <bucket-owner-access-key> -w <bucket-owner-secret-key> -f vmdk <vmdk_disk_type_5> -b <bucket_name> --region <region_name> -t <instance_type> -s <disk size> -a x86_64 -p Linux 
```

References
----------

1. [AWS VM Import/Export](http://aws.amazon.com/ec2/vm-import/)
2. [AWS VM Import/Export for Linux](http://aws.amazon.com/blogs/aws/vm-import-export-for-linux/?utm_source=feedburner&utm_medium=feed&utm_campaign=Feed%3A+AmazonWebServicesBlog+(Amazon+Web+Services+Blog))
