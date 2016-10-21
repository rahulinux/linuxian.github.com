---
ID: 1438
post_title: >
  Install and Configure fluentd-agent (
  td-agent )
author: Rahul Patil
post_date: 2015-01-04 07:22:26
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2015/01/04/install-configure-fluentd-agent-td-agent/
published: true
dsq_thread_id:
  - "3388350016"
---
<blockquote>As we see installation and configuration of <a href="http://linuxian.com/2014/10/23/centralize-logging-fluentd-elasticsearch-kibana/">Centralize Logging server</a> is pretty simple, now let's see how to configure client.</blockquote>
<strong>Installation</strong>

Note: we are doing this setup on Ubuntu 14.04 Client, if you are using different then please refer <a href="http://docs.fluentd.org/articles/install-by-deb">this</a> page.


[code lang="bash"]
curl -L http://packages.treasure-data.com/debian/RPM-GPG-KEY-td-agent | sudo apt-key add -
sudo apt-add-repository 'deb http://packages.treasuredata.com/2/ubuntu/trusty/ trusty contrib'
sudo apt-get -yq update
sudo apt-get -yq install td-agent
[/code]

Install require plugins 

[code lang="bash"]
/opt/td-agent/embedded/bin/fluent-gem install fluent-plugin-elasticsearch
/opt/td-agent/embedded/bin/fluent-gem install fluent-plugin-record-modifier
/opt/td-agent/embedded/bin/fluent-gem install fluent-plugin-secure-forward

[/code]

Let's configure the td-agent.

We are just sending syslog and nginx logs to logserver, so first we need to make sure connectivity between client and server. you need to make sure port 24224 UDP and TCP both are open or not. 

[code]
nc -vzw1 logserver-ip  24224
[/code]

td-agent service run through td-agent user, we need to give read permission to td-agent user 

[code]
usermod -G adm td-agent
[/code]

Now edit "/etc/td-agent/td-agent.conf" and configure as below :

Note: You need to enter logserver, replace "Logserver-IP" with your log server ip 

[code]
###############################################
## 
## Forwarder


## Section 1 :: Sending Logs 

###############################################
## Secure forward logs to fluentd server
&lt;match **&gt;
  type forward 
  send_timeout 10s
  recover_wait 5s
  heartbeat_interval 1s
  phi_threshold 16
  hard_timeout 60s

  &lt;server&gt;
    host Logserver-IP 
    port 24224
    weight 20
  &lt;/server&gt;

&lt;/match&gt;

## Section 2 :: Reading Logs 

###############################################
## tail nginx logs 
## add tag nginx.access
#
&lt;source&gt;
  type tail
  format nginx
  path /var/log/nginx/access.log
  tag nginx.access
  # Select a file to store offset position
  pos_file /tmp/nginx-access-td-agent
&lt;/source&gt;

################################################
## tail nginx logs 
## add tag nginx.error
&lt;source&gt;
  type tail
  format /^(?&lt;time&gt;[^ ]+ [^ ]+) \[(?&lt;log_level&gt;.*)\] (?&lt;pid&gt;\d*).(?&lt;tid&gt;[^:]*): (?&lt;message&gt;.*)$/
  path /var/log/nginx/error.log
  tag nginx.error
  # Select a file to store offset position
  pos_file /tmp/nginx-error-td-agent
  time_format %Y/%m/%d %H:%M:%S
&lt;/source&gt;

################################################
## tail syslog 
## add tag syslog
&lt;source&gt;
  type tail 
  format syslog
  path /var/log/syslog
  tag syslog
  pos_file /tmp/syslog-td-agent.tmp
&lt;source&gt;
[/code]

If your logformat is different then you can create custom regex format for your logs using <a href="http://fluentular.herokuapp.com/parse?regexp=%5E%28%3F%3Ctime%3E%5B%5E%5C%5D%5D*%29+%28%3F%3Chost%3E%5B%5E+%5D*%29+%28%3F%3Ctag%3E%5B%5E+%5D*%29%3A+%28%3F%3Cremote%3E%5B%5E+%5D*%29+%28%3F%3Chost%3E%5B%5E+%5D*%29+%28%3F%3Cuser%3E%5B%5E+%5D*%29+%5C%5B%28%3F%3Ctime%3E%5B%5E%5C%5D%5D*%29%5C%5D+%22%28%3F%3Cmethod%3E%5CS%2B%29%28%3F%3A+%2B%28%3F%3Cpath%3E%5B%5E%5C%22%5D*%29+%2B%5CS*%29%3F%22+%28%3F%3Ccode%3E%5B%5E+%5D*%29+%28%3F%3Csize%3E%5B%5E+%5D*%29+%22%28%3F%3Creferer%3E%5B%5E%5C%22%5D*%29%22+%22%28%3F%3Cagent%3E%5B%5E%5C%22%5D*%29%22%24&input=Sep+12+16%3A17%3A36+192.168.1.1+nginx-access%3A+127.0.0.1+-+-+%5B12%2FSep%2F2014%3A16%3A17%3A35+%2B0530%5D+%22GET+%2F+HTTP%2F1.1%22+304+0+%22-%22+%22Mozilla%2F5.0+%28X11%3B+Linux+x86_64%29+AppleWebKit%2F537.36+%28KHTML%2C+like+Gecko%29+Ubuntu+Chromium%2F36.0.1985.125+Chrome%2F36.0.1985.125+Safari%2F537.36%22&time_format=%25d%2F%25b%2F%25Y%3A%25H%3A%25M%3A%25S+%25z">this</a> page. 
Now save above file and restart td-agent 

[code]
service td-agent restart
[/code]

Sample Kibana output from my logserver UI :


<a href="http://linuxian.com/wp-content/uploads/2015/01/kibana1.jpg"><img src="http://linuxian.com/wp-content/uploads/2015/01/kibana1.jpg" alt="kibana1" width="1283" height="427" class="aligncenter size-full wp-image-1446" /></a>

<a href="http://linuxian.com/wp-content/uploads/2015/01/kibana2.jpg"><img src="http://linuxian.com/wp-content/uploads/2015/01/kibana2.jpg" alt="kibana2" width="1275" height="513" class="aligncenter size-full wp-image-1447" /></a>