---
ID: 562
post_title: >
  Decimal to Binary Conversion using Bash
  Script
author: Rahul Patil
post_date: 2013-03-21 00:27:06
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2013/03/21/decimal-to-binary-conversion-using-bash-script/
published: true
dsq_thread_id:
  - "2078918550"
---
Decimal to Binary Conversion using Bash Script

[code lang="bash"]
#!/bin/bash
# PurPose:- decimal to binary converter
# Author :- Rahul Patil&amp;lt;http://www.linuxian.com&gt;

for ((i=32;i&gt;=0;i--)); do
        r=$(( 2**$i))
        Probablity+=( $r  )
done

[[ $# -eq 0 ]] &amp;&amp; { echo -e &quot;Usage n t $0 numbers&quot;; exit 1; }

echo -en &quot;DecimalttBinaryn&quot;
for input_int in $@; do
s=0
test ${#input_int} -gt 11 &amp;&amp; { echo &quot;Support Upto 10 Digit number :: skiping &quot;$input_int&quot;&quot;; continue; }

printf &quot;%-10st&quot; &quot;$input_int&quot;

        for n in ${Probablity[@]}; do

                if [[ $input_int -lt ${n} ]]; then
                        [[ $s = 1 ]] &amp;&amp; printf &quot;%d&quot; 0
                else
                        printf &quot;%d&quot; 1 ; s=1
                        input_int=$(( $input_int - ${n} ))
                fi
        done
echo -e
done
[/code]

Download Script Using :
[crayon lang="bash"]
wget http://partystartx.tk/old/ho//download/dec2bin.sh
[/crayon]
Usage :-
[crayon lang="bash"]
bash dec2bin.sh 10 245 13431 1234567890 1200 545416 612635416 4165841 3581 38515871 3584103
[/crayon]

OutPut:-
<a href="http://partystartx.tk/old/ho//wp-content/uploads/2013/03/dec2bin.png"><img class="alignleft size-full wp-image-564" title="dec2bin" alt="" src="http://partystartx.tk/old/ho//wp-content/uploads/2013/03/dec2bin.png" width="389" height="189" /></a>