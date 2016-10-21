---
ID: 1228
post_title: >
  Disable remove pemission on VSFTP for
  Specific user
author: Rahul Patil
post_date: 2014-01-11 13:48:46
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2014/01/11/disable-remove-pemission-vsftp-specific-user/
published: true
dsq_thread_id:
  - "2108391002"
---
We have had an requirement, to disable remove/delete permission for particular user in vsftpd, I just Googled for the solution but I haven't found for the same. so after checking `man vsfptd.conf`  pages I come to knew Option `"user_config_dir"`. 

This  powerful  option  allows the override of any config option specified in the manual page, on a per-user basis. Usage is simple, and is best  illustrated  with an  example.  If you set user_config_dir to be `/etc/vsftpd/user_conf` and then log on as the user  "champu",  then  vsftpd  will  apply  the  settings  in  the  file `/etc/vsftpd/user_conf/champu`  for  the duration of the session.

So let see how to configure this. 

Configure `/etc/vsftpd/vsftpd.conf` as below, note this configuration uses system users.

[crayon]# No ANONYMOUS users allowed
anonymous_enable=NO

# Allow 'local' users with WRITE permissions (0755)
local_enable=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
xferlog_enable=YES

# Turn on verbose vsftpd log format. The default vsftpd log file is /var/log/vsftpd.log
log_ftp_protocol=YES
# connect_from_port_20=YES
#
pam_service_name=vsftpd

chroot_local_user=YES
listen=YES

# if you don't want to give access to user use /home/$USER then you can 
# use following param to use different home dir
# local_root=/home/vsftpd/$USER
# user_sub_token=$USER

user_config_dir=/etc/vsftpd/vsftpd_user_conf 
[/crayon]

Now let's create configuration for `testuser`

[crayon]mkdir -p /etc/vsftpd/vsftpd_user_conf/testuser[/crayon]

add entries which you want apply only for `testuser`, So edit `/etc/vsftpd/vsftpd_user_conf/testuser` :

[crayon]# Disable delete commands
cmds_denied=DELE,RMD[/crayon]

Once you configure the setting, you need to reload vsftpd service to apply the changes. 

[crayon]# /etc/init.d/vsftpd reload[/crayon]

Now Test

User will get permission denied error :
[crayon]ftp> ls -rlht
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 500      500          2048 Jan 11 13:13 test.db
226 Directory send OK.
ftp> del test.db
550 Permission denied.
[/crayon]

also you can check vsftpd logs :
[crayon]
[root@test-server ~]# tail -f /var/log/vsftpd.log
Sat Jan 11 18:43:05 2014 [pid 2846] [testuser] FTP response: Client "10.0.0.1", "226 Directory send OK."
Sat Jan 11 18:43:10 2014 [pid 2846] [testuser] FTP command: Client "10.0.0.1", "DELE test.db"
Sat Jan 11 18:43:10 2014 [pid 2846] [testuser] FTP response: Client "10.0.0.1", "550 Permission denied."[/crayon]

I hope this help you, if you face any issue then please feel free to comment below.