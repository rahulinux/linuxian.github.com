---
ID: 148
post_title: >
  Running A MyDNS Name Server On
  CentOs5/RHEL (MySQL + PHP + MyDNS )
author: Rahul Patil
post_date: 2012-08-26 13:14:47
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/08/26/running-a-mydns-name-server-on-centos5rhel-mysql-php-mydns/
published: true
dsq_thread_id:
  - "2083811518"
---
This tutorial shows how to run a MyDNS name server on an CentOs5/RHEL server.
It covers the installation of MySQL, PHP, MyDNS, the web frontend for the MyDNS name server.

Why MyDNS
<ul>
	<li> it's only Authoritative DNS.</li>
</ul>
<ul>
	<li> is designed to serve records directly from an Mysql database</li>
</ul>
<ul>
	<li> stability, security, interoperability, and speed, though not necessarily in that order.</li>
</ul>
<ul>
	<li> it does not include recursive name service, nor a resolver library. It is primarily designed</li>
</ul>
<ul>
	<li>  for organizations with many zones and/or resource records who desire the ability to perform  real-time dynamic updates on their DNS data via MySQL.</li>
</ul>
What you need:
mysql Client
mysql-server
apache (default installed)
php

log in as root.

<strong>1. Installing Mysql-server and Client</strong>
We can install MySQL as follows:

[sourcecode language="bash"]
yum install mysql mysql-server
[/sourcecode]

Then we create the system startup links for MySQL (so that MySQL starts automatically whenever the
system boots) and start the MySQL server:

[sourcecode language="bash"]
chkconfig --levels 235 mysqld on
/etc/init.d/mysqld start
[/sourcecode]

to set a password for the user root (otherwise anybody can access your MySQL database!).

[sourcecode language="bash"]
mysqladmin -u root password yourpassword
[/sourcecode]

Creating Database Called "mydns" with mydns user

[sourcecode language="bash"]
mysqladmin -u root -p create mydns
Enter password:
[/sourcecode]

Create a user the mydns server can use to access the `mydns' database.

[sourcecode language="bash"]
mysql -u root -p mydns
Enter password:
mysql&gt; use mydns;
mysql&gt; GRANT SELECT ON mydns.* TO mydns@localhost IDENTIFIED BY 'yourpassword';
mysql&gt; GRANT SELECT, INSERT, UPDATE, DELETE ON mydns.* TO 'mydns'@'localhost' IDENTIFIED BY 'yourpassword';
mysql&gt; GRANT SELECT, INSERT, UPDATE, DELETE ON mydns.* TO 'mydns'@'localhost.localdomain' IDENTIFIED BY 'yourpassword';
mysql&gt; FLUSH PRIVILEGES;
mysql&gt; quit;
[/sourcecode]

<strong>2. Installing MyDNS</strong>

We can install MyDNS as follows:

[sourcecode language="bash"]
cd /usr/local/src/
wget http://mydns.bboy.net/download/mydns-mysql-1.1.0-1.i386.rpm
rpm -ivh mydns-mysql-1.1.0-1.i386.rpm
[/sourcecode]

Open file "/etc/mydns.conf" with your faourate editor and do changes as mention as below

[sourcecode language="bash"]
db-user = mydns                 # SQL server username
db-password = yourpassword         # SQL server password
database = mydns                # MyDNS database name
[/sourcecode]

Create the tables in the `mydns' database using :

[sourcecode language="bash"]
mydns --create-tables | mysql -u root -p mydns
Enter password:
[/sourcecode]

Now Start MyDns

[sourcecode language="bash"]
/etc/init.d/mydns start
[/sourcecode]

<strong>3. To Configure PHP Front End With Auth</strong>
By Default mydns-mysql package create admin.php file
this will find using :

[sourcecode language="bash"]
rpm -ql mydns-mysql | grep admin.php
/usr/share/mydns/admin.php  ## OUTPUT
[/sourcecode]

By Default admin.php not password protected , so download attached admin_mysql.tar.gz

[sourcecode language="bash"]
cp /usr/share/mydns/admin.php{,-default}  # to take backup of existing admin.php
cd /usr/share/mydns/
wget &quot;https://sites.google.com/site/linuxrahulpatil/documents/admin_mydns.zip&quot; -O admin_mydns.zip
unzip admin_mydns.zip
[/sourcecode]

Now edit admin.php and set database Username/password and for login page
do following changes
on line number 13,14 for login page
$username = 'rahul';
$password = 'x';

on line number 81,82 to connect database
$dbuser = "mydns";
$dbpass = "yourpassword";

To configure apache to support php script with mysql

[sourcecode language="bash"]
yum install httpd php php-mysql php-mbstring php php-devel php-gd php-eaccelerator php-mcrypt php-mhash php-mssql php-soap php-tidy curl curl-devel perl-libwww-perl ImageMagick libxml2 php-cli -y
[/sourcecode]

Open file "/etc/httpd/conf.d/mydns.conf" with your fauraite editor
and add follwing entries.

[sourcecode language="bash"]
###############################################
#      Web application to manage MySQL
###############################################

&lt;Directory &quot;/usr/share/mydns&quot;&gt;
Order Deny,Allow
Deny from all
Allow from All
&lt;/Directory&gt;

AddHandler php5-script .php
AddType text/html .php
DirectoryIndex admin.php

Alias /mydns /usr/share/mydns
Alias /MyDns /usr/share/mydns
Alias /MyDNS /usr/share/mydns
################################################
[/sourcecode]

Now check all requied services and add to startup.

[sourcecode language="bash"]
/etc/init.d/mysqld restart
/etc/init.d/mydns restart
/etc/init.d/httpd restart
chkconfig --level 235 mysqld on
chkconfig --level 235 mydns on
chkconfig --level 235 httpd on
[/sourcecode]

Now access your mydns using http://YOUR-SERVER-IP/mydns