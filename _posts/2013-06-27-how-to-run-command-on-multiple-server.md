---
ID: 717
post_title: How to Run command on Multiple Server
author: Rahul Patil
post_date: 2013-06-27 20:58:38
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2013/06/27/how-to-run-command-on-multiple-server/
published: true
dsq_thread_id:
  - "2078917288"
---
<blockquote>Hello,
How to run cronjob for multiple red hat and Debian servers which can
automatically restart winbind,ntp and samba services on daily basis??
I need script for this.
There are more than 100 servers which can be accessible from any 1 server.
Please tell me how to do this.</blockquote>
You can use following Script for the same, you just need to add/modify services which you need to restart. Note that you need to have ssh password less log-in , if not then refer <a href="ssh-key-based-authentication">this</a> page.

Your Server list "/opt/servers_list" should as below :

[sourcecode lang="plane"]

192.168.1.10
192.168.1.20
192.168.1.30

....

[/sourcecode]

<strong>Script :</strong>

[sourcecode lang="shell"]

#!/usr/bin/env bash
Servers_list=/opt/servers_list
Services=( portmap ntpd gpm )

for Host in $( &lt; $Servers_list )
do
        echo &quot;Restarting services on $Host&quot;

        ssh $Host xargs -n1 &lt;&lt;&lt; ${Services[@]} | 
        awk '{print &quot;/etc/init.d/&quot;$0,&quot;restart&quot;}' | sh
        echo &quot;Completed....&quot;
done

[/sourcecode]

<strong>Explanation :</strong>
Script will read each line/server from "/opt/servers_list" using <strong>for loop</strong> with <strong>$(&lt; filename)</strong> Option , then on each server it will run service restart command "<strong>/etc/init.d/servicename restart</strong>" , the trick I use <strong>awk</strong> with <strong>xargs</strong> to run multiple command at once , So it will save time, because it's save time. because it's better to ssh once and run command and finish on each server instead of doing ssh for each command.  if you just want to run any single on multiple server then you can use "<strong>ssh   $Host   'Your Command Here ' </strong> "

If you don't have password less log-in then you can use following python script which read IP,Username and password from input file and fire service restart command on remote host using ssh.

this script required  python version 2.6 and higher and "<strong>paramiko</strong>" module to use ssh .

By default CentOS/RHEL Python version is 2.4 , but you can simple download and install latest version using epel repo.

<strong>Setup for RHEL/CentOS</strong>

Install Epel repo.

[crayon lang="plane" ]

rpm -Uvh http://dl.fedoraproject.org/pub/epel/5/i386/epel-release-5-4.noarch.rpm

[/crayon]

Install required packages

[crayon lang="plane"]

yum install python26.i386 python26-paramiko.noarch

[/crayon]

then change script shebange/first line to <strong>#!/usr/bin/python26</strong>

If you are using <strong>ubuntu</strong> then just install "<strong>python-paramiko</strong>" .

Script:

[sourcecode lang="python"]
#!/usr/bin/env python

# Author : Rahul Patil &lt; http://www.linuxian.com &gt;
# Date : Thu Jun 30 02:00:12 IST 2013
# PurPose : Restart services on remote servers, input file contain serverIP Username Password
# this script will read ip username password from inputfile(which you need to define in this script ).
# then it will fire service restart commands on remote servers using ssh.

# Required Python Version 2.7.3 and higher
import os,sys
from paramiko import AutoAddPolicy, SSHClient

# add your services below in queted sepereted by comma
serviceList=[ 'portmap', 'ntpd' ]

&quot;&quot;&quot;
server list should as below
hostip username password
&quot;&quot;&quot;
serverList='/opt/servers_list'

# Created List of command because do once ssh and run commands
# also if we do ssh for single command it takes time.
restartCmd = [&quot;/etc/init.d/&quot; + srv + &quot; restart&quot; for srv in serviceList]

def SshCommand(s,u,p,c):
        &quot;&quot;&quot;
        Run command on remote server
        &quot;&quot;&quot;
        client = SSHClient()
        client.set_missing_host_key_policy(AutoAddPolicy())
        print &quot;SSH ==&gt;&gt; %s&quot; % (s)
        try:
                client.connect(s, username=u, password=p)
                stdin, stdout, stderr = client.exec_command(c)
                for line in stdout:
                print '... ' + line.strip('n')
                client.close()
                print &quot;Close Connection %s&quot; % (s)
        except:
                print &quot;Unable to Connect: %s&quot; % (s)
                pass

def Main():
        &quot;&quot;&quot;
        Read Server from input list then run command using ssh
        &quot;&quot;&quot;
        with open(serverList) as s:
            for line in s:
                l=line.split()
                server, username, password = l[0], l[1], l[2]
                SshCommand( server,username,password,'; '.join(restartCmd) )

if __name__ == '__main__':
    Main()
[/sourcecode]