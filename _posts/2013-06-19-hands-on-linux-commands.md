---
ID: 610
post_title: Hands-On Linux Commands
author: Rahul Patil
post_date: 2013-06-19 16:57:35
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2013/06/19/hands-on-linux-commands/
published: true
---
This is the first of my Linux commands FAQ series where I want to list some useful shell scripts/commands, keyboard shortcuts , some may be already known to you and some may not be, but nonetheless I feel its my duty to share some not so trivial things to search and find that easily sometimes, also I needed a quick reference of these which may be useful for Linux System Admin, coders including me in future, so here we go.

<strong>1. Place the argument of the most recent command on the shell</strong>

When typing out long arguments, such as

[crayon language="plain"] tail -f /var/log/messages [/crayon] 

You can put that argument on your command line by holding down the <kbd>ALT</kbd> key and pressing the period <kbd>.</kbd> or by pressing <kbd>ESC</kbd> then the period <kbd>.</kbd>

For example: tail -f <kbd>ALT</kbd> + <kbd>.</kbd>
this would put '/var/log/messages' as my argument. Keeping pressing <kbd>ALT</kbd>+<kbd>.</kbd> to cycle through arguments of your commands starting from most recent to oldest. This can save a ton of typing.

<strong>2. Man Pages</strong>

Command "man" is your friend, Before googling , you should check man pages to solve issue yourself. here is simple Example, if want to understand File System Hierarchy, then simply use :

[crayon lang="plane"]  man hier[/crayon] 

<strong>3. SSH</strong>

If ServerB is Not accessible but you can access ServerA , and ServerA can access ServerB , then you can use .

[crayon lang="plane"]  ssh -t ServerA  ssh  ServerB [/crayon] 

Directly ssh to ServerB that is only accessible through ServerA

<strong>4. RPM, DPKG</strong>

If you want to Check which file belongs to which package.

In RHEL/CentOS

[crayon lang="plane"]  rpm -qf   /path/of/file [/crayon] 

In Ubuntu

[crayon lang="plane"]  dpkg -S  /path/of/file [/crayon] 

Note that if package installed using source code, then it will never show , only apply for files those are installed through package manager.


<strong>5. Get absolute Path of file .</strong>

This trick very useful in Shell Script.

if you want to check absolute path of file which is in your current directory :

[crayon lang="plane"]  readlink  -f  filename [/crayon] 

If you want to get absolute path of script it self

[crayon lang="plane"]  This_Script=$(readlink -f $0 )[/crayon] 

<strong>6. Backup/Rename File </strong>
If you want to Backup file with current date name.
[crayon lang="plane"]  cp  filename{,-bkp-$(date +%F)} [/crayon] 
If you want to rename file 
[crayon lang="plane"]  mv filename{,-old} [/crayon] 

<strong>7. Fast, built-in pipe-based data sink</strong>
[crayon lang="plane"]  Yourcommand   |:[/crayon] 
This is shorter and actually much faster than >/dev/null (see sample output for timings)

<strong>8. Save Command in History Without Executing it. </strong>
Type your command and Simple Press <kbd>ALT</kbd>+<kbd>#</kbd>

<strong>9. Search and Execute Recent commands from your history</strong>
Simply Press <kbd>Ctrl</kbd>+<kbd>r</kbd> and type some word to search your Command, Simply do Next using <kbd>Ctrl</kbd>+<kbd>r</kbd>  to cycle through commands. 
<wbr></wbr>

<strong>10. Fast Execute recent command.</strong>
Suppose you have Open file using vim then you save file and run some other command, now just run [crayon lang="plane"]  !vim[/crayon]  this will execute last command start with vim from your history. 



If you want to share any command line tips & tricks, just post in comments.