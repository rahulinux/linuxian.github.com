---
ID: 553
post_title: >
  Telnet by hostname without modifying
  /etc/hosts file Without root user
author: Rahul Patil
post_date: 2013-02-11 21:02:29
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2013/02/11/telnet-by-hostname-without-modifying-etchosts-file-without-root-user/
published: true
dsq_thread_id:
  - "2078918833"
---
<blockquote>I have several devices in my network and I have a file with the name / ip of each device, is there any way i can execute a telnet to that file and execute the line I want so i can access the device I need?

e.i
hosts
1.1.1.1 main
2.2.2.2 smpt

telnet hosts main</blockquote>
<ul>
        <li>Method 1</li>
</ul>
You can use alias as below , just keep in your ~/bashrc file

[crayon lang="bash"]
alias telnet_router 'telnet 192.168.1.1'
[/crayon]



<ul>
        <li>Method 2</li>
</ul>

You can use below function
[crayon lang="bash"]
function do_telnet () {
# just copy and past this fucntion ~/.bashrc file.
# then run "source ~/.bashrc" command to reload function or just logout and login
# then just run "do_telnet router" command to connect router or host
#

[ $# -eq 0 ] && echo "Please give hostname to connect destination" || {
. ~/.hosts  # path of host file
# in this file keep entry as below
# router=192.168.1.1   # without hash sign(#)
# switch=192.168.1.100

eval telnet $$@
}

}

[/crayon]

<ul>
        <li>Method 3</li>
</ul>
[crayon lang="bash"]
echo "router 10.0.0.1" >> ~/my_hosts
echo "export HOSTALIASES=~/my_hosts" >> ~/.bashrc
source ~/.bashrc
[/crayon]

Then just use

[crayon lang="bash"]
telnet router
[/crayon]