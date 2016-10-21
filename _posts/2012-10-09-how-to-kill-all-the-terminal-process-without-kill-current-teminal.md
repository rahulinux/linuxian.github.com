---
ID: 199
post_title: >
  How to kill all the terminal process
  without kill current teminal
author: Rahul Patil
post_date: 2012-10-09 17:16:14
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/10/09/how-to-kill-all-the-terminal-process-without-kill-current-teminal/
published: true
dsq_thread_id:
  - "2134780963"
---
<blockquote>I am log in 3 different terminals.
How to kill all the terminal process without my present bash shell</blockquote>
This can be done via below command:

[sourcecode language="bash"]

w | awk '! /^ 00/ &amp;&amp; ! /^USER/ &amp;&amp; !/w$/ &amp;&amp; ! /up/ {print &quot;/dev/&quot;$2}' | xargs -n1  fuser -k[/sourcecode]