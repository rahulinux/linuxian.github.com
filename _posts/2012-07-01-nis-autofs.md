---
ID: 75
post_title: NIS + Autofs
author: Rahul Patil
post_date: 2012-07-01 21:24:46
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/07/01/nis-autofs/
published: true
dsq_thread_id:
  - "2521197101"
---
"NIS with Autofs", i faced this question in interview :), so i just practice on the same and posting here.

Required Package :-
ypserv , autofs  , nfs

Make sure the required ypserv, nfs, nfslock, and portmap daemons are both running and configured to start after the next reboot.

Install ypserv using :

[sourcecode language="bash"]
$ yum install ypserv
[/sourcecode]

After installing above packages check :
# then check rpcinfo -p localhost
# portmap and ypserv service should be running in server
# Make sure you put FWs off before doing anything in NIS
# Make sure you start portmap BEFORE you start NFS

configure NIS Domain name
edit "/etc/sysconfig/network" with your faurait editor and just define domain name as below :

[sourcecode language="bash"]
NISDOMAIN=altnix
[/sourcecode]

set NIS  Domain name useing below commnad:

[sourcecode language="bash"]
domainname altnix
[/sourcecode]

Configure /etc/yp.conf and make changes as below :

[sourcecode language="bash"]
altnix server server1.altnix.local
[/sourcecode]

Note: if dont have LAN DNS then add server ip and name “192.168.x.x server1.altnix.local” in /etc/hosts

start services

[sourcecode language="bash"]
$ service ypserv start
$ service yppasswdd restart
[/sourcecode]

check your domain name is properly configured or not using below command out should be domain name which you configured :

[sourcecode language="bash"]
$ domainname
[/sourcecode]

configure user database using below steps :

[sourcecode language="bash"]
$ cd /var/yp/ &amp;&amp; /usr/lib/yp/ypinit -m

Ctrl D
[/sourcecode]

@@ NFS Server Part @@

Configure /etc/exports as below :

[sourcecode language="bash"]
/home *(rw,sync)
[/sourcecode]

and run below command :

[sourcecode language="bash"]
$ exportfs –a
[/sourcecode]

restart required services using :

[sourcecode language="bash"]
$ service portmap restart
$ service nfs restart
[/sourcecode]

Testing : Check if all is ok with

[sourcecode language="bash"]
$ rpcinfo -p
$ showmount –e localhost
[/sourcecode]

@@ Client side settings @@

run below command and select NIS

[sourcecode language="bash"]
authconfig-tui
[/sourcecode]

//select
//enter NIS domain name=altnix;
//hostname=server1.altnix.local

checking folder and user info from NIS server

[sourcecode language="bash"]
$ ypcat passwd | grep username
[/sourcecode]

configure autofs master file in client computer
edit "/etc/auto.master" file with your faurait editor

[sourcecode language="bash"]
/home /etc/auto.my
[/sourcecode]

//do not need create /home/guests directory in advanced
//info of /home/guests, user can use ypcat passwd to find

configure autofs slave file

edit /etc/auto.my file and do below changes :

[sourcecode language="bash"]
* server1.altnix.local:/home/&amp;
[/sourcecode]

restart autofs service and add in startup

[sourcecode language="bash"]
$ service autofs restart
$ chkconfig autofs on
[/sourcecode]

Now just test via login with username and password

if you face any issue , you can post your issue with error in comments :)