---
ID: 1556
post_title: Monitor Storm Topology
author: Rahul Patil
post_date: 2016-10-07 11:04:58
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2016/10/07/monitor-storm-topology/
published: true
dsq_thread_id:
  - "5204052726"
---
I have created one monitoring plugin for Storm topology checks. this plugin will give you the following things:
<ul>
	<li>  If error found in spout or bolt then it will stout the error on screen</li>
	<li>  if topology not emitted from last 3 hours then it will stdout the warning on sceen</li>
</ul>
&nbsp;


[code]

Usage:
check_storm_topology.py  --ip &lt;nimbus_server_ip&gt; [--port=p] [--topology=topology ...]

Options:
--ip                     specify ip address of nimbus.
--port=8080              [default: 8080].
--topology=topologyname  topology name [default: all]

Examples:
check_storm_topology.py --ip 10.22.10.150
check_storm_topology.py --ip 10.22.10.150 --port 8080
check_storm_topology.py --ip 10.22.10.150 --port 8080 --topology mytopology
check_storm_topology.py --ip 10.22.10.150 --topology mytopology1 --topology mytopology2

[/code]


&nbsp;

You can find the updated script from <a href="https://github.com/rahulinux/check_storm_topology">this page</a>.