---
ID: 102
post_title: AWK Script
author: Rahul Patil
post_date: 2012-08-10 15:27:01
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/08/10/awk-script/
published: true
dsq_thread_id:
  - "2149903737"
---
This script will count system user and normal user in your system using only awk, it's very simple but script may be useful.

[sourcecode language="bash"]
#!/bin/awk
# This Script will count system user, total user,normal user in system
# Normal user id start from 500 in CentOs/RHEL base systems
# if you are using Debain/Ubuntu base systems, then you have to change this to 1000 or as per /etc/login.def file using UID_MIN TAG

#########################################################
# Author:- Rahul Patil                                  #
# Date  :- Thursday, 9 August 2012, 01:50:49 IST        #
# Site  :- &lt;http://www.linuxian.com&gt;                    #
#########################################################

#########################################################
# Usage:                                                #
# awk -f check_user.awk                                 #
#########################################################

#################-{ Start Of Programm }-#################

BEGIN {

# passwd file
ARGV[1]=&quot;/etc/passwd&quot;;
ARGC = 2;

# Filed Seperator
FS=&quot;:&quot;;

# Normal UID Start From
UID_MIN=500;

}

function PRINT(a,b,c)
{
printf(&quot;%st%st%stn&quot;,a,b,c);
}

{total = FNR};
($3 &gt; UID_MIN){normal_users++};
{ system_users = total-normal_users};

END {
PRINT(&quot;Total User&quot;,&quot;=&quot;, total);
PRINT(&quot;Normal Users&quot;,&quot;=&quot;, normal_users);
PRINT(&quot;System Users&quot;,&quot;=&quot;, system_users);
}

#################-{ END  Of Programm }-#################
[/sourcecode]

Sample Output

[sourcecode language="bash"]
Total User      =       40
Normal Users    =       3
System Users    =       37
[/sourcecode]