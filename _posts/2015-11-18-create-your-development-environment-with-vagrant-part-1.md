---
ID: 1531
post_title: >
  Create Your Development environment with
  Vagrant | Part-1
author: Rahul Patil
post_date: 2015-11-18 18:47:54
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2015/11/18/create-your-development-environment-with-vagrant-part-1/
published: true
dsq_thread_id:
  - "4332322062"
---
<h2><b>What is Vagrant ?</b></h2>
<ul>
	<li><b>Vagrant</b> is tool that creates and configures<a href="https://en.wikipedia.org/wiki/Virtualization"> virtual</a><a href="https://en.wikipedia.org/wiki/Development_environment_%28software_development_process%29"> development environments</a>.<a href="https://en.wikipedia.org/wiki/Vagrant_%28software%29#cite_note-3">[3]</a> It can be seen as a higher-level<a href="https://en.wikipedia.org/wiki/Wrapper_library"> wrapper</a> around virtualization software such as<a href="https://en.wikipedia.org/wiki/VirtualBox"> VirtualBox</a>,<a href="https://en.wikipedia.org/wiki/VMware"> VMware</a>,<a href="https://en.wikipedia.org/wiki/Kernel-based_Virtual_Machine"> KVM</a> and<a href="https://en.wikipedia.org/wiki/Linux_Containers"> Linux Containers</a> (LXC), and around<a href="https://en.wikipedia.org/wiki/Configuration_management"> configuration management</a> software such as<a href="https://en.wikipedia.org/wiki/Ansible_%28software%29"> Ansible</a>,<a href="https://en.wikipedia.org/wiki/Chef_%28software%29"> Chef</a>,<a href="https://en.wikipedia.org/wiki/Salt_%28software%29"> Salt</a>, and<a href="https://en.wikipedia.org/wiki/Puppet_%28software%29"> Puppet</a>.</li>
</ul>
<h2><b>Why Vagrant ?</b></h2>
<ul>
	<li>Vagrant provides easy to configure, reproducible, and portable work environments. Vagrant launches things to run apps/services for the purpose of development. This can be on VirtualBox, VMware, AWS, OpenStack, Docker.</li>
</ul>
<b>Work-flow </b>
<ul>
	<li>Developer defines the VM configuration</li>
	<li>Vagrant interfaces with Virtualbox or other VM manager to build and launch the VM</li>
	<li>Developer can easily rebuild the VM</li>
	<li>VM configuration can easily be shared</li>
</ul>
<h2><b>Component </b></h2>
<ul>
	<li>Box
<ul>
	<li>Pre-made images or download from http://www.vagrantbox.es/</li>
</ul>
</li>
	<li>Vagrantfile
<ul>
	<li>This tells Vagrant what hardware to spin up</li>
</ul>
</li>
	<li>Provider
<ul>
	<li>Where you want to spin up your VM’s like VirtualBox VMWare etc.</li>
</ul>
</li>
	<li>Provision
<ul>
	<li>Configure VM’s in runtime using Shell, Chef, Puppet or ansible</li>
</ul>
</li>
</ul>

<hr />

<b>Let’s get started </b>

I’m assuming you have VirtualBox Installed and following steps will help you to install Vagrant and snip up your custom box.

Install Vagrant

For CentOS/RHEL6

[code]
yum install ruby
yum install rubygems
gem update --system
gem install vagrant
[/code]

For Ubuntu

[code]


sudo apt-get install vagrant 

[/code]

Once you installed the vagrant, let’s create the single VM test
Vagrant require the box ( it’s an Image ) to boot, so you have two options,
<ul>
	<li>Download the box</li>
	<li>Create custom box ( if you don’t have fast Internet you can go with this )</li>
</ul>
If you have fast Internet then you can download box, just go to vagrant box link at <a href="http://www.vagrantbox.es/">http://www.vagrantbox.es/</a> and copy the link of box which you want to boot


[code]
vagrant box add &quot;box url&quot;
[/code]


Once you added the box, you can use that box to launch the VMs
You can list box using:


[code]
vagrant box list
[/code]


Let’s launch the VM


[code]
mkdir  my_testvm
cd my_testvm
[/code]


below command will create vagrant configuration “Vagrantfile” which you can use to modify, update the VM settings


[code]
vagrant init “box name”
vagrant up
# You can ssh to vm using
vagrant ssh
[/code]


In Next Part we will see how to manually the image creation step