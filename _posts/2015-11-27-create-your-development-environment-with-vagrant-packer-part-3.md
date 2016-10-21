---
ID: 1546
post_title: >
  Create Your Development environment with
  Vagrant + Packer| Part-3
author: Rahul Patil
post_date: 2015-11-27 12:21:45
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2015/11/27/create-your-development-environment-with-vagrant-packer-part-3/
published: true
dsq_thread_id:
  - "4355391545"
---
In this part we will see how to create your own box or Image in automated way.

We will be using Packer tool for creating box

<strong>About Packer</strong>

Packer is free and open-source software for creating identical machine images or containers for multiple platforms from a single source configuration

You can download Packer from this page.

<strong>Install and Setup Packer</strong>

Installation of packer is very easy, just download the binary files and place in your PATH

[code]
cd /usr/local/bin/
wget -cnd https://releases.hashicorp.com/packer/0.8.6/packer_0.8.6_linux_amd64.zip
unzip packer_0.8.6_linux_amd64.zip
[/code]

Okay, now you have packer, Virtual box and Vagrant is Installed. now think what are the basic thing you need to install CentOS6 automatically.

1. <strong>kickstart  </strong>:- Using kick start method you can install and Setup your OS automatically
2. <strong>ISO </strong>: CentOS6 ISO

Yes, only two things are required, we don't need to create kickstart DVD for unattended installation, packer will help to boot the kickstart configuration using floppy mode.

<strong>Setup Kickstart</strong>

You can create your own or you can use my ks.cfg and modify as per your requirement

[code]
mkdir centos6-image
cd centos6-image
wget https://raw.githubusercontent.com/rahulinux/packer/master/ks.cfg
[/code]


<strong>Packer Configuration</strong>
[code]
wget https://raw.githubusercontent.com/rahulinux/packer/master/centos6-vbox.json
[/code]

Note: you must edit the "centos6-vbox.json" and update the path of your ISO and md5sum

Scripts for setup vagrant things inside your VM and cleanup
[code]
mkdir scripts
cd scripts
wget https://raw.githubusercontent.com/rahulinux/packer/master/scripts/setup.sh
wget https://raw.githubusercontent.com/mitchellh/vagrant/master/keys/vagrant.pub
wget https://raw.githubusercontent.com/rahulinux/packer/master/scripts/cleanup.sh
[/code]

Let's build your centos6 vagrant box
[code]
packer build centos6-vbox.json
[/code]

Output
<pre>
[code]
virtualbox-iso output will be in this color.

==&gt; virtualbox-iso: Cannot find &quot;Default Guest Additions ISO&quot; in vboxmanage output (or it is empty)
==&gt; virtualbox-iso: Downloading or copying Guest additions checksums
    virtualbox-iso: Downloading or copying: http://download.virtualbox.org/virtualbox/4.3.10/SHA256SUMS
==&gt; virtualbox-iso: Downloading or copying Guest additions
    virtualbox-iso: Downloading or copying: http://download.virtualbox.org/virtualbox/4.3.10/VBoxGuestAdditions_4.3.10.iso
==&gt; virtualbox-iso: Downloading or copying ISO
    virtualbox-iso: Downloading or copying: file:///opt/ISO/CentOS-6.4-x86_64-bin-DVD1.iso
==&gt; virtualbox-iso: Creating floppy disk...
    virtualbox-iso: Copying: ks.cfg
==&gt; virtualbox-iso: Creating virtual machine...
==&gt; virtualbox-iso: Creating hard drive...
==&gt; virtualbox-iso: Attaching floppy disk...
==&gt; virtualbox-iso: Creating forwarded port mapping for SSH (host port 2430)
==&gt; virtualbox-iso: Starting the virtual machine...
    virtualbox-iso: WARNING: The VM will be started in headless mode, as configured.
    virtualbox-iso: In headless mode, errors during the boot sequence or OS setup
    virtualbox-iso: won't be easily visible. Use at your own discretion.
==&gt; virtualbox-iso: Waiting 5s for boot...
==&gt; virtualbox-iso: Typing the boot command...
==&gt; virtualbox-iso: Waiting for SSH to become available...
==&gt; virtualbox-iso: Connected to SSH!
==&gt; virtualbox-iso: Uploading VirtualBox version info (4.3.10)
==&gt; virtualbox-iso: Uploading VirtualBox guest additions ISO...
==&gt; virtualbox-iso: Executing local command: curl -k https://raw.githubusercontent.com/mitchellh/vagrant/master/keys/vagrant.pub &gt; scripts/vagrant.pub
    virtualbox-iso: % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
    virtualbox-iso: Dload  Upload   Total   Spent    Left  Speed
    virtualbox-iso: 100   409  100   409    0     0    166      0  0:00:02  0:00:02 --:--:--   166
==&gt; virtualbox-iso: Uploading scripts/vagrant.pub =&gt; /tmp/ssh_key
==&gt; virtualbox-iso: Provisioning with shell script: scripts/setup.sh
    virtualbox-iso: sudo: unknown defaults entry `requretty'
    virtualbox-iso:
    virtualbox-iso: We trust you have received the usual lecture from the local System
    virtualbox-iso: Administrator. It usually boils down to these three things:
    virtualbox-iso:
    virtualbox-iso: #1) Respect the privacy of others.
    virtualbox-iso: #2) Think before you type.
    virtualbox-iso: #3) With great power comes great responsibility.
    virtualbox-iso:
    virtualbox-iso: [sudo] password for packer:
==&gt; virtualbox-iso: Gracefully halting virtual machine...
    virtualbox-iso: sudo: unknown defaults entry `requretty'
    virtualbox-iso: [sudo] password for packer:
    virtualbox-iso: Removing floppy drive...
==&gt; virtualbox-iso: Preparing to export machine...
    virtualbox-iso: Deleting forwarded port mapping for SSH (host port 2430)
==&gt; virtualbox-iso: Exporting virtual machine...
    virtualbox-iso: Executing: export packer-virtualbox-iso-1448344753 --output output-virtualbox-iso/packer-virtualbox-iso-1448344753.ovf
==&gt; virtualbox-iso: Unregistering and deleting virtual machine...
==&gt; virtualbox-iso: Running post-processor: vagrant
==&gt; virtualbox-iso (vagrant): Creating Vagrant box for 'virtualbox' provider
    virtualbox-iso (vagrant): Copying from artifact: output-virtualbox-iso/packer-virtualbox-iso-1448344753-disk1.vmdk
    virtualbox-iso (vagrant): Copying from artifact: output-virtualbox-iso/packer-virtualbox-iso-1448344753.ovf
    virtualbox-iso (vagrant): Renaming the OVF to box.ovf...
    virtualbox-iso (vagrant): Compressing: Vagrantfile
    virtualbox-iso (vagrant): Compressing: box.ovf
    virtualbox-iso (vagrant): Compressing: metadata.json
    virtualbox-iso (vagrant): Compressing: packer-virtualbox-iso-1448344753-disk1.vmdk
Build 'virtualbox-iso' finished.

==&gt; Builds finished. The artifacts of successful builds are:
--&gt; virtualbox-iso: 'virtualbox' provider box: builds/virtualbox-centos6.box

[/code]
</pre>
Also you can build your AWS AMI, have look at <a href="https://github.com/rahulinux/packer/blob/master/aws-ami.json">this </a>sample JSON.