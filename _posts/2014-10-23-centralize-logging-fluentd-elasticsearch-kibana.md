---
ID: 1379
post_title: >
  Centralize Logging with Fluentd +
  Elasticsearch + Kibana
author: Rahul Patil
post_date: 2014-10-23 06:53:47
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2014/10/23/centralize-logging-fluentd-elasticsearch-kibana/
published: true
dsq_thread_id:
  - "3146729611"
---
&nbsp;
<div></div>
<div><b><span style="font-size: large;">Overview </span></b></div>
<div>

<hr />

</div>
<div></div>
<div></div>
<div>
<div>

Recently we have deployed ELK Setup in my current company, it's Very nice project, our all requirement were meet using fluentd, like Routing logs based on tags, email alert, custom field add, with Nice Webui.

</div>
<div></div>
<div>

This document for Setup Centralize log server using Fluentd + Elasticsearch + Kibana. simply fluentd agent ( td-agent ), will forward logs to fluentd server. for WebUI authentication we have used Google Auth, there are 3 authentication Kibana-proxy provides ( Basic, Google, CAS ).

</div>
<div></div>
</div>
<div><b><span style="font-size: large;">Architecture </span></b></div>
<div>

<hr />

</div>
<div></div>
<div></div>
<div></div>
<div>

<a href="http://linuxian.com/wp-content/uploads/2014/10/fluentd-elasticsearch-kibana.png"><img class="aligncenter size-full wp-image-1381" src="http://linuxian.com/wp-content/uploads/2014/10/fluentd-elasticsearch-kibana.png" alt="fluentd-elasticsearch-kibana" width="730" height="279" /></a>

&nbsp;

</div>
<div></div>
<div></div>
<div>
<div><b><span style="text-decoration: underline;">Component Break Down</span></b></div>
<div></div>
<div>
<ol>
	<li><strong>Fluentd client:</strong> td-agent ( fluentd agent ), will be install on client side, it will read logs and forward it to fluentd server.</li>
	<li><b>Fluentd Server :  </b>server will received logs from other fluentd client and forward it elasticsearch, also you can send to S3, mongodb etc.</li>
	<li><b>Elasticsearch: </b>The main objective of a central log server is to collect all logs at one place, plus it should provide some meaningful data for analysis. Like you should be able to search all log data for your particular application at a specified time period. Hence there must be a searching and well indexing capability on our fluentd server. To achieve this, we will install another opensource tool called as elasticsearch. Elasticsearch uses a mechanism of making an index, and then search that index to make it faster.  Its a kind of search engine for text data.</li>
	<li><b>Kibana : </b>Kibana is a user friendly way to view, search and visualize your log data</li>
</ol>
</div>
<b><span style="font-size: large;">Setup </span></b>

<hr />

</div>
<div>
<p>
<pre class="">Server        : Ubuntu 14.04 LTS
IPaddr        : 192.168.122.22
Client        : 192.168.122.1
Logs          : Nginx access and error logs ( Customize format )
Fluentd       : 0.10.52 ( Client &amp; Server )
Elasticsearch : 1.2.2
Kibana        : 3.1.0</pre>

<hr />
</p>
<b><span style="font-size: large;">Installation</span></b>

<hr />

</div>
<div><b><span style="color: #ff9900; font-size: large;">Setup Elasticsearch </span></b></div>
<div></div>
<div>Install required packages for elasticsearch</div>
<div>

[code lang="bash"]
sudo apt-get update
sudo apt-get install openjdk-7-jre-headless --yes
[/code]

</div>
<div></div>
<div>Note: Java version should be 1.7.0_55 or above</div>
<div></div>
<div>Install Elasticsearch</div>
<div></div>
<div>

[code lang="bash"]
sudo wget https://download.elasticsearch.org/elasticsearch/elasticsearch/elasticsearch-1.2.2.deb
sudo dpkg -i elasticsearch-1.2.2.deb
[/code]

</div>
<div></div>
<div>
<div>Append below line in "/etc/elasticsearch/elasticsearch.yml"</div>
<div></div>
<div>

[code lang="bash"]script.disable_dynamic: true[/code]

</div>
<div>

Start service and enable at start-up

[code lang="bash"]
service elasticsearch start
update-rc.d elasticsearch defaults
[/code]

</div>
<div>
<div>
<div><b><span style="color: #ff9900; font-size: large;">Install and Configure Nginx </span></b></div>
<div></div>
<div></div>
<div>

[code lang="bash"]sudo apt-get install nginx --yes[/code]

</div>
<div><b><span style="color: #ff9900; font-size: large;"> </span></b></div>
<div>Edit configuration file "/etc/nginx/sites-available/default" and add below options</div>
</div>
<div></div>
<div>

[code lang="bash"]server {
 listen                *:80 ;
 server_name           YOUR_DOMAIN_NAME;
 access_log            /var/log/nginx/kibana.log;
 location / {
        # Forward to kibana proxy
        proxy_pass http://localhost:9201;
        # an HTTP header important enough to have its own Wikipedia entry:
        #   http://en.wikipedia.org/wiki/X-Forwarded-For
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        # enable this if and only if you use HTTPS, this helps Rack
        # set the proper protocol for doing redirects:
        # proxy_set_header X-Forwarded-Proto https;

        # pass the Host: header from the client right along so redirects
        # can be set properly within the Rack application
        proxy_set_header Host $http_host;

        # we don't want nginx trying to do something clever with
        # redirects, we set the Host: header above already.
        proxy_redirect off;

        # set &quot;proxy_buffering off&quot; *only* for Rainbows! when doing
        # Comet/long-poll stuff.  It's also safe to set if you're
        # using only serving fast clients with Unicorn + nginx.
        # Otherwise you _want_ nginx to buffer responses to slow
        # clients, really.
        # proxy_buffering off;
 }

}[/code]

</div>
<div></div>
<div>
<div>Once restart Nginx and add in start-up</div>
<div></div>
<div>

[code lang="bash"]service nginx start
update-rc.d nginx defaults[/code]

</div>
<div> <b><span style="color: #ff9900; font-size: large;">Setup Kibana</span></b></div>
<div>

[code lang="bash"]
cd /opt/
apt-get update 
apt-get install npm git apt-add-repository -y  
git clone https://github.com/fangli/kibana-authentication-proxy
cd kibana-authentication-proxy/
git submodule init
git submodule update
npm install
npm install -g forever
ln -fs $(which nodejs) /usr/bin/node
[/code]

</div>
<div>
<div>Note: Create Client ID for Google Auth</div>
<div></div>
<div>Use Following Steps to Create Client ID</div>
<div>
<ol>
	<li>Login to Gmail Account</li>
	<li>Go to http://code.google.com/apis/console</li>
	<li>Create New Project</li>
	<li>Under "Api and Auth" Section Goto Credentials</li>
	<li>Create New Clent ID ( For localsetup use "installed Application" and "Other For Public Domain Use WebApplication)</li>
	<li>Then come to "Consent screen" you have empty field "PRODUCT NAME" - you need to select e-mail address as well.</li>
</ol>
</div>
<div><span style="color: #222222; font-family: arial; font-size: small;">Now Configure Client ID and other details which is required for Google auth in "</span><span style="color: #222222; font-family: arial; font-size: small;"><i>/opt/kibana-authentication-proxy/config.js</i></span>"</div>
<div></div>
<div></div>
<div>
<div><b>Setup init script for Kibana</b></div>
<div></div>
<div>Edit "/etc/init/kibana.conf" and add following entries</div>
<div>

[code lang="bash"]
#!upstart

description &quot;Kibana Proxy for Logserver&quot;

start on started mountall
stop on shutdown

# This line is needed so that Upstart reports the pid of the Node.js process
# started by Forever rather than Forever's pid.
expect fork

env APP_ROOT=/opt/kibana-authentication-proxy/app.js
env LOG=/var/log/node.log
env PID_FILE=/var/run/node.pid

script
     exec  forever --pidFile $PID_FILE -a -l $LOG start $APP_ROOT
end script 

pre-stop script
     exec  forever stop  $APP_ROOT
end script
[/code]

</div>
<div></div>
<div>
<div><b><span style="color: #ff9900; font-size: large;">Setup Fluentd </span></b></div>
<div>
<div></div>
<div></div>
<div>

[code lang="bash"]
curl -L http://packages.treasure-data.com/debian/RPM-GPG-KEY-td-agent | sudo apt-key add -
sudo apt-add-repository 'deb http://packages.treasuredata.com/2/ubuntu/trusty/ trusty contrib'
sudo apt-get -yq update
sudo apt-get -yq install td-agent
[/code]

</div>
</div>
</div>
<div> Install require plug-ins</div>
<div></div>
<div>

[code lang="bash"]
/opt/td-agent/embedded/bin/fluent-gem install fluent-plugin-elasticsearch
/opt/td-agent/embedded/bin/fluent-gem install fluent-plugin-record-modifier
/opt/td-agent/embedded/bin/fluent-gem install fluent-plugin-secure-forward
[/code]

</div>
</div>
<div></div>
<div>Edit "/etc/td-agent/td-agent.conf"</div>
<div>

[code lang="bash"]
## DESCRIPTION : 
## It will Listen fluentd on TCP/UDP 24224 for Local intranet logging
## and TCP 24284 for Secure logging from client  
## Then it will forward to elasticsearch on localhost 9200

## You must set SERVER_IP and CLIENT_IP 

####
## Output descriptions:
##
## Forward nginx logs To elasticsearch
&lt;match nginx.**&gt;
   type elasticsearch
   logstash_format true
   flush_interval 5s #debug
   host localhost
   port 9200
   index_name fluentd
   type_name fluentd
&lt;/match&gt;

#### match tag=debug.** and dump to console
#&lt;match **&gt;
#  type stdout
#&lt;/match&gt;


####
## Source descriptions:
##

## built-in TCP input
## @see http://docs.fluentd.org/articles/in_forward
&lt;source&gt;
  type forward
  bind SERVER_IPADDR 
&lt;/source&gt;

## Received Logs with Encryption for outside intranet traffice
##
&lt;source&gt;
 type secure_forward
 shared_key         raweng_logserver
 cert_auto_generate     yes
 self_hostname ${hostname}
 allow_anonymous_source no  # Allow to accept from nodes of &lt;client&gt;
 &lt;client&gt;
   host CLIENT_IP
   # network address (ex: 192.168.10.0/24) NOT Supported now
  &lt;/client&gt;
&lt;/source&gt;

[/code]

</div>
<div></div>
<div>Note: Please update server Ipaddress and client address in above configuration file.</div>
<div></div>
<div>Then do configtest</div>
<div></div>
<div>

[code lang="bash"]
/etc/init.d/td-agent configtest
[/code]

</div>
<div> If everything seems OK then you can start service and add into start-up</div>
<div></div>
<div>

[code lang="bash"]
# Start services 
service nginx restart
service td-agent restart
service elasticsearch restart
start kibana
# Add into start-up jobs
update-rc.d elasticsearch defaults
update-rc.d nginx defaults
update-rc.d td-agent defaults
[/code]

</div>
<div></div>
<div><b><span style="font-size: large;">Troubleshooting </span></b></div>
<div></div>
<div>
<div>If in case logs are not appearing, you can start fluentd in  debug mode using below steps :</div>
<div></div>
<div>1. Stop running td-agent and run below command .</div>
<div></div>
<div>2. Add Following option in "/etc/td-agent/td-agent.conf"</div>
<div></div>
<div>

[code lang="bash"]
#### match tag=** and dump to console
&lt;match **&gt;
  type stdout
&lt;/match&gt;
[/code]

</div>
<div></div>
3. Start service with</div>
<div></div>
<div>

[code lang="bash"]
/opt/td-agent/embedded/bin/fluentd -vvv -c /etc/td-agent/td-agent.conf
[/code]

</div>
<div></div>
<div> In Next Part we will see how to add and configure client.
In case if you face any issue or need any help just comment below </div>
</div>
</div>
</div>
</div>