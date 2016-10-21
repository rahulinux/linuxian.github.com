---
ID: 1263
post_title: Trouble-shooting Tips | Inode full
author: Rahul Patil
post_date: 2014-03-22 14:29:56
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2014/03/22/trouble-shooting-tips-inode-full/
published: true
dsq_thread_id:
  - "2484492942"
---
<div>
<div>

<span style="font-size: large;"><b>Error</b></span>

&nbsp;
<blockquote>“No space left on device”</blockquote>
<span style="font-size: large;"><b>Discussion </b></span>

</div>
<span style="color: #0000ff;">
</span>On of friend facing disk space issue, he gave me ssh login, and when I checked `<b>df -i</b>` I notice Only / slash root partition is there and it's inode was 100% full. this is how bad who don't care partitions should be separate like /var /opt /usr /tmp /home, so it can be easy to diagnose which partition taking too much inode. but now it was bit hard to me to check in which directory taking too many inode.

</div>
<div></div>
<div>When your partition size is full (in <i>df -h</i> )  then you can easily check using</div>
<div>[crayon]du -sh * | grep G[/crayon]</div>
<div></div>
<div>

So it will display directory which are taking space in GB's

Or you can find bigger files using find command

</div>
<div>[crayon]find / -type f -size +1024M[/crayon]</div>
<div>

But in my case it was different only one partition and need to find which  directory taking lot of inodes.

Then one simple command helped me to find this, it's <span style="font-size: large;"><b>ls</b></span>
<div>

How ?

</div>
<div>

Let's see it's practically

</div>
First we need to create lot's of file anywhere in file-system, so first check how much inode available :

</div>
<div></div>
<div>[crayon][root@uat ~]# df -i
Filesystem                  Inodes IUsed  IFree IUse% Mounted on
/dev/mapper/vg_uat-lv_root  558624 28582 530042    6% /
tmpfs                       127550     1 127549    1% /dev/shm
/dev/sda1                   128016    38 127978    1% /boot[/crayon]</div>
<div></div>
<div>OK 6% Used, So let's create gazillions of files to fill this</div>
<div>[crayon]ruby -e '1.upto(530042) { |n| File.open("test_#{n}", "w") {} }'[/crayon]</div>
<div></div>
<div>Note I could simply use touch file_{1..530042}, but it's gave me an error <i>-bash: /usr/bin/touch: Argument list too long</i>, that's why I use ruby here, it took <b>real 0m21.568s</b> time to create 530042 files, see result of df -i</div>
<div></div>
<div>[crayon][root@uat inode_test]# df -i
Filesystem                  Inodes  IUsed  IFree IUse% Mounted on
/dev/mapper/vg_uat-lv_root  558624 558624      0  100% /
tmpfs                       127550      1 127549    1% /dev/shm
/dev/sda1                   128016     38 127978    1% /boot[/crayon]</div>
<div></div>
<div><span style="font-size: large;"><b>Solution</b></span></div>
<div></div>
<div>[crayon]find / -xdev -type f | cut -d "/" -f 2 | sort | uniq -c | sort -n[/crayon]</div>
<div></div>
<div>

<b>Command break down </b>
<ul>
	<li><b>-xdev</b>             exclude  /proc /sys /dev</li>
	<li><b>-type f</b>            find only files recursively</li>
	<li><b>cut -d "/" -f 2  </b>select 2nd column from path eg. from /home/champu  select chmpu</li>
	<li><b>sort</b>                we need to pass sorted output to uniq because uniq command need sorted input</li>
	<li><b>uniq -c</b>           <b>uniq</b> with count second column</li>
	<li><b>sort -n</b>            lastly sort 1st filed of inode low to max</li>
</ul>
<div>

<a href="http://paste.ubuntu.com/7124088/" target="_blank"><b>Output </b></a>

</div>
<div>But on server I haven't used find command I use simply <b>ls -Rf /</b> command and pressed CTRL + C where it get stuck then I found the directory where one application created lot of temp files.</div>
<div></div>
<div><strong>How to remove multiple files ?</strong></div>
<div>Below command will delete all files in current directory</div>
<div>[code]find . -mindepth 1 -type f -delete[/code]

</div>
<div></div>
<div></div>
<span style="font-size: large;"><b>Additional Info
</b></span>
<ul>
	<li>It's also possible that deleting files will not reduce the inode count if the files have multiple hard links</li>
	<li>You can delete a directory entry but, if a running process still has the file open, the inode won't be freed.</li>
</ul>
If you have different experience/solution with this scenario then don't forget to share..

</div>
<div></div>