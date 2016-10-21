---
ID: 362
post_title: >
  Download Entire Album photos from Google
  Plus using Wget
author: Rahul Patil
post_date: 2012-11-05 21:04:43
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/11/05/download-images-from-google-plus-using-wget/
published: true
dsq_thread_id:
  - "2079699314"
---
Below Script will help you to download JPEG images from plus.google.com

[sourcecode language="shell"]

#!/bin/bash
# Get link from below link
# echo &quot;exmaple :- https://plus.google.com/photos/+SreeSanth/albums&quot;

# if directly link not pass to script then it will prompt for link
if [ -z $1 ]; then
 read -p &quot;Enter GooglePlus Link :- &quot; Link
[ -z ${Link} ] &amp;&amp; exit 1
fi

# store albums in below temp path
Temp_album=&quot;/tmp/albums&quot;

# download album
wget -qc &quot;${Link}&quot; -O ${Temp_album}

# extract images links from albums using Regex
Download_Links=( `grep -oP &quot;https://[a-zA-Z0-9-.]+.[a-zA-Z]{2,3}(:[a-zA-Z0-9]*)?/?([a-zA-Z0-9-._?,'/\+&amp;amp;%$#=~])*.jpg&quot; ${Temp_album} | awk -F&quot;/&quot; '!_[$7]++'`)

# check Last file number &amp; then set count value
Last_file=&quot;$(ls -1 *.jpeg| sort -t &quot;_&quot; -k2 -n| tail -1)&quot;

# if last file is exists then start counter from that last file
if [ -f ${Last_file} ]; then
 count=&quot;$( echo ${Last_file::-5} | cut -d&quot;_&quot; -f2 )&quot;
else
 count=0
fi

# actual download process start from here
for download in ${Download_Links[@]}
do
wget ${download} -O photo_${count}.jpeg
let count=count+1
done

# make empty album file
&gt; ${Temp_album}
[/sourcecode]