---
ID: 38
post_title: Upload file using curl and bash script
author: Rahul Patil
post_date: 2012-07-01 13:51:28
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/07/01/upload-file-using-curl-and-bash-script-2/
published: true
dsq_thread_id:
  - "2096004253"
---
<pre class="crayon-selected">We always use Winscp to download data from server, but some time our client didn't give
us ssh access they give us VPN access to server, then we use curl for data upload and
download. and the same can be done through following script.
[sourcecode language="bash"]
#!/bin/bash
THIS_SCRIPT=`readlink -f $0`

UPLOAD () {
curl –progress-bar -F myfile=@$@ ‘http://upload.filefactory.com/upload.php’ 1&gt; /tmp/upload.txt &amp;&amp; STATUS=$?

if [ $STATUS -eq 0 ]; then
printf “%sn” “File Uploaded Successfully”
printf “%sn” “Please download from:” “http://www.filefactory.com/file/$(cat /tmp/upload.txt)/n/$@”
else
printf “%sn” “error while uploading”
fi
}

if [ $# -eq 0 ]; then
printf “%sn” “Please Specify Filename”
printf “%sn” “Usage:”
printf “t%-10sn” “$THIS_SCRIPT filaname”
exit 1
fi

UPLOAD $*

[/sourcecode]