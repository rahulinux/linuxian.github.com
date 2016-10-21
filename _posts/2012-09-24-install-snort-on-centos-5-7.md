---
ID: 185
post_title: Install Snort on CentOS 5.7
author: Rahul Patil
post_date: 2012-09-24 13:24:03
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/09/24/install-snort-on-centos-5-7/
published: true
dsq_thread_id:
  - "2080011123"
---
Snort is a popular open source intrusion detection system. You can obtain it at: http://www.snort.org/ . Snort analyzes traffic and tries to detect and log suspicious activity. Snort is also capable of sending alerts based on the analysis that it does.

<strong>Step 1# Install Required Packages</strong>

Install packages using yum

[sourcecode language="bash"]
yum install mysql-bench mysql-devel php-mysql gcc pcre-devel php-gd gd glib2-devel gcc-c++ libcap-devel  mysql-server –y
[/sourcecode]

Download all packages in /usr/local/src/

<strong>Step 2# Compilation</strong>
Before installing snort, we will install required packages libdnet,libpcap,daq

Installing Required Packages
<strong>Installing libdnet</strong>

[sourcecode language="bash"]
wget http://libdnet.googlecode.com/files/libdnet-1.12.tgz
[/sourcecode]

extract packages

[sourcecode language="bash"]
tar xvfz libdnet-1.12.tgz
cd libdnet-1.12/
[/sourcecode]

configure libdnet use autoconfway method

[sourcecode language="bash"]
./configure
make
make install
cd ..
[/sourcecode]

<strong>Installing libpcap</strong>

[sourcecode language="bash"]
wget -cnd wget http://www.tcpdump.org/release/libpcap-1.0.0.tar.gz
tar -xzvf libpcap-1.0.0.tar.gz
cd libpcap-1.0.0
./configure
make
make install
cd ..
[/sourcecode]

<strong>Installing Daq</strong>

[sourcecode language="bash"]
wget -cnd &quot;http://www.snort.org/downloads/1850&quot; -O daq-1.1.1.tar.gz
tar -xzvf daq-1.1.1.tar.gz
./configure --with-libpcap-includes=/usr/local/lib/
make
make install
[/sourcecode]

<strong>Installing SNORT</strong>

[sourcecode language="bash"]
wget -cnd &quot;http://www.snort.org/downloads/1862&quot; -O snort-2.9.3.1.tar.gz
tar -xzvf snort-2.9.3.1.tar.gz
cd snort-2.9.3.1
./configure --with-mysql --enable-dynamicplugin
make
make install
groupadd snort
useradd -g snort snort -s /sbin/nologin
mkdir /etc/snort &amp;&amp; mkdir /etc/snort/rules &amp;&amp;  mkdir /etc/snort/so_rules
mkdir /var/log/snort &amp;&amp; chown snort:snort /var/log/snort
cd etc/ &amp;&amp; cp -v * /etc/snort/
[/sourcecode]

<strong>Verify the Snort Installation</strong>
Verify the installation as shown below.

[sourcecode language="bash"]
[root@centos5 ~]# snort --version

,,_     -*&gt; Snort! &lt;*-
o&quot;  )~   Version 2.9.3.1 IPv6 GRE (Build 40)
''''    By Martin Roesch &amp; The Snort Team: http://www.snort.org/snort/snort-team
Copyright (C) 1998-2012 Sourcefire, Inc., et al.
Using libpcap version 1.0.0
Using PCRE version: 6.6 06-Feb-2006
Using ZLIB version: 1.2.3
[/sourcecode]

&nbsp;

<strong>Configure Snort</strong>

Snort rules come in two flavors.One is a paid subscrption that assures you have the latest and
gratest(highly recommentded and very inexpensive), the other is for registerd users at Snort.org.
so at the very least go to Snort.org and register for an email account and then download the latest rules file.
One you have done this you can use scp (ssh secure copy) to copy thease files over to your new snort installation
using comand similar to the following.

after downloading rule on server , do following steps

[sourcecode language="bash"]
mkdir /tmp/snort/
tar -xzvf snortrules-snapshot-2922.tar.gz -C /tmp/snort/
mkdir /etc/snort/so_rules
cp /tmp/snort/so_rules/precompiled/Centos-5-4/i386/2.9.2.2/* /etc/snort/so_rules
cp -r  /tmp/snort/rules /etc/snort/
[/sourcecode]

Modify Your snort.conf file:
The snort.conf file is located in /etc/snort. Again using your favorite text editor
open this file and make the following changes.
"var RULE_PATH ../rules" chage this line to "var RULE_PATH /etc/snort/rules"
"var SO_RULE_PATH ../so_rules" change this line to "var SO_RULE_PATH /etc/snort/so_rules"
"var WHITE_LIST_PATH ../rules" change this line to "var WHITE_LIST_PATH /etc/snort/rules"
"var BLACK_LIST_PATH ../rules" change this line to "var BLACK_LIST_PATH /etc/snort/rules"

and crate two file as below

[sourcecode language="bash"]
touch /etc/snort/rules/black_list.rules
touch /etc/snort/rules/white_list.rules
[/sourcecode]

Next - Scroll down to the "output" section and add the following line
output log_unified2: filename snort.log, limit 128
OR
use following command for doing same settings as mention as above

[sourcecode language="bash"]
sed -i.bkp &quot;s/var RULE_PATH ../rules/var RULE_PATH /etc/snort/rules/&quot; /etc/snort/snort.conf
sed -i &quot;s/var SO_RULE_PATH ../so_rules/var SO_RULE_PATH /etc/snort/so_rules/&quot; /etc/snort/snort.conf
sed -i &quot;524i output log_unified2: filename snort.log, limit 128&quot; /etc/snort/snort.conf
sed -i &quot;s/var BLACK_LIST_PATH ../rules/var BLACK_LIST_PATH /etc/snort/rules/&quot; /etc/snort/snort.conf
sed -i &quot;s/var WHITE_LIST_PATH ../rules/var WHITE_LIST_PATH /etc/snort/rules/&quot; /etc/snort/snort.conf
touch /etc/snort/rules/white_list.rules
touch /etc/snort/rules/black_list.rules
[/sourcecode]

<strong>SNORT INIT SCRIPT</strong>
Download file called "snortd" and copy in to /etc/init.d/ then make it executable

[sourcecode language="bash"]
cd /etc/init.d/
wget -cnd &quot;https://sites.google.com/site/linuxrahulpatil/documents/snortd?attredirects=0&amp;d=10&quot; -O snortd
[/sourcecode]

create symlink of for snort , it’s required for init script

[sourcecode language="bash"]
ln -fs /usr/local/bin/snort /usr/sbin/snort
chmod +x /etc/init.d/snortd  #(set executable permission to init file)
[/sourcecode]

now start snort service

[sourcecode language="bash"]
/etc/init.d/snort start
[/sourcecode]

Reference link

<cite>http://www.<strong>snort</strong>.<strong>org</strong></cite>

Enjoy!!

Configure SNORT with custom rule will coming soon...