---
ID: 1276
post_title: Parse Data using AWK|Ruby
author: Rahul Patil
post_date: 2014-03-22 15:06:44
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2014/03/22/parse-data-using-awkruby/
published: true
dsq_thread_id:
  - "2484504441"
---
Recently I help to parse top,ps output data to csv using awk and ruby. data wasn't complex. see following awk and ruby script which might be help you to do the same task.

<strong>Input Data</strong>

[code]top - 11:48:02 up 8 days, 15:49, 7 users, load average: 0.05, 0.05, 0.00
 Tasks: 1 total, 0 running, 1 sleeping, 0 stopped, 0 zombie
 Cpu(s): 1.7%us, 1.9%sy, 0.1%ni, 96.4%id, 0.0%wa, 0.0%hi, 0.0%si, 0.0%st
 Mem: 198319908k total, 28256968k used, 170062940k free, 767436k buffers
 Swap: 0k total, 0k used, 0k free, 23313752k cached

 PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND
 28983 root 10 -10 373m 46m 27m S 2.0 0.0 0:26.10 ras

 top - 12:03:02 up 8 days, 16:04, 7 users, load average: 1.24, 1.37, 0.81
 Tasks: 1 total, 0 running, 1 sleeping, 0 stopped, 0 zombie
 Cpu(s): 6.2%us, 2.7%sy, 0.1%ni, 91.0%id, 0.0%wa, 0.0%hi, 0.0%si, 0.0%st
 Mem: 198319908k total, 29182584k used, 169137324k free, 768524k buffers
 Swap: 0k total, 0k used, 0k free, 24202460k cached

 PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND
 28983 root 10 -10 387m 60m 29m S 79.0 0.0 12:17.57 ras

 top - 12:33:03 up 8 days, 16:34, 7 users, load average: 0.56, 0.72, 0.73
 Tasks: 1 total, 0 running, 1 sleeping, 0 stopped, 0 zombie
 Cpu(s): 5.1%us, 2.5%sy, 0.1%ni, 92.3%id, 0.0%wa, 0.0%hi, 0.0%si, 0.0%st
 Mem: 198319908k total, 31074584k used, 167245324k free, 770688k buffers
 Swap: 0k total, 0k used, 0k free, 26041508k cached

 PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND
 28983 root 10 -10 396m 69m 29m S 57.6 0.0 31:32.00 ras[/code]

<strong>Expected Output</strong>

[code]top,11:48:02,1.7%us,1.9%sy,28256968k,28983,373m,46 m,27m,2.0,0.0,ras
 top,12:03:02,6.2%us,2.7%sy,29182584k,28983,387m,60 m,29m,79.0,0.0,ras
 top,12:33:03,5.1%us,2.5%sy,31074584k,28983,396m,69 m,29m,57.6,0.0,ras[/code]

<strong>Using AWK</strong>

[code lang="ruby"]
awk '{
        if (/^top/) {
                time = $3
        }

        if (/^Cpu/) {
                # remove comma from end
                gsub(&quot;,$&quot;, &quot;&quot;, $2) 
                gsub(&quot;,$&quot;, &quot;&quot;, $3)
                usr = $2
                sys = $3
        }

        if (/^Mem:/) {
                mem = $2
        }

        if (/^PID/) {
                getline  # go to next line 
                pid = $1
                virt = $5
                res = $6
                shr = $7
                cpu = $9
                mem2 = $10
                procname = $NF
                OFS=&quot;,&quot; # out put filed separator change to comma 
                print &quot;top&quot;,time,usr,sys,mem,pid,virt,res,shr,cpu,mem2,procname
        }
}'  inputfile  
[/code]

<strong>Ruby Script</strong>


[code lang="ruby"] 
#!/usr/bin/ruby -w
require 'csv'

unless ARGV[0] and File.file?(ARGV[0])
  puts &quot;\nUsage: #{__FILE__} input_file\n\n&quot;
  exit 1
end

input_file = ARGV[0]
store_records = [ &quot;top&quot; ]
pid = false
csv_report = &quot;report.csv&quot;

File.open(input_file,'rb') do |infile|
  infile.each_line do |line|

    case line

      when /^top/
        # get time
        store_records &lt;&lt;  /[0-9]+:[0-9]+:[0-9]*/.match(line)

      when /^Cpu/
        # get us%; &amp;amp; sy%;
        store_records += [ /[0-9\.]+%us/.match(line),
                           /[0-9\.]+%sy/.match(line) ]
      when /^Mem/
        # get memory
        store_records &lt;&lt; /(Mem:\s)(\d*k)/.match(line)[2]

      when pid
        # let's write to csv 
        store_records += line.split.values_at(0,4,5,6,8,9,-1)
        CSV.open(csv_report,&quot;ab&quot;) do |csv|
          csv &lt;&lt; store_records
        end
        store_records = [ &quot;top&quot; ]
        pid = false

      when /^PID/
        pid = true
  
    end
    
  end

end

puts &quot;CSV report has been created : #{csv_report}&quot;[/code]