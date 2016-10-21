---
ID: 757
post_title: 'How to mount new partition &#8220;/var/log/&#8221; without reboot'
author: Rahul Patil
post_date: 2013-09-22 15:00:49
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2013/09/22/how-to-mount-new-partition-varlog-without-reboot/
published: true
dsq_thread_id:
  - "2078922394"
---
<blockquote>How to mount new partition "/var/log/" without reboot</blockquote>
This question is asked in <a href="http://askubuntu.com/questions/346493/can-you-mount-new-partition-to-var-log-without-rebooting/346579#346579">one of forum </a>, where user want to mount without reboot, I'm posting here because I want to explore more...

Whenever you provisioning new server, you should take care of partition which are going to be increase in future (it's a part of Capacity Planning ). typically partitions "/var" and "/home/" should be in <a href="http://en.wikipedia.org/wiki/Logical_Volume_Manager_%28Linux%29">LVM</a>, because LVM allows you to re-size your disk partitions easily as needed. If your Partitions is in LVM then, we can extended partition on the fly without interrupting any services.

So this article on non-LVM partition, If your partition is in non-LVM ( here it's "/var/" )  then what's steps should be perform. If your non-LVM Partition is slash "/" and you want to increase this then you need to boot your <a href="http://www.sysresccd.org/SystemRescueCd_Homepage">Rescuce </a>mode and do the required changes .

<strong>Here is Steps for non-LVM Partition ( "/var/log/" )</strong>

<strong>Step 1</strong>

We check what processes/deamons are using <code>/var/log/</code> and stop them , so we can use :

[crayon lang="shell" ]lsof +D /var/log | awk '!/COMMAND/{print $1 | "sort -u"}' [/crayon]

In my case returns

[crayon lang="shell" ]apache2
monit
rsyslogd
[/crayon]

So I just stopped those services until <code>lsof</code>'s output was blank

<strong>Step 2</strong>

Then we need to have the same directory structure with respective permissions, so we can use <code>rsync</code>:

[crayon lang="shell" ]
mkdir /var/oldlog rsync -a --include '*/' --exclude '*' /var/log/ /var/oldlog/
[/crayon]

<strong>Step 3</strong>

Mount your new partition and copy the directory structure onto the new partition

[crayon lang="shell" ]
mount /dev/sdX /var/log/ rsync -a --include '*/' --exclude '*' /var/oldlog/ /var/log/
[/crayon]

Now start the services that you have stopped and <code>tail</code> the logs, if everything seems to be fine then do not forget to make en entry in <code>/etc/fstab</code>

Note : These steps should be fast enough so that it should not drop logs, so you can write a script based on above steps and check it and run.