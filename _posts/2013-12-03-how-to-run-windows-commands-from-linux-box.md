---
ID: 992
post_title: >
  How to run Windows commands from Linux
  Box
author: Rahul Patil
post_date: 2013-12-03 15:45:28
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2013/12/03/how-to-run-windows-commands-from-linux-box/
published: true
dsq_thread_id:
  - "2078895322"
---
<blockquote>How to run Windows commands from Linux Box</blockquote>
This question come to my mind while create shell script which fetch details from Linux and Unix server remotely via ssh, Why not windows server ?

So this post help to install WMI tool on Linux and some required settings on windows server then you will be able to run windows commands from Linux Box.
<h2><strong>Setup</strong></h2>
I'm assuming you are using CentOS 5 32 bit and you have internet connection on your Linux box.
<h2><strong>Installation</strong></h2>
<h6>Download and Install RPM</h6>
<h6><strong>Note</strong>: If you using differ version of CentOS or RHEL then you can Simply Search in Google lat say for CentOS 6 , you can perform search like "<strong><em>site:pkgs.org  wmi el6 rpm</em></strong>"</h6>
[crayon lang="shell"] # wget -cnd wget http://www.atomicorp.com/channels/atomic/centos/5/i386/RPMS/wmi-1.3.14-3.el5.art.i386.rpm [/crayon]

then simply install using rpm :

[crayon lang="shell"] # rpm -ivh wmi-1.3.14-3.el5.art.i386.rpm [/crayon]

That's it in Linux Side
<h3><strong>On Windows machine , You have to make sure following things</strong></h3>
<h5><strong>Issues 1#</strong></h5>
<h5>In case If you following Error:</h5>
<h5><strong>[winexe/winexe.c:129:on_ctrl_pipe_error()] ERROR: Cannot open control pipe - NT_STATUS_ACCESS_DENIED</strong></h5>
Then Enable following setting in Windows Side

[code]
Start ==&gt; Run ==&gt; secpol.msc
Local Policies  ==&gt; Security Options ==&gt; Network access: Sharing and security model for local accounts
Set =&gt; Classic: Local users authenticate as themselves
Then Apply and OK [/code]

<strong>Issue 2#</strong>
<h6>If you face following Error :</h6>
<strong>[winexe/winexe.c:120:on_ctrl_pipe_error()] ERROR: Failed to install service winexesvc - NT code 0x00000424</strong>

Then Do following things on Windows Machine
Run Following command :
On the command prompt, enter the following 2 commands:

[code]
sc create winexesvc binPath=C:WINDOWSWINEXESVC.EXE start=auto DisplayName=winexesvc
sc description winexesvc &quot;Remote command provider for Linux Box&quot;

[/code]
<h4><strong>Test from Linux Server</strong></h4>
[crayon lang="shell"]

# winexe -U  username%password  //192.168.226.1  "systeminfo"

[/crayon]

Note: change username and password and IPaddress in above command and run.

If you run into any problem, Just comment below, I'm glad to help you.