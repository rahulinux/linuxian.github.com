---
ID: 110
post_title: Get Only IP Address as Variable
author: Rahul Patil
post_date: 2012-08-12 15:00:02
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/08/12/print-ip-address/
published: true
dsq_thread_id:
  - "2078958653"
---
In any script if you want grep only IP Address , then you can use following awk and sed tricks

Using Awk:

[sourcecode language="bash"]
/sbin/ifconfig | awk -F':' 'NR==2{split($2,a,&quot; &quot;); print a[1]}'
[/sourcecode]

Using Sed:

[sourcecode language="bash"]
ip -f inet addr show dev eth0 | sed -n 's/^ *inet *([.0-9]*).*/1/p'
[/sourcecode]
[sourcecode language="bash"]
ifconfig eth0 | sed -n 's/^ *inet addr:*([.0-9]*).*/1/p'
[/sourcecode]

To Check Live/Public IP

[sourcecode language="bash"]
curl ifconfig.me
[/sourcecode]