---
ID: 280
post_title: 'Error while loading php module [undefined symbol: OnUpdateLong]'
author: Rahul Patil
post_date: 2012-11-02 12:27:20
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/11/02/error-while-loading-php-module-undefined-symbol-onupdatelong/
published: true
dsq_thread_id:
  - "2078921949"
---
While loading php module into apache I faced below error

/usr/local/apache2/modules/libphp5.so into server: /usr/local/apache2/modules/libphp5.so: undefined symbol: OnUpdateLong

After trying a couple of things I found a solution which might also be helpful to others, so I thought about making a post to help others having the same problem.


Solution is as follows :

1. Rename existing compiled php directory using :
[sourcecode language="bash"] 
mv /usr/local/apache2/php{,-old}
[/sourcecode]

Note:- php directory may be diffrent in your side

2 change the directory to downloaded php source code directory 

3. to clean old binary file
[sourcecode language="bash"]
make clean
[/sourcecode]
recompile again
[sourcecode language="bash"]
./configure --prefix=/usr/local/apache2/php 
			--with-apxs2=/usr/local/apache2/bin/apxs 
			--with-config-file-path=/usr/local/apache2/php 
			--enable-force-cgi-redirect 
			--disable-cgi 
			--with-zlib
			
make

make install
[/sourcecode]
4. now restart apache service and check php module is loaded or not, using below command
[sourcecode language="bash"]
/usr/local/apache2/bin/httpd -M | grep php
[/sourcecode]
Output :
[sourcecode language="bash"]
Syntax OK
php5_module (shared)
[/sourcecode]

Hope you like the solution, feel free to comment.