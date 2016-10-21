---
ID: 1481
post_title: Automate add/remove hosts in Nagios
author: Rahul Patil
post_date: 2015-07-20 06:23:14
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2015/07/20/automate-addremove-hosts-in-nagios/
published: true
dsq_thread_id:
  - "3951489324"
---
If you have multiple servers which frequently added and removed in monitoring system, how you will manage this ? We had similar issue where I work and i will present my idea on the topic.
<div>
<div>
<div><span style="font-size: large;"><b>Motivation </b></span></div>
<div></div>
Me &amp; my team is working with Hugh cluster setup, day by day there are multiple nodes added and removed in Amazon auto scale mode. we are using <span class="il">Nagios</span> monitoring. so we have decided to use <span class="il">Nagios</span> module which based on Python called <a href="http://pynag.org/" target="_blank">Pynag</a>. we have created simple wrapper using this module which will add/remove host in <span class="il">Nagios</span>.

</div>
If you want to use this same idea at your work.. you can use following steps

</div>
<b><span style="font-size: large;">Setup &amp; Installation </span></b>
<div>
<div>
<div>
<div>

Note: all step should perform on your <span class="il">NagiOS </span>server.

</div>
<div>

Install Python module

Using fedora/redhat:

</div>
<div></div>
<div>

[code]
yum install pynag
[/code]

</div>
<div></div>
<div>Using debian/ubuntu:</div>
<div></div>
<div>

[code]
sudo apt-get install python-pynag pynag
[/code]

</div>
</div>
<div></div>
<div><b>Download script </b></div>
<div>

[code]
cd /usr/local/bin/ 
wget https://raw.githubusercontent.com/rahulinux/nagios_script/master/manage_nagios.py 
chmod +x manage_nagios.py
[/code]

</div>
</div>
<div>
<div></div>
<div>Edit this script and cross verify <span class="il">Nagios</span> configuration path &amp; host declaration</div>
<div></div>
<div>Now you can use this script</div>
</div>
</div>
<div>

[code]
Usage:
           manage_nagios.py --add Hostname group1,group2
           manage_nagios.py --del Hostname
[/code]

</div>
<div></div>
<div>
<div>
<h1>Summary</h1>
It is just quick tip :), I hope it will help you to automate your nagiso monitoring

You can create your own script using this module as per your requirement. more about this module : <a href="https://github.com/pynag/pynag" target="_blank">https://github.com/pynag/pynag</a>

</div>
<div></div>
<div><b>Happy Monitoring !! </b></div>
</div>