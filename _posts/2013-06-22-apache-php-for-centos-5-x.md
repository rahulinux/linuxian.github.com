---
ID: 707
post_title: Apache + PHP for CentOS 5.x
author: Rahul Patil
post_date: 2013-06-22 07:50:29
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2013/06/22/apache-php-for-centos-5-x/
published: true
dsq_thread_id:
  - "2078917770"
---
This is Simple How-to on Apache with PHP integration in RHEL/CentOS 5.x:

We are assuming that Apache is Preinstalled ,so directly Jump to PHP installation.
There are Couple of Options to accomplish the same, it's depend on how was apache installed.

<strong>Method 1# </strong>
If your Apache is default installed and/or install using rpm or yum , then you can use below Step to enable PHP.
Enable Extra Repo to install PHP.

[crayon lang="bash"]
rpm -Uvh http://dl.fedoraproject.org/pub/epel/5/$(arch)/epel-release-5-4.noarch.rpm
rpm -Uvh http://repo.webtatic.com/yum/centos/5/latest.rpm
[/crayon]

Install PHP
[crayon lang="bash"]
yum --disablerepo=* --enablerepo=webtatic
install php php-cli php-gd php-mysql php-mbstring
[/crayon]

Restart/reload Apache to load php

[crayon lang="bash"]
/etc/init.d/httpd restart
[/crayon]

Now Jump to Testing Section .

<strong>Method 2#</strong>
If your Apache is installed using Source Code, then you can refer below Steps to enable PHP.
but note that you can enable/disable Module, if Apache was compiled with DSO ("dynamic shared object") support .

Download latest php
[crayon lang="bash"]
wget http://au1.php.net/distributions/php-5.4.11.tar.gz
cd /usr/local/src/
[/crayon]

extract packages
[crayon lang="bash"]
tar -xzvf php-5.4.11.tar.gz
cd php-5.4.11
[/crayon]

install required packages
[crayon lang="bash"]
yum install libxml2 libxml2-devel
[/crayon]

Now let's compile it.
[crayon lang="bash"]

./configure --prefix=/usr/local/apache2/php
--with-apxs2=/usr/local/apache2/bin/apxs
--with-config-file-path=/usr/local/apache2/php
--enable-force-cgi-redirect
--disable-cgi
--with-zlib
[/crayon]

Note that Check Path , in case it can be differ in your end, may be compiled in another location.
[crayon ang="bash"]
make
make install
[/crayon]

Edit httpd.conf and enable php script
[crayon ang="bash"]
vim /usr/local/apache2/conf/httpd.conf
[/crayon]

[crayon lang="bash"]
#========================================================
# Use for PHP 5.x:
AddHandler php5-script .php

# Add index.php to your DirectoryIndex line:
DirectoryIndex index.php

AddType text/html .php

# PHP Syntax Coloring
# (optional but useful for reading PHP source for debugging):
AddType application/x-httpd-php-source phps
#========================================================
[/crayon]

<strong>Testing </strong>

Check Module is Loaded or not using below command :
[crayon lang="bash"]
httpd -M | grep php
[/crayon]

Now Simply Create one php page for testing.

[crayon lang="bash"]
echo '<!--?php phpinfo(); -->' &gt;&gt; /var/www/html/index.php
[/crayon]

Now Just Browse http://IP_OF_SERVER/index.php

Note that above command will work if your apache installed using RPM, because default apache home is "/var/www/html/", so apache will load page with default configuration. if apache was compiled then you need to properly host your index.php for testing.

<strong>Troubleshooting</strong>
See log file /var/log/httpd/error* log file. Sometime due to error modules get disabled.

<strong>Reference Links</strong>
If you want go into details , refer below links
http://dan.drydog.com/apache2php.html
http://dan.drydog.com/apache2php.html

If you face any issue, please post in comments we will help you on the same.