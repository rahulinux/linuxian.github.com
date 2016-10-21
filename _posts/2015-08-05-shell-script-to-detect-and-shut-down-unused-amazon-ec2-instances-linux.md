---
ID: 1492
post_title: 'Shell Script to Detect and Shut Down Unused Amazon EC2 Instances &#8211; Linux'
author: Rahul Patil
post_date: 2015-08-05 14:37:43
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2015/08/05/shell-script-to-detect-and-shut-down-unused-amazon-ec2-instances-linux/
published: true
dsq_thread_id:
  - "4005596302"
---
<strong>Issue</strong>

We were using CloudWatch Alarm for monitoring EC2 Instance based on CPU utilization but the server were having issue like if Java Process take no load then it will shutdown the Instances automatically.

We observe that server has free Memory more than 85%, so decided to use to triggered shutdown based on Memory unfortunately we haven't found the solution then we ended up using own custom script

<strong>How this script work</strong>

Basically this script check two things 
1. Memory :: if memory free more than 80% 
2. Process :: if no Java process running 
Note: You can change this value as per your requirement, in our case we have to keep Process value to 1 because there is 1 tomcat process keep running.

Then finally script will check this two things in loop with 5 minutes interval and with max tries set in this script. we have set max tries 12 because, in 1 hour it will check every 5 minute interval means 12 time is enough, once this condition match 12 times then it will trigger the shutdown server. 

in this check if any process run or memory usage goes high then it will reset the counter. 

We have schedule this script in cron for every 1 hour  as below:

[code]
#Auto shutdown based on momory and number of java process
00 * * * * /usr/local/bin/auto_shutdown.sh &gt;&gt;/var/log/auto_shutdown.log 2&gt;&amp;1
[/code]

We push this script on multiple server using ansible :) 

[code]
---
  - hosts: all
    gather_facts: false
    vars:
      # Email id's seperated by comma
      email_ids: 'admin1@xyz.com,admin2@xyz.com'
    tasks:
       - name: make sure lockfile is installed
         yum: name=procmail state=installed

       - name: Adding auto shutdown script
         template: src=./auto_shutdown.sh.j2 dest=/usr/local/bin/auto_shutdown.sh

       - file: path=/usr/local/bin/auto_shutdown.sh mode=0755

       - name: setting cron
         cron:
             name='Auto shutdown based on momory and number of java process'
             minute='00'
             job='/usr/local/bin/auto_shutdown.sh &gt;&gt;/var/log/auto_shutdown.log 2&gt;&amp;1'

       - name: make sure cron service is running
         service: name=crond  enabled=yes

[/code]



<strong>The Script
</strong>



[crayon] 
#!/bin/bash

# Purpose :: Auto shutdown trigger based on  memory and Java process
# Date    :: Tue Aug  4 12:36:22 IST 2015

memory_threshold=80      # if 80 percent and more than momory is free
process_threshold=0      # if 0  java process running
server_ip=`hostname`     # will be configure by ansible
email_ids=admin@xyz.com
project='{{ project }}'

max_tries=12  # try max 12 times with wait time interval
wait_time=5m  # wait for 5 min then check again
wait_count=0
# make sure script should be run twice
lock_file=/tmp/auto_shutdown.lock

check_momory(){

      free -m | awk 'FNR == 3 {print int($4/($3+$4)*100)}'

}

check_java_process(){

     ps -ef | awk '{$8 ~ /java/&& n++} END{ print n}'

}

alert(){
   local level=$1
   [[ $# -eq 1 ]] && level=INFO
   local msg=${@//$level/}

   if [[ $level == INFO ]]
   then
         echo "$( date ) :: $level ${msg}"
   else
         echo "$( date ) :: $level ${msg}" | tee >( mail -s "Crawl-v1 auto shutdown alert for $server_ip" $email_ids )
   fi

}

## Check script is already running or not

lockfile -r 0 $lock_file || exit 1
trap " [ -f $lock_file ] && /bin/rm -f $lock_file" 0 1 2 3 13 15

while true
do

   # if momory is less than 5% then check java process

   if [[ $(check_momory) -ge $memory_threshold  ]]
   then
        # if process are running less than $process_threshold then send alert
        if [[ $(check_java_process) -le $process_threshold ]]
        then
            # increment counter
            (( n++ ))

            # check max retries
            if [[ $n -ge $max_tries ]]
            then
                 alert CRITICAL "No Utilization since page 1 hour | Momory $(check_momory)% free | Java Process $(check_java_process)| ServerIP :: $server_ip | Project :: $project"
                 shutdown -h now
                 exit 0
            else
                 alert "Try #$n | Max retry $max_tries"
                 sleep $wait_time
            fi
        else
            alert "$(check_java_process) Java Proccess running | ServerIP :: $server_ip | Project :: $project"
            alert "Loop terminated with #$n retry, because Memory $(check_momory) and Java Process $(check_java_process)"
            exit 0
        fi
   else
        alert "Memory Utilized :: $( check_momory )% | ServerIP :: $server_ip | Project :: $project"
        break
   fi
done

[/crayon]