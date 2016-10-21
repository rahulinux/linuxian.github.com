---
ID: 122
post_title: >
  Detecting new disks in Linux without
  reboot
author: Rahul Patil
post_date: 2012-08-16 12:54:51
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/08/16/detecting-new-disks-in-linux/
published: true
dsq_thread_id:
  - "2079698654"
---
When you have new HDD/LUNs created on the SAN fabric, zoned &amp; mapped it to the server, how can you detect the HDD,luns on the linux server online, without rebooting it?.

When you dynamically add new disks to a Linux VM running on ESX server, how do you detect that disks on the Linux virtual machine?.

Here are the steps to do that :

1. Install sg3_utils and lsscsi package.

(For Ubuntu Install <code>apt-get install scsitools</code>)

[sourcecode language="bash"]
[root@station01 ~]# yum install –y sg3_utils lsscsi
[/sourcecode]

2. The “lsscsi” command will list the disks attached to it. If you have just attached a disk, you will not be able to see it. You can also list this using “fdisk –l”

[sourcecode language="bash"]
[root@station01 ~]# lsscsi
[0:0:0:0]    disk    VMware   Virtual disk     1.0   /dev/sda
[root@fedora01 ~]#
[/sourcecode]

As you can see above, I currently have one disk connected to the system. To scan for a new device I just added, we should run rescan-scsi-bus.sh from the host.

3. Run the command “/usr/bin/rescan-scsi-bus.sh” , to dynamically detect and activate the new disk.

[sourcecode language="bash"]
[root@station01 ~]# /usr/bin/rescan-scsi-bus.sh -l
Host adapter 0 (mptspi) found.
Scanning SCSI subsystem for new devices
Scanning host 0 for  SCSI target IDs  0 1 2 3 4 5 6 7, LUNs  0 1 2 3 4 5 6 7
Scanning for device 0 0 0 0 ...
OLD: Host: scsi0 Channel: 00 Id: 00 Lun: 00
Vendor: VMware,  Model: VMware Virtual S Rev: 1.0
Type:   Direct-Access                    ANSI SCSI revision: 02
Scanning for device 0 0 1 0 ...
OLD: Host: scsi0 Channel: 00 Id: 01 Lun: 00
Vendor: VMware,  Model: VMware Virtual S Rev: 1.0
Type:   Direct-Access                    ANSI SCSI revision: 02
Scanning for device 0 0 2 0 ...
NEW: Host: scsi0 Channel: 00 Id: 02 Lun: 00
Vendor: VMware,  Model: VMware Virtual S Rev: 1.0
Type:   Direct-Access                    ANSI SCSI revision: 02
1 new device(s) found.
0 device(s) removed.
[/sourcecode]

&nbsp;

[sourcecode language="bash"]
[root@station01 ~]# lsscsi
[0:0:0:0]    disk    VMware   Virtual disk     1.0   /dev/sda
[0:0:1:0]    disk    VMware   Virtual disk     1.0   /dev/sdb
[root@station01 ~]#
[/sourcecode]

You see the new disk is visible. Now you can create a partition or filesystem on it.

After running those commands, check dmesg and /var/log/messages to see if there are any device detections. You can also do "fdisk -l" or "cat /proc/scsi/scsi" to see the attached LUNs. This works fine in RHEL5, CentOS5.