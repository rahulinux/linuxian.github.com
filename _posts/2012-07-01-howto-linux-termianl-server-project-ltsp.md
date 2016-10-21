---
ID: 80
post_title: 'Howto : Linux Terminal Server Project (LTSP)'
author: Rahul Patil
post_date: 2012-07-01 21:39:08
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/07/01/howto-linux-termianl-server-project-ltsp/
published: true
dsq_thread_id:
  - "2078958636"
---
Step 1# Installation of LTSP
Download ltsp-4.1.0-1_2.iso image from its website (http://ltsp.mirrors.tds.net/pub/ltsp/isos/)

[sourcecode language="bash"]
wget -cnd http://ltsp.mirrors.tds.net/pub/ltsp/isos/ltsp-4.1.0-1.iso
[/sourcecode]

Mount LTSP iso on /mnt

[sourcecode language="bash"]
mount -o loop /root/Desktop/Download/ltsp-4.1.0-1.iso /mnt
[/sourcecode]

Download and install ltsp-utils from (http://ltsp.mirrors.tds.net/pub/ltsp/utils/ltsp-utils-0.25-0.noarch.rpm)

[sourcecode language="bash"]
rpm -ivh http://ltsp.mirrors.tds.net/pub/ltsp/utils/ltsp-utils-0.25-0.noarch.rpm
yum install perl-libwww-perl
[/sourcecode]

Run 'ltspadmin’ (as the superuser), and configure it to install the packages from local media.

• In ltspadmin, choose ‘Configure the installer options’, and then you can specify the pathname to where the package files are located.
• Pathname MUST be in the form of a URL !!!
Ex. file:///mnt and other option all blank enter
• After configuring URL in ltspadmin, choose ‘Install/Update LTSP Packages’ and select all package and install or you can read README file in this iso file

Step 2# Install Required Packages

[sourcecode language="bash"]
yum install xinetd tftp-server dhcp rdesktop -y
[/sourcecode]

Step 3#
Configure tftp for ltsp server
Open "/etc/xinetd.d/tftp" and make disable = no. Otherwise tftp not work.

[sourcecode language="bash"]
service tftp
{
socket_type = dgram
protocol = udp
wait = yes
user = root
server = /usr/sbin/in.tftpd
server_args = -s /tftpboot
disable = no
per_source = 11
cps = 100 2
flags = IPv4
}
[/sourcecode]

Now restart xinetd service

[sourcecode language="bash"]
/etc/init.d/xinetd restart
[/sourcecode]

Step 4#
Configure nfs for ltsp server

Open the file "/etc/exports" and enter following lines in it.

[sourcecode language="bash"]
/opt/ltsp 192.168.0.0/255.255.255.0(ro,no_root_squash,sync)
/var/opt/ltsp/swapfiles 192.168.0.0/255.255.255.0(rw,no_root_squash,async)
[/sourcecode]

#(above process can be done through ltspcfg)

# Now restart nfs service

[sourcecode language="bash"]
service nfs restart
chkconfig nfs on
[/sourcecode]

Step 5#
# Configure dhcpd.conf for ltsp server

vim /etc/dhcpd.conf

[sourcecode language="bash"]
ddns-update-style ad-hoc;

option subnet-mask 255.255.0.0;
option broadcast-address 192.168.255.255;
option routers 192.168.3.1;
option domain-name-servers 192.168.3.1;
#option domain-name “your_domain.org”; # You really should fix this
option option-128 code 128 = string;
option option-129 code 129 = text;

get-lease-hostnames true;

next-server 192.168.3.1;
option root-path &quot;192.168.3.1:/opt/ltsp/i386&quot;;

subnet 192.168.0.0 netmask 255.255.255.0 {
range 192.168.0.100 192.168.0.199;
# if substring (option vendor-class-identifier, 0, 9) = “PXEClient” {
filename &quot;/lts/2.4.26-ltsp-2/pxelinux.0&quot;;
# }
# else{
# filename &quot;/lts/vmlinuz-2.4.26-ltsp-2&quot;;
# }
}
[/sourcecode]

then restart dhcpd service using :

[sourcecode language="bash"]
/etc/init.d/dhcpd restart
[/sourcecode]

Step 6#
Configure lts.conf for ltsp server

Now you need to configure #vim /opt/ltsp/i386/etc/lts.conf file for client.
Here is example of my lts.conf file

[sourcecode language="bash"]
[Default]
SERVER = 192.168.3.1
XSERVER = auto
X_MOUSE_PROTOCOL = &quot;PS/2&quot;
X_MOUSE_DEVICE = “/dev/psaux”
X_MOUSE_RESOLUTION = 400
X_MOUSE_BUTTONS = 3
USE_XFS = N
X_COLOR_DEPTH = 24 # it is for xp terminal server
X_MODE_0 = 800*600
RUNLEVEL = 5
LDM_REMOTECMD = /etc/X11/xinit/Xsession
SCREEN_01 = startx
[/sourcecode]

Step 7#
Configure xdm-config file for ltsp server

Next, open the file /etc/X11/xdm/xdm-config and comment out the line :
DisplayManager.requestPort: 0
if above file not avaiable , then ignore it
And Edit This file also because sometime it makes problem
/opt/ltsp/i386/etc/X11/xdm/xdm-config and comment out the line :
! DisplayManager.requestPort: 0

Step 8#
Enable Remote Login in RedHat so that client able access login screen.

[sourcecode language="bash"]
gdmsetup
[/sourcecode]

After starting the gdmsetup utility, click the Remote tab. Under the Remote tab, change the Style pull-down menu selection from ‘Remote login disabled’ to ‘Same as Local’ :
After configuring remote access to the GDM login manager, select the Security tab. Under the Security tab,

I have checked the options:
· Allow local system administrator login
· Allow remote system administrator login

Exit from the gdmsetup utility and restart the GDM service :

[sourcecode language="bash"]
/usr/sbin/gdm-restart
[/sourcecode]

add all services at start-up and once reboot server then check from client side