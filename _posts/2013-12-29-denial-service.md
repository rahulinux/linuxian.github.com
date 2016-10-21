---
ID: 1128
post_title: 'Basic Security Advice :: Denial-of-service'
author: Rahul Patil
post_date: 2013-12-29 14:18:03
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2013/12/29/denial-service/
published: true
dsq_thread_id:
  - "2079853201"
---
<span style="font-size: large;"><b>What is DDOS Attack ?</b></span>

Here is Simple Definition from <a href="http://en.wikipedia.org/wiki/Denial-of-service_attack" target="_blank">Wikipedia</a>
<blockquote>In <a title="Computing" href="http://en.wikipedia.org/wiki/Computing" target="_blank">computing</a>, a <b>denial-of-service attack</b> (<b>DoS attack</b>) or <b>distributed denial-of-service attack</b> (<b>DDoS attack</b>) is an attempt to make a machine or network resource unavailable to its intended <a title="User (computing)" href="http://en.wikipedia.org/wiki/User_%28computing%29" target="_blank">users</a>. Although the means to carry out, motives for, and targets of a DoS attack may vary, it generally consists of efforts to temporarily or indefinitely interrupt or suspend <a title="Network service" href="http://en.wikipedia.org/wiki/Network_service" target="_blank">services</a> of a <a title="Host (network)" href="http://en.wikipedia.org/wiki/Host_%28network%29" target="_blank">host</a> connected to the <a title="Internet" href="http://en.wikipedia.org/wiki/Internet" target="_blank">Internet</a>.</blockquote>
<div></div>
In short : A DoS (Denial of Service) attack is simply an attempt by an attacker to exhaust the resources available to a network, application or service so that genuine users cannot gain access.

<span style="font-size: large;"><b>How it's Works ?</b></span>
<div>

Well as you understood basic fundamental how it's work, So lets <b>Dig Under the Hood</b>.

</div>
<div>

We need to understand the packet flow ( Technically -  TCP Handshake ), there are lot of steps, but I will cover only which are related to this topic, other will lead to confuse and waste of time.

</div>
<div>1. Suppose IPaddr : 10.0.0.5 ( Client )  initiating connection to ==&gt; 10.0.0.100 ( Server )</div>
<div>2. While initiating connection Client will Open Random Port greater than 1023 , client will send <b>SYN</b> packet to the server.</div>
<div>

3. After receiving  <b>SYN</b> packet, Server will send <b>SYN-ACK, </b>but in back-end server will allocate some memory for further communication.

</div>
The Normal Process as below :
<p style="text-align: center;"><a href="http://linuxian.com/wp-content/uploads/2013/12/syn.jpg"><img class="alignnone size-medium wp-image-1129" alt="syn" src="http://linuxian.com/wp-content/uploads/2013/12/syn-300x218.jpg" width="300" height="218" /></a></p>
So if client sends lots of <b>SYN</b> packet to the server using any kind of tools or scripts, So what will happen in server end, it will respond <b>SYN-ACK, </b>then allocating some space in memory then again sending <b>SYN-ACK, </b>So if there is no any prevention for limiting the <b>SYN </b>packet then server will busy to responding the that client.
<div><span style="font-size: large;"><b>How to Check DDOS on Server ?
</b></span></div>
<div>You can Simply check using <b>netstat</b> tool we need check connection state</div>
<div>from <b>netstat</b> man pages :</div>
<div>
<blockquote>
<blockquote><i>  SYN_RECV</i>
<i>            A connection request has been received from the network.</i></blockquote>
</blockquote>
So you can use following command to list IP which are sending continues SYN packets

</div>
[crayon]netstat -antp | awk '/SYN_RECV/{ split($5,a,":"); print a[1] | "sort"}' | uniq -c [/crayon]

<span style="font-size: large;"><b>How to Perform DDOS Attack ?</b></span>

<strong>WARNING: Please Do not try this at work, because your IP might be get blacklist.</strong>
<div>There are server tools available for the same, even we can create from scratch if we understand the concept.</div>
<div>Here is the example of General Tools which are mostly used and comes with distro like backtrack.</div>
[crayon]hping3 -i u1 -S -p PORT SERVER-IP --rand-source[/crayon]
<div>the hping tool will use random source FAKE public Ip's you can check in netstat.
<div></div>
<div><span style="font-size: large;"><span style="font-size: large;"><b>How to Prevent the DDOS Attack ?</b></span></span></div>
<div>Using Open Source, Kernel Level</div>
[crayon]iptables -A INPUT -i eth0 -p tcp --dport 80 -m state --state NEW -m recent --set --name HTTP[/crayon]

[crayon]iptables -A INPUT -i eth0 -p tcp --dport 80 -m state --state NEW -m recent --update --seconds 60 --hitcount 80 --rttl --name HTTP -j DROP[/crayon]
<div>Or you can configure IDS like Snort to detect the DDOS traffic
<div>Or as this prevention mostly configured on Firewall/Gateway End, So you may need to check :<span style="font-size: large;"><b>
</b></span>Hardware firewalls OR UTM (Unified Threat Management) security platform solutions like :<span style="font-size: large;">
</span><b>
Fortinet </b>
<a href="http://www.fortinet.com/products/fortiddos/" target="_blank">topic related to DDOS</a></div>
<div></div>
<div><strong>Reference Links</strong></div>
<div>
<ul>
	<li><a href="http://serverfault.com/questions/424179/simple-way-to-block-ddos-by-number-of-requests">Simple* way to block DDoS by number of requests</a></li>
</ul>
</div>
<div>
<ul>
	<li><a href="http://rockdio.org/ayudatech/how-to-stop-small-ddos-attacks-some-basic-security-advice/">How to stop Small DDOS attacks (Some basic security advice)</a></li>
</ul>
</div>
</div>
<div><b>Caution :- This tutorial is for educational purpose. I am not responsible for any illegal action.</b></div>
</div>