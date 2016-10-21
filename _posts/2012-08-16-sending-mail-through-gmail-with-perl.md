---
ID: 115
post_title: Sending Mail Through Gmail with Perl
author: Rahul Patil
post_date: 2012-08-16 01:21:55
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/08/16/sending-mail-through-gmail-with-perl/
published: true
dsq_thread_id:
  - "2080125031"
---
Every Linux admin required to get an email notification. such as, when the script finished or completed OK, if any service crash and to get custom email messages sent from the script itself. Now we are going to look at how you can send email from Perl.

First Install Required Packages.

For CentOs/RHEL

[sourcecode language="bash"]
yum install perl-Net-SSLeay perl-Digest-HMAC.noarch perl-IO-Socket-SSL.noarch rpm-libs -y
rpm -ivh http://pkgs.repoforge.org/perl-Net-SMTP-TLS/perl-Net-SMTP-TLS-0.12-1.el5.rf.noarch.rpm
[/sourcecode]

For Ubuntu

[sourcecode language="bash"]
aptitude perl libnet-smtp-tls-perl
[/sourcecode]

Code:

[sourcecode language="bash"]
use Net::SMTP::TLS;
my $mailer = new Net::SMTP::TLS(
'smtp.gmail.com',
Hello   =&gt;      'smtp.gmail.com',
Port    =&gt;      587,
User    =&gt;      'username',
Password=&gt;      'password');
$mailer-&gt;mail('from@domain.com');
$mailer-&gt;to('to@domain.com');
$mailer-&gt;data;
$mailer-&gt;datasend(&quot;Sent from perl!&quot;);
$mailer-&gt;dataend;
$mailer-&gt;quit;
[/sourcecode]