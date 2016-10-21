---
ID: 1298
post_title: 10 Practical Example of Grep
author: Rahul Patil
post_date: 2014-03-31 15:44:41
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2014/03/31/10-practical-example-grep/
published: true
dsq_thread_id:
  - "2564327110"
---
<div>
<div>
<div>
<div>Grep is very Good and handy tool which we use daily basis, now we will see some more example which will make you more comfortable and productive while using Grep

<b><span style="font-size: large;">1. <a href="http://partmaps.org/era/unix/award.html#cat" target="_blank">The Useless Use of Cat Award</a></span></b></div>
</div>
<div></div>
<div>Don't use</div>
<div></div>
<div>
<div><span style="font-size: large;"><b>[code]cat filename | grep blabla[/code]</b></span>

</span></div>
<div></div>
</div>
If you are using, you must read <a href="http://partmaps.org/era/unix/award.html#cat" target="_blank">The Useless Use of Cat Award</a>

</div>
Instead of, You can use

<div><span style="font-size: large;"><b>[code]grep blabla filename [/code]</b></span></div>

<div><span style="font-size: large;"><b>2. Search for some process </b></span>

<div>You might be using</div>

<div><span style="font-size: large;"><b>[code]ps -ef | grep -v grep | grep cron[/code]</b></span>
</div>
<div>1st grep for exclude grep then grep for cron process</div>
<div>Instead of you can use</div>
<div><span style="font-size: large;"><b>[code language="css"]ps -ef | grep &quot;[[c]]ron&quot;[/code]</b></span></div>

By putting the brackets around the letter and quotes around the string you search for the regex, which says, "Find the character 'c' followed by '<b>ron</b>'."
<div>

<span style="font-size: large;"><b>3. </b></span><span style="font-size: large;"><b>PID of a process</b></span></div>
<div>If you want Only PID then you can use <b>pgrep</b></div>
<div></div>
<div><span style="font-size: large;"><b>
[code]pgrep mongod[/code]</b></span></div>


<div>OR</div>
<div><span style="font-size: large;"><b>[code]pgrep -f /path/of/binary[/code]</b></span></div>


<div>OR</div>
<div></div>
<div><span style="font-size: large;"><b>[code]pgrep -f /path/of/processfile[/code]</b></span></div>

</div>
<div>

</div>
<br></br>
<div><span style="font-size: large;"><b>4. Grep Standard Error Stream ( stderr )
</b></span></div>



<div>Back in time I have had to get Stream of given video file using ffmpeg , but the thing that I waste time is that binary which was sending output as stderr and grep only finding the result in stdout.</div>

<div>If you are trying below example which will not work :</div>
<div></div>
<div><span style="font-size: large;"><b>
[code]ffmpeg -i limitless_HD.m4v | grep &quot;Stream&quot;[/code]</b></span>

</div>
<div></div>
<div>So if you want to grep stderr too then you need to use :</div>
<div></div>
<div><span style="font-size: large;"><b>[code]ffmpeg -i limitless_HD.m4v 2&gt;&amp;1 | grep &quot;Stream&quot;[/code]
</b></span>
</div>
<div></div>
<div>It's basically just <a href="http://www.cyberciti.biz/faq/redirecting-stderr-to-stdout/" target="_blank">redirecting  standard error stream to standard output stream</a>.
<span style="font-size: large;"><b>

</b></span></div>
<div><span style="font-size: large;"><b>5. Most Common, exclude  Comments and Blank lines </b></span></div>
<div></div>
<div><span style="font-size: large;"><b>[code]grep -vE &quot;^$|^#&quot; /etc/squid/squid.conf[/code]</b></span></div>


<ul>
	<li>-v Invert the sense of matching</li>
	<li>^ <i> </i>character matches the beginning of the line</li>
	<li><i><code>|</code>  </i>matches lines containing either of those two expressions</li>
	<li>-E is same as <b>egrep</b></li>
</ul>
Similarly we can use -E switch for finding error,debug log messages in logs
<div><span style="font-size: large;"><b>
[code]grep -E '(reject|warning|error|fatal|panic):' /var/log/maillog[/code]</b></span></div>



<span style="font-size: large;"><b>6. Do you want to save each result in different file ?</b></span>
<div><span style="font-size: large;"><b>
[code]ps -ef | tee &gt;( grep ^rahul &gt;/tmp/rahul_ps ) &gt;( grep ^root &gt;/tmp/root_ps)[/code]</b></span>


</div>
<div>The main work is done by <b>tee</b> command and <b>&gt;( stuff )</b> , it's  <a href="http://en.wikipedia.org/wiki/Process_substitution" target="_blank">Process substitution,
</a></div>
<div>You can do anything like count lines with matching <b>&gt;(wc -l) </b>



</div>
<div><span style="font-size: large;"><b>7. Print 2 lines Before match and 2 lines After match </b></span></div>
<div>So as I knew you might be using</div>
<div></div>
<div><span style="font-size: large;"><b>[code]nl /etc/passwd | grep -B 2 -A 2 &quot;halt:&quot;[/code]</b></span>
</div>
<div></div>
<div>What about <b>-C ?
</b>it does the same, see</div>
<div></div>
<div><span style="font-size: large;"><b>[code]nl /etc/passwd | grep -C 2 &quot;halt:&quot;[/code]</b></span>

</div>
<div></div>
<div>Even more precise version is Just use <b>-n</b> where n is line of context</div>
<div><span style="font-size: large;"><b>[code]nl /etc/passwd | grep -2 &quot;halt:&quot;[/code]</b></span>

</div>
<div></div>
<div>If you want print same number of lines then <b>-n</b> and <b>-C</b> fine otherwise go for <b>-A -B</b>



</div>
<div><span style="font-size: large;"><b>8. Search Specific String in specified path</b></span></div>
<div></div>
<div><span style="font-size: large;"><b>[code]grep -sIr --include=\*.{conf,cfg} -w &quot;mytextosearch&quot; /etc/[/code]
</b></span>

</div>

<div><b>Explanation </b>:
<ul>
	<li><b>-I</b>  Skipp checking binary files</li>
	<li><b>-s</b>  Suppress error messages such as Permission Denied</li>
	<li><b>-r</b>  Search Recursively ( searches sub-directories )</li>
	<li><b>--include</b> check only in given extension, if you not specified it will look into all files</li>
	<li>Additionally if you want to print only file-name then just use <b>-l</b> (<code> The letter ell.</code> ) switch to grep</li>
</ul>
</div>
<div><span style="font-size: large;"><b>9. Grep PCRE<i> </i><a href="http://en.wikipedia.org/wiki/Perl_Compatible_Regular_Expressions" target="_blank">[  Perl Compatible Regular Expressions ]</a></b></span><i>
</i></div>
<div></div>
<div>The simple Example would be find tab in input file</div>
<div></div>
<div><span style="font-size: large;"><b>[code]grep -P '\t' inputfile[/code]</b></span>

</div>
<div></div>
<div>Little  bit complex one</div>
<div></div>
<div><span style="font-size: large;"><b>[code]echo FOO | grep -P '(?i)foo'[/code]
</b></span>

</div>
<div>
<b>Explanation  : </b>
<ul>
	<li><b>-P </b>  Activate perl-regexp for grep (a powerful extension of regular extensions)</li>
	<li><b>(?i) </b> Turns on case insensitive mode.</li>
</ul>
Matches IP Address

<b>With Extended grep ( Without PCRE )</b>

<div><span style="font-size: large;"><b>[code]grep -Eo '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' /etc/hosts[/code]

</b></span></div>

<b>With PCRE</b>

<div><span style="font-size: large;"><b>[code]grep -Po '\d{1,3}.\d{1,3}.\d{1,3}.\d{1,3}' /etc/hosts[/code]</b></span></div>



<b>Explanation :</b>
<ul>
	<li><b>-o </b>    print only matched</li>
	<li><b>\d</b>     means digit, you can find more basic Regex from <a href="http://www.cheatography.com/davechild/cheat-sheets/regular-expressions/" target="_blank">this page</a>.</li>
	<li><b>{1,3} </b>find at least 1 or 3 matched</li>
</ul>
But <b>-P </b>switch has some limitation such as, it does not handle matches on multi line, So there is new tool called <a href="http://unixhelp.ed.ac.uk/CGI/man-cgi?pcregrep" target="_blank"><b>pcregrep</b></a>.

To illustrate the same have look at below example.

Input Sample File

[code]line1 bla bla 
line2 some text 
line3 bla blamyword
line4 some text 
line5 bla bla 
line6 some text[/code]



Now what is the requirement is matches from word <b>bla</b> to word <b>myword</b>.
<div><span style="font-size: large;"><b>
[code]pcregrep -M 'bla.*(\n|.)*.myword' inputfile[/code]</b></span></div>


Output

[code]line1 bla bla 
line2 some text 
line3 bla bla myword[/code]



<span style="font-size: large;"><b> </b></span>

<span style="font-size: large;"><b>10. Positive Lookahead </b></span>

It's basically used for matched inside bracket, or after some match, let's see some practical example

I want to match anything inside brackets
<div><span style="font-size: large;"><b>
[code]ifconfig eth0 | grep --color -P '(?&lt;=\().*(?=\)\s+)'[/code]
</b></span></div>

You can see output in color which matches. so you can use<b> -o</b> to extract the matches.

<b>Explanation </b>:
<ul>
	<li><b><code>(?&lt;=\()</code> :</b> This is a <em>positive lookbehind</em>, the general format is <code>(?&lt;=foo) bar</code> and that will match all cases of <code>bar</code> found right after <code>foo</code>. In this case, we are looking for an opening parenthesis, so we use <code>\(</code> to escape it.</li>
	<li><b><code>(?=\)\s+)</code> : </b>This is a <em>positive lookahead</em> and simply matches the closing parenthesis and \s+ means whitespace .</li>
</ul>
</div>
Read More
<ul>
	<li><a href="http://www.regular-expressions.info/lookaround.html">lookaround </a></li>
	<li><a href="http://www.rexegg.com/regex-disambiguation.html">PCRE Regex </a></li>
	<li><a href="http://beyondgrep.com/">Beyond Grep</a></li>
</ul>
<div></div>
<div></div>
I hope it help you.</div>