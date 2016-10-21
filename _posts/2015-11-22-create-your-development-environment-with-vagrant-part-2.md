---
ID: 1540
post_title: >
  Create Your Development environment with
  Vagrant | Part-2
author: Rahul Patil
post_date: 2015-11-22 07:57:56
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2015/11/22/create-your-development-environment-with-vagrant-part-2/
published: true
dsq_thread_id:
  - "4340487807"
---
In <a href="http://linuxian.com/2015/11/18/create-your-development-environment-with-vagrant-part-1/" target="_blank">Previous part</a> we've learned about vagrant basics, and how to spin-up your first VM using online vagrant box .

Now we will see how to create your own vagrant box from scratch.

First Create your VM in virtual box, we are going to create box for CentOS6.

Once your Installed your OS(i.e. CentOS), proceed with following steps:

<strong>
Install Required Packages</strong>

Install base packages
[code]
yum install perl gcc
[/code]

Install Open SSH Server
[code]
yum install openssh-server
[/code]

Disable SELinux
[code]
sed -i 's/^SELINUX\=.*/SELINUX=disabled/g' /etc/selinux/config
[/code]

Disable IPTables:
[code]
service iptables stop
chkconfig iptables off
[/code]

Add vagrant user and set password
[code]
useradd -r vagrant -s /bin/bash
echo 'vagrant:vagrant' | chpasswd
[/code]

Add vagrant user in sudoers
Edit "/etc/suders.d/vagrant"
[code]
vagrant ALL=(ALL) NOPASSWD: ALL
Defaults:vagrant    !requiretty
[/code]

Setup ssh Key for vagrant user

[code]
su - vagrant
mkdir .ssh
wget --no-check-certificate https://raw.github.com/mitchellh/vagrant/master/keys/vagrant.pub -O /home/vagrant/.ssh/authorized_keys
chmod 0600 .ssh/authorized_keys
[/code]

Install Virtual box Guest package:
[code]
cd /tmp/
wget -cnd http://download.virtualbox.org/virtualbox/4.3.34/VBoxGuestAdditions_4.3.34.iso
sudo mount -o loop /tmp/VBoxGuestAdditions_4.3.34.iso /mnt/
/mnt/VBoxLinuxAdditions.run
[/code]

Let's Clean UP
[code]
sudo umount /mnt
sudo rm -rfv /tmp/VBoxGuestAdditions_4.3.34.iso
sudo dd if=/dev/zero of=/EMPTY bs=1M
sudo rm -f /EMPTY
[/code]

Shutdown your VM
[code]
shutdown -h now
[/code]


Now we need to export your VM to box format, use following command for the same.

[code]
mkdir centos6-base
# Check your running VM name using
VBoxManage list runningvms
vagrant package --base your-vm-name
[/code]

Once you run above command, it will export your VM in package.box file in current directory where you executed that command.

We need to add this box in your Vagrant using below command:
[code]
vagrant box add package.box --name your_image_name
[/code]

Let's start your VM
[code]
mkdir myvm
cd myvm
vagrant init your_imange_name
vagrant up
[/code]

In next part we will see how to automated this Process.