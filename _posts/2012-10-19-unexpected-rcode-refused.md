---
ID: 236
post_title: unexpected RCODE REFUSED
author: Rahul Patil
post_date: 2012-10-19 12:16:56
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/10/19/unexpected-rcode-refused/
published: true
dsq_thread_id:
  - "2081641795"
---
Today i faced issue with caching DNS server as below:
<div><a href="http://partystartx.tk/old/ho//wp-content/uploads/2012/10/bind_issue.png"><img class="alignleft size-full wp-image-239" title="bind_issue" src="http://partystartx.tk/old/ho//wp-content/uploads/2012/10/bind_issue.png" alt="" width="950" height="213" /></a>This issue happen because ISP DNS Server down, as you can see highlighted ISP DNS Server IP.</div>
to check instantly,  we can use dig command as below :

[sourcecode language="bash"]

dig www.linuxian.com @180.149.63.3


[/sourcecode]

above command will send query to "180.149.63.3" then you will find the issue

To Solve this issue we can change forwarders in named.conf. and reload bind service.

Note:- Do not restart DNS service is Production because if you did that , then it will flush cache entry from memory , always use reload or HUP single to bind service, it will just reload configuration file.