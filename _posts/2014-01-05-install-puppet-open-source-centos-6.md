---
ID: 1183
post_title: >
  How to install Puppet Open Source on
  CentOS 6.5
author: Rahul Patil
post_date: 2014-01-05 14:15:52
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2014/01/05/install-puppet-open-source-centos-6/
published: true
dsq_thread_id:
  - "2093451835"
---
Let us discuss the overview, Installation and configuration of Puppet OpenSource a Configuration automation tool.

<strong>1. Overview</strong>
<ul>
	<li>Puppet</li>
	<li>Puppet architecture</li>
	<li>General Information</li>
</ul>
<strong>2. Installation on CentOS 6 ( 64 bit )</strong>
<ul>
	<li>The Setup Information</li>
	<li>Setting Up Yum repository</li>
	<li>Installing Puppet Server</li>
	<li>Installing Puppet Agent</li>
	<li>Configuring Client</li>
	<li>Testing</li>
</ul>
<strong>1. Overview </strong>
<h1>Puppet</h1>
Puppet is IT automation software that helps system administrators manage infrastructure throughout its lifecycle, from provisioning and configuration to orchestration and reporting. you could compare puppet with Active Directory GPO. Using Puppet, you can easily automate repetitive tasks, quickly deploy critical applications, and proactively manage change, scaling from 10s of servers to 1000s, on-premise or in the cloud.
<h1></h1>
<h1>Puppet Architecture</h1>
It's simple client server model as below :
<ul>
	<li>Agents nodes/clients
<ul>
	<li>Agent auto-fetch configuration information from master server every 30 minuts default</li>
</ul>
</li>
	<li>Master server
<ul>
	<li>Master server is also an Agent</li>
	<li>Master server delivers configuration information to Agents nodes.</li>
	<li>Console server - Primary management WebGui, Can be same as Master server instancts, but should be seperated Cloud Provisioner - Permits quick deployment of of new instances. Ex. Vmware, Amazon Web Services [ But this features only available in Puppet Enterprise version so it's just for your information ]</li>
</ul>
</li>
</ul>
<h1></h1>
<h1>General Information</h1>
<ul>
	<li>Which OSfamily Supports by puppet ?
<ul>
	<li>Redhat/CentOS,Scientific</li>
	<li>Debian and Ubuntu</li>
	<li>Suse</li>
	<li>Oracle Solaries</li>
	<li>IBM AIX</li>
	<li>Windows ( Limited )</li>
</ul>
</li>
	<li>Puppet Master Port : TCP 8140</li>
</ul>
<strong>2. Installation</strong>
<h1>The Setup</h1>
This Howtos assumes you have following things :
<ul>
	<li>OS : CentOS 6.5 64 bit minimal installation</li>
	<li>IP : 10.0.0.12</li>
	<li>Hostname : puppet.home.local</li>
	<li>Disable SELinux</li>
	<li>Disable IPtables for temporary later you can port TCP 8140</li>
	<li>Puppet Server at-least have 1 GB RAM</li>
</ul>
<strong>Setting Up Yum repository</strong>

[crayon]rpm -Uvh https://yum.puppetlabs.com/el/6/products/x86_64/puppetlabs-release-6-7.noarch.rpm[/crayon]

<strong>Install the Puppet Master</strong>

[crayon]

# Download puppet-server from Puppet Labs
yum install -y puppet-server

# Start Puppet-Server
/etc/init.d/puppetmaster start

# Set Puppet Master to run on startup
puppet resource service puppetmaster ensure=running enable=true

[/crayon]

<strong>Installing Puppet Agent</strong>

Note: this steps should perform on Puppet server and your puppet client

First configure <code>/etc/hosts</code> file in Server and Client  ( Or use DNS )

[crayon]

# Replace 10.0.0.11 with your Puppet Server IP
10.0.0.12 puppet.linuxian.local puppet

# Replace 10.0.0.13 with your Puppet Client
10.0.0.13 client01.linuxian.local client01

[/crayon]

Setup Yum repo for install puppet agent

[crayon]
rpm -ivh https://yum.puppetlabs.com/el/6/products/x86_64/puppetlabs-release-6-7.noarch.rpm

# Disable the repo to be used automatically
sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/puppetlabs.repo

# Install the Puppet Client
yum install -y puppet --enablerepo=puppetlabs*
[/crayon]

Edit  `/etc/puppet/puppet.conf` and add the agent variables:

[crayon]
# In the [agent] section
server = puppet
report = true
pluginsync = true
[/crayon]

Set the puppet agent to run on boot:

[crayon]
chkconfig puppet on
puppet agent --daemonize
[/crayon]

Go to your puppet server and check for signed requests from your nodes

[crayon]# Note you will have to do this anytime a new node attempts to join the puppet master

puppet cert list

[/crayon]

<strong>Output</strong>
[crayon][root@puppet ~]# puppet cert list
"client01.linuxian.local" (SHA256) F3:10:AF:50:82:7D:36:3E:4F:A6:97:0A:23:F9:F7:8C:7A:5B:BA:B2:AD:2F:8E:76:01:8A:A5:02:E8:BF:92:54
[/crayon]

Sign the cert to allow the puppet node to join
[crayon][root@puppet ~]# puppet cert sign client01.linuxian.local
[/crayon]

Output
[crayon]
Notice: Signed certificate request for client01.linuxian.local
Notice: Removing file Puppet::SSL::CertificateRequest client01.linuxian.local at '/etc/puppetlabs/puppet/ssl/ca/requests/client01.linuxian.local.pem
[/crayon]

<strong>Testing </strong>
Now we will create simple motd module and apply it on all nodes

Edit `/etc/puppet/manifests/site.pp` and add following entry :

[crayon]
class motd {

	file { "/etc/motd":

	ensure  => file,
	content => template("/etc/motd.erb"),
	}

}

node default {

	include motd

}

[/crayon]

Now create your `/etc/motd.erb` file
[crayon]
###############################################################
#                 Welcome to linuxian.com                     #         
#        All connections are monitored and recorded           #
# Disconnect IMMEDIATELY if you are not an authorized user!   #
###############################################################
[/crayon]

Now apply settings 

[crayon]puppet apply /etc/puppet/manifests/site.pp[/crayon]

this will apply settings on puppet server and all client ( after 30 minute default intervals or you can restart puppet client to apply settings ), you can check `tail -f /var/log/messages` at client end