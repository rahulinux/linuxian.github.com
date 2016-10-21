---
ID: 1215
post_title: SNMP Client on CentOS/RHEL
author: Rahul Patil
post_date: 2014-01-08 12:05:45
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2014/01/08/snmp-client-centos-5/
published: true
dsq_thread_id:
  - "2100280572"
---
Recently I've added my Linux Servers under Solarwinds Monitoring, So I had perform following steps on my Linux Server, I hope it help you too. 

If you are using any firewall then you need to allow Port UDP 161.

Now Login as root or use SUDO for following commands, if you are using RHEL5/6 then you can configure Yum using  cd-media, then you can install packages using Yum

<strong>Install SNMP Packages </strong>

[crayon]yum install net-snmp net-snmp-utils [/crayon]

Configuration, I'm using Public community 

[crayon]# take backup of default configuration file
mv -v /etc/snmp/snmpd.conf{,-default} [/crayon]

Configure `snmpd.conf` file as below 
[crayon]########################################################################
# define your network 
com2sec local localhost  public
com2sec mynetwork 172.16.0.0/16  public
####
# Second, map the mynetwork into a group name:
group MyRWGroup v1 local
group MyRWGroup v2c local
group MyROGroup v1 mynetwork 
group MyROGroup v2c mynetwork 

########################################################################
# System contact information
#

syslocation Mumbai-Vikroli
syscontact Linux Ops

####
# Third, create a view for us to let the group have rights to:

# Make at least snmpwalk -v 1 localhost -c public system fast again.
# name incl/excl subtree mask(optional)
view systemview included .1.3.6.1.2.1.1
view systemview included .1.3.6.1.2.1.25.1.1
view all included .1 80 # This will include all MIB like disk,loadavg you don't need to include those individually 

####
# Finally, grant the group read-only access to the systemview view.
# group context sec.model sec.level prefix read only notif
access MyROGroup "" any noauth exact all none none

# read write access to local 
access MyRWGroup  "" any noauth exact all all none
[/crayon]



By Default it's allowed only for localhost and if you try from other side you will received error : `Timeout: No Response`, So you need to Edit `/etc/sysconfig/snmpd.options` and make following changes :

[crayon]# snmpd command line options
OPTIONS="-Lsd -Lf /dev/null -p /var/run/snmpd.pid 0.0.0.0"[/crayon]
Please comment below if you are facing any issue with the same. 

Now once restart snmpd daemon 
[crayon]/etc/init.d/snmpd restart[/crayon]
Make sure it should be running and listen on Port 161 you can check `netstat -tulnp | grep 161`

Enable at startup 
[crayon]chkconfig --level 35 snmpd on [/crayon]

<strong>Testing </strong>

[crayon]snmpwalk -v 2c -c public localhost system[/crayon]