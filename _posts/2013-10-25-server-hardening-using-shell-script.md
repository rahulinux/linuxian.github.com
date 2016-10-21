---
ID: 910
post_title: Server Hardening using Shell Script
author: Rahul Patil
post_date: 2013-10-25 01:14:15
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2013/10/25/server-hardening-using-shell-script/
published: true
dsq_thread_id:
  - "2081736441"
---
<h2><strong>Why this script is required ?</strong></h2>
if you want enhancing server security through a variety of means which results in a much more secure server operating environment, then script is useful. you can easily add/modify parameter, in Configuration Section in Script.

You can Download Script from <a href="https://github.com/rahulinux/compliance">GitHub</a>
<h4><span style="font-size: medium;"><strong>Sample Demo <a href="http://linuxian.com/wp-content/uploads/2013/12/security.png"><img class="aligncenter size-full wp-image-1050" alt="security" src="http://linuxian.com/wp-content/uploads/2013/12/security.png" width="564" height="634" /></a></strong></span></h4>
&nbsp;
<h1><strong>How it will work ?</strong></h1>
It will follow the server hardening process which is mention in "server hardening parameters".
<ul>
	<li>Backup of any configuration file which are going to edit as <code>config-bkp-current-date</code>.</li>
	<li>Comment old parameters and apply the new one.</li>
	<li>Make configuration file read-only using chattr.</li>
</ul>
<h1></h1>
<h1><strong>Prerequisites</strong></h1>
<ul>
	<li>The tools/commands are used in script, which almost available in all *nix destro, however you can make sure following list: <code>awk</code>,<code>sed</code>,<code>mktemp</code>,<code>chkconfig</code>,<code>stat</code>.</li>
	<li>Tested and working in RHEL/CentOS 5.x</li>
</ul>
<h1></h1>
<h1><strong>List of Security Rules:</strong></h1>
<ul>
	<li>Disable direct root login</li>
	<li>Deny all except ip associated with stdin using TCP Wrapper</li>
	<li>Other remaining will update soon on GitHub</li>
</ul>
<h1></h1>
<h1><strong>What else?</strong></h1>
If you have any questions or suggestions, you want to share anything else with me, feel free to drop me an e-mail . I appreciate any feedback, including constructive (and polite) criticism, improvement suggestions, questions about usage (if the documentation is unclear).

Thanks for my friend <em>Vasanta Koli.</em>. for  involving me<em>
</em>