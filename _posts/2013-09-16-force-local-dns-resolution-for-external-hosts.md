---
ID: 739
post_title: >
  Force local DNS resolution for external
  hosts
author: Rahul Patil
post_date: 2013-09-16 03:19:53
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2013/09/16/force-local-dns-resolution-for-external-hosts/
published: true
dsq_thread_id:
  - "2078917141"
---
In this post we will see How to Forcefully resolve your internal IPaddress of all external hosts. The steps that we are going to see was inspired by <a href="http://unix.stackexchange.com/questions/87812/force-local-dns-resolution-for-external-hosts/87818#87818">Question </a>on Unix &amp; Linux Stackexchange community .

Edit  named.conf and configure as below

[crayon lang="shell" ]
zone "." IN {
type master;
file "/etc/bind/root.db";

};
[/crayon]

Then configure  "/etc/bind/root.db" as below

[php]

$ORIGIN .
$TTL 1D
@     IN     SOA  @ none. ( 0 1D 1H 1W 3H );
.     IN     NS   @
@     IN     A   10.0.0.1
*     IN     A   10.0.0.1

[/php]

Replace IPAddress 10.0.0.1 with yours which you want to resolve. after configuration above settings Once restart bind service and check from client side.

Example Output

[php]
root@router:~# dig facebook.com

; &lt;&lt;&gt;&gt; DiG 9.8.1-P1 &lt;&lt;&gt;&gt; facebook.com
;; global options: +cmd
;; Got answer:
;; -&gt;&gt;HEADER&lt;&lt;- opcode: QUERY, status: NOERROR, id: 31375
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 1

;; QUESTION SECTION:
;facebook.com.                  IN      A

;; ANSWER SECTION:
facebook.com.           86400   IN      A       10.0.0.1

;; AUTHORITY SECTION:
.                       86400   IN      NS      .

;; ADDITIONAL SECTION:
.                       86400   IN      A       10.0.0.1

;; Query time: 3 msec
;; SERVER: 10.0.0.1#53(10.0.0.1)
;; WHEN: Mon Sep 16 03:12:06 2013
;; MSG SIZE  rcvd: 73

[/php]