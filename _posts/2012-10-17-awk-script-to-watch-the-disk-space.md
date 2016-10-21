---
ID: 221
post_title: AWK script to watch the disk space
author: Rahul Patil
post_date: 2012-10-17 11:59:38
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/10/17/awk-script-to-watch-the-disk-space/
published: true
dsq_thread_id:
  - "2079409982"
---
df displays the amount of disk space available on the file system containing each file name argument. If no file name is given, the space available on all currently mounted file systems is shown. Read man page of df if you are new to df command.

[sourcecode language="bash"]
#!/usr/bin/awk -f

######################################################
#                disk_usage.awk                      #
#            written by Rahul Patil                  #
#              &lt;www.linuxian.com&gt;                    #
#                  Oct 17 2012                       #
#                                                    #
#               Monitor Disk Usage                   #
#   This Script is to be executed on a server        #
#     where you want to monitor disk space           #
#    just make it executable and place in to         #
#               /etc/cron.daily/                     #
#         Note :- mail service should be             #
#            configured in your server               #
#                                                    #
######################################################

BEGIN{

ARGV[1]=&quot;/tmp/disk_usage.tmp&quot;;
ARGC = 2;

# set admin email so that you can get email
ADMIN=&quot;root@localhost&quot;

# set alert level 90% is default
threshold=85;

date=getenv(&quot;date&quot;);

hostname=getenv(&quot;uname -n&quot;);

getenv(&quot;df -Ph &gt; /tmp/disk_usage.tmp&quot;);
}

function getenv ( cmd )  {
	cmd | getline tmp_var;
	close(cmd);
	return tmp_var;
}

# filter and remove
!(/Filesystem/){

			used=$5;
			if ( int(used) &gt;= threshold ) {

			print &quot;Running out of space &quot;,$1,&quot; &quot;,  used,&quot; on &quot;, hostname, &quot;as on :&quot;, date
			print &quot;mail -s 'Alert: Almost out of disk space,&quot; $1, used,&quot;'&quot;, ADMIN, &quot;&gt;/dev/null&quot; | &quot;sh&quot;

			close(&quot;sh&quot;)

		}

}

[/sourcecode]
<h3>Setup Cron job</h3>
Save and install script as <a href="http://www.cyberciti.biz/faq/how-do-i-add-jobs-to-cron-under-linux-or-unix-oses/">cronjob</a>. Copy script to /etc/cron.daily/ (script <a href="http://bash.cyberciti.biz/monitoring/monitor-diskhttps://sites.google.com/site/linuxrahulpatil/documents/disk_usage.awk?attredirects=0&amp;d=1-space-alert.bash.php">downolad link</a>)
<code></code>

[sourcecode language="bash"]

# cd /etc/cron.daily/

# wget &quot;https://sites.google.com/site/linuxrahulpatil/documents/disk_usage.awk?attredirects=0&amp;d=1&quot; -O disk_usage.awk

# chmod +x /etc/cron.daily/disk_usage.awk

[/sourcecode]

Short Code by Wateal(he is master in awk &amp; automation task)

[sourcecode language="bash"]
#!/usr/bin/awk -f
# Author :- Wateal
BEGIN{
ADMIN=&quot;root@localhost&quot;
threshold=20
&quot;date&quot; | getline date
&quot;uname -n&quot; | getline hostname

	while(&quot;LC_ALL=C df -Ph&quot; | getline){
		used=$5
			if($1 != &quot;Filesystem&quot; &amp;&amp; int(used) &gt;= threshold){
			print &quot;Running out of space: &quot;$1,used&quot; used on &quot;hostname&quot; as on: &quot;date
			print &quot;mail -s &quot;Alert: Almost out of disk space: &quot; $1,used&quot; used&quot; &quot;ADMIN&quot; &gt;/dev/null&quot; | &quot;sh&quot;
			close(&quot;sh&quot;);
			}
	}
}
[/sourcecode]

Reference link

http://www.cyberciti.biz/tips/shell-script-to-watch-the-disk-space.html