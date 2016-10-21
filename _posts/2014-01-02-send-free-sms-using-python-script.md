---
ID: 1175
post_title: Send Free SMS Using Python Script
author: Rahul Patil
post_date: 2014-01-02 21:51:15
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2014/01/02/send-free-sms-using-python-script/
published: true
dsq_thread_id:
  - "2088162262"
---
My sister get frustrated with annoying ads on free sms sites, So I thought why can't do this with  simple command line script. then I made simple python script which will send sms from command line. This script is pretty simple and it will send sms through indyarocks.com free sms service.


<strong>
How to use it?</strong>

<strong>NOTE: THIS SMS SERVICE ONLY AVAILABLE FOR INDIA </strong>

If you want to use this script, you may have to install supported packages, so you can simply install using 
[crayon]wget "https://raw.github.com/rahulinux/sendsms/master/resolve_dependencies.sh" [/crayon]

then run it as root or use SUDO 
[crayon]bash resolve_dependencies.sh [/crayon]

Now Download and use  `sendsms.py`
[crayon]
wget "https://raw.github.com/rahulinux/sendsms/master/sendsms.py"
[/crayon]

Set executable Permission 
[crayon]
chmod +x sendsms.py
[/crayon]

If you have registered on <a href="http://indyarocks.com/registration">sms site</a> then you can use 
[crayon]
./sendsms.py -u username -p 'password' -to 90299186xx -m "Sent sms from Python script!!"
[/crayon]


<strong>Please note this script only tested on Ubuntu 12 and higher and CentOS 6, However if you face any issue, please feel free to comment. </strong>