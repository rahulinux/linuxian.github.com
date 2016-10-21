---
ID: 1253
post_title: Yum reinstall Option
author: Rahul Patil
post_date: 2014-03-22 14:18:09
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2014/03/22/restoring-usrbin-yum-accidental-deletion/
published: true
dsq_thread_id:
  - "2484259522"
---
<p>Yum has this option in newer version ( =&gt; 3.2 ) available in CentOS/RHEL 6.x. It's quite handy if you deleted a file by mistake which is installed by rpm/yum. when I checked man pages, I haven't found the information that I was looking for like, does it change the configuration file, so on ?<br /> <br /><b>man yum</b></p><blockquote><i>reinstall<br />               Will reinstall the identically versioned package as is currently  installed.   This<br />              does  not  work  for  "installonly"  packages,  like Kernels. reinstall operates on<br />              groups, files, provides and filelists just like the "install" command.<br /> </i></blockquote><p><br /><br />So the question is, what are the changes made if you use reinstall option with Yum ? <br /><br />I did some test and want to share with you, hope this help. <br /><br />Suppose you have run <i><b>yum reinstall puppet</b></i></p><ol><li>It's check which version is installed using rpmdb, and try to install same version.</li><li>if configuration file is missing then it install. <br />( I had moved <i><b>"/etc/puppet/puppet.conf"</b></i> to other location but after running reinstall, it's place the default configuration file. )</li><li>if configuration if exists then it's not touch that file.</li></ol><p>I've observe this from test, if you find any different from above then fill free to post or reply on this thread.</p><p><b>Conclusion</b> </p><p>If want original configuration file back, if you want accidentally deleted binary/tools file  back which is installed by rpm/yum then this option is useful.</p>