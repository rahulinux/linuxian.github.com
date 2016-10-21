---
ID: 1153
post_title: 'Bash Script :: To compressed one day old files'
author: Rahul Patil
post_date: 2013-12-30 12:11:43
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2013/12/30/bash-script-compressed-one-day-old-files/
published: true
dsq_thread_id:
  - "2081599577"
---
I recently had to compressed one day old db archive files in my DB Server, there are only last two days of files there and some old files, so I've to do with only yesterday files, so I made a little script to help me do it.

This script use `find` command to get only one day old files and compressed it in current directory. it will also take care if there is wight space in file name. 

[crayon] 
# <pre class="lang:shell decode:false" > copy from below line
#!/usr/bin/env bash

# This Script will compress One day old file means yesterday files
set -e
nday=1                          # set how many days old file
ftype="*.dbf"                   # set file type which should be compressed
lookinto="."                    # path of files
format="%-10s\t%10s\t%-10s\n"   # print fmt because don't repeat :P

print_finfo(){
        local fname=$1
        [[ -f $fname ]] || { echo "$1 does not exists"; exit 1; }
        stat -c "%s %Z %n"  "${fname}" |
        awk -v fmt=$format '{printf fmt, ($1/1024/1024)" MB",strftime("%D",$2),$3}'
}

printf "$format" Size Date Filename
while IFS= read -r -d '' f;
do
        printf "%s\t%s\n" "compressing $f"
        print_finfo $f
        gzip -v "${f}"
        printf "%s\t%s\n" "Size status After compression $f"
        print_finfo ${f}".gz"
        echo ""
done &lt; &lt;( find $lookinto -mindepth 1 -daystart -mtime $nday   -iname "$ftype" -print0 )
## </pre> EOF
[/crayon]
<strong>
Sample Output </strong>

[code]
[oracle@dbserver archive]$ bash compress_1day_old.sh
Size                  Date      Filename

compressing ./1_abcd_xxx.dbf
278.975 MB        12/29/13      ./1_abcd_xxx.dbf
./1_abcd_xxx.dbf:         86.0% -- replaced with ./1_abcd_xxx.dbf.gz
Size status After compression ./1_abcd_xxx.dbf
39.0607 MB        12/30/13      ./1_abcd_xxx.dbf.gz

compressing ./2_abcd_xxx.dbf
278.976 MB        12/29/13      ./2_abcd_xxx.dbf
./2_abcd_xxx.dbf:         77.5% -- replaced with ./2_abcd_xxx.dbf.gz
Size status After compression ./2_abcd_xxx.dbf
62.683 MB         12/30/13      ./2_abcd_xxx.dbf.gz

[/code]

I hope someone find it useful, if you face any issue just comment below :)