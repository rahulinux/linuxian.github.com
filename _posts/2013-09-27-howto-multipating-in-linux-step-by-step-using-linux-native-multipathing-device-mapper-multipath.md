---
ID: 434
post_title: 'Howto Multipating in linux step-by-step using linux-native-multipathing [device-mapper-multipath]'
author: Rahul Patil
post_date: 2013-09-27 11:57:23
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2013/09/27/howto-multipating-in-linux-step-by-step-using-linux-native-multipathing-device-mapper-multipath/
published: true
dsq_thread_id:
  - "2079496943"
---
<h2><strong>Information</strong></h2>
Preamble: The procedure described within this article is only supported on CentOs5/RHEL5 level and higher. Earlier releases may not work as expected.
<h2><strong>Introduction</strong></h2>
We are going to use Openfiler Storage . it is an Open Source Network Attached Storage and Storage Area Network Solution. you can easily install Openfiler, refer <a href="http://www.openfiler.com/learn/how-to/graphical-installation">this page</a>.  Native Multipathing using Device-mapper package which available in CentOs, RHEL Suse. DM MPIO features automatic configuration of the MPIO subsystem for a large variety of setups. Active/passive or active/active (with round-robin load balancing) configurations of up to 8 paths to each device are supported.
<h3><strong>Requirements</strong></h3>
1. Openfiler Storage
2. CentOs5.x / RHEL5.x
3. Assign multiple VMware virtual disk to the Openfiler host and two NICs for multipath
<h3><strong>Scenario</strong></h3>
<h3>On Storage Side:</h3>
<h3>We will Create a LUN, will assign for two hosts using ISCSI Target,</h3>
<h3>On Server Side:
We will  install required packages and scan LUN, then we will use that use ISCSI initiator and Multipathing.</h3>
<h2><strong>Getting Started</strong></h2>
After installing Openfiler, Add extra SCSI HDD in your Openfiler and run using root user below command:

[sourcecode lang="bash"]rescan-scsi-bus.sh[/sourcecode]

Above command will scan new HDD in your storage then it will show in fdisk -l

Storage or Openfiler can accessible via Below URL will give login screen is as above.

<a href="http://linuxian.com/wp-content/uploads/2013/12/URL1.png"><img class="aligncenter size-full wp-image-1056" alt="URL1" src="http://linuxian.com/wp-content/uploads/2013/12/URL1.png" width="186" height="20" /></a>.

Username :- openfiler

Password :- password

<strong>Step 1# Creating Volumes</strong>
<pre></pre>

<strong><a href="http://linuxian.com/wp-content/uploads/2013/12/2012-11-07-14_37_22-Volumes-_-Volume-Groups-Nightly.png"><img class="aligncenter size-full wp-image-1000" alt="2012-11-07-14_37_22-Volumes-_-Volume-Groups-Nightly" src="http://linuxian.com/wp-content/uploads/2013/12/2012-11-07-14_37_22-Volumes-_-Volume-Groups-Nightly.png" width="1091" height="415" /></a></strong>

<pre></pre>
<strong>Step 2# Block Device</strong>

<a href="http://linuxian.com/wp-content/uploads/2013/12/2012-11-07-19_55_42-Volumes-_-Block-Devices-Nightly.png"><img class="aligncenter size-full wp-image-1001" alt="2012-11-07-19_55_42-Volumes-_-Block-Devices-Nightly" src="http://linuxian.com/wp-content/uploads/2013/12/2012-11-07-19_55_42-Volumes-_-Block-Devices-Nightly.png" width="524" height="141" /></a>

..

.

.

.

.

.

.

.
<pre></pre>

<strong>Step 3# Creating Partition</strong>

<a href="http://linuxian.com/wp-content/uploads/2013/12/2012-11-07-20_04_56-Volumes-_-Block-Devices-_-Edit-Partitions-Nightly.png"><img class="aligncenter size-full wp-image-1004" alt="2012-11-07-20_04_56-Volumes-_-Block-Devices-_-Edit-Partitions-Nightly" src="http://linuxian.com/wp-content/uploads/2013/12/2012-11-07-20_04_56-Volumes-_-Block-Devices-_-Edit-Partitions-Nightly.png" width="768" height="343" /></a>

<pre></pre>
<strong>Step 4# Add Volumes</strong>

<a href="http://linuxian.com/wp-content/uploads/2013/12/2012-11-07-20_10_10-Volumes-_-Volume-Groups-Nightly.png"><img class="aligncenter size-full wp-image-1005" alt="2012-11-07-20_10_10-Volumes-_-Volume-Groups-Nightly" src="http://linuxian.com/wp-content/uploads/2013/12/2012-11-07-20_10_10-Volumes-_-Volume-Groups-Nightly.png" width="518" height="350" /></a>.

.

.

..

.

..

.

.

.

.

.

.

.
<pre></pre>

<strong>Step 5# Select ISCSI Target and Click on Add as below ( In case this option don't allow to add iqn  then make sure Iscsci-target and other <a href="http://linuxian.com/wp-content/uploads/2013/12/2012-11-07-20_46_50-Volumes-_-iSCSI-Targets-Nightly.png"><img class="aligncenter size-full wp-image-1006" alt="2012-11-07-20_46_50-Volumes-_-iSCSI-Targets-Nightly" src="http://linuxian.com/wp-content/uploads/2013/12/2012-11-07-20_46_50-Volumes-_-iSCSI-Targets-Nightly.png" width="692" height="165" /></a>iscsci services are enabled in Services tab. ) </strong> <strong></strong>

<pre></pre>
<strong>Step 6# Click on LUN Mapping
</strong>

<a href="http://linuxian.com/wp-content/uploads/2013/12/2012-11-07-20_53_07-Volumes-_-iSCSI-Targets-Nightly.png"><img class="aligncenter size-full wp-image-1007" alt="2012-11-07-20_53_07-Volumes-_-iSCSI-Targets-Nightly" src="http://linuxian.com/wp-content/uploads/2013/12/2012-11-07-20_53_07-Volumes-_-iSCSI-Targets-Nightly.png" width="866" height="165" /></a>
<pre></pre>

<strong>Step 7# Map LUN</strong>

<a href="http://linuxian.com/wp-content/uploads/2013/12/2012-11-07-20_56_11-Volumes-_-iSCSI-Targets-Nightly.png"><img class="aligncenter size-full wp-image-1008" alt="2012-11-07-20_56_11-Volumes-_-iSCSI-Targets-Nightly" src="http://linuxian.com/wp-content/uploads/2013/12/2012-11-07-20_56_11-Volumes-_-iSCSI-Targets-Nightly.png" width="682" height="121" /></a>

<pre></pre>
<strong>Step 8# Click on System Tab in Top Section</strong> <strong>and Add Network ACL as Below</strong>

<a href="http://linuxian.com/wp-content/uploads/2013/12/2012-11-07-21_01_36-System-_-Network-Setup-Nightly.png"><img class="aligncenter size-full wp-image-1009" alt="2012-11-07-21_01_36-System-_-Network-Setup-Nightly" src="http://linuxian.com/wp-content/uploads/2013/12/2012-11-07-21_01_36-System-_-Network-Setup-Nightly.png" width="587" height="207" /></a>

If you want to add more HDD then, then use different iqn mapping on each LUN, so that you will have uniq WWID number for each LUN.

Hmm..  Storage part is finish.. now let ‘s move to client side

<strong>On Client side</strong>

First make sure <strong>IPTables</strong> and<strong> SELinux</strong> Service Should be disabled.

Install Required Packages

[crayon lang="sh"]

# yum install iscsi-initiator-utils device-mapper-multipath  sg3_utils

[/crayon]

Now Scan LUN using

[crayon lang="sh"]

# iscsiadm -m discovery -t sendtargets -p IPAddrOfStorage

[/crayon]

It will be showing 2 LUN, because storage has 2 NIC

Now restart iscsi service, then it will login to iSCSI.

[crayon lang="sh"]

# /etc/init.d/iscsid restart

# /etc/init.d/iscsi restart

[/crayon]

Now Get WWID using following command, it’s required to configure multipath

[crayon lang="sh"]

[root@node1 ~]# ls  /dev/disk/by-id/scsi*| awk -F"scsi-" '{print $2}'

14f504e46494c45525979514e4f442d7557335a2d746d3130

[/crayon]

Now Copy WWID number and configure/etc/multipath.conf as below, it’s just for alias the data,

[crayon lang="sh"]

# Append following lines to label

multipaths {

multipath {

no_path_retry         fail

wwid                  14f504e46494c45525979514e4f442d7557335a2d746d3130

alias                 data

}

[/crayon]

After configure once restart multipath service

[crayon lang="sh"]

# /etc/init.d/multipathd restart

[/crayon]

Now you will able to see output of multipath –ll as below

[crayon lang="sh"]

[root@node1 ~]# multipath -ll

data (14f504e46494c45525979514e4f442d7557335a2d746d3130) dm-2 OPNFILER,VIRTUAL-DISK

[size=4.99G][features=0][hwhandler=0][rw]

_ round-robin 0 [prio=1][active]

_ 9:0:0:0  sda 8:0   [active][ready]

_ round-robin 0 [prio=1][enabled]

_ 10:0:0:0 sdb 8:16  [active][ready]

[/crayon]

Now you can use "<strong>/dev/mapper/data</strong>" , just make a filesystem on it and mount it.

[crayon lang="sh"]

# mkfs.ext3 /dev/mapper/data

# mount /dev/mapper/data  /mnt

# df -h

[/crayon]