---
ID: 84
post_title: use color in script
author: Rahul Patil
post_date: 2012-07-03 00:18:54
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/07/03/use-color-in-script/
published: true
dsq_thread_id:
  - "2078958634"
---
When I write a bash script (.sh) that will have to output log-text indicating the user what the script is doing, I use color – it’s just the easiest way to highlight the indications for the rest of the printout.

To do so, I normally define 2 or 3 helper functions, that will just output in colors the text passed in as argument $1:

[sourcecode language="bash"]
shw_grey () {
echo $(tput bold)$(tput setaf 0) $@ $(tput sgr 0)
}

shw_norm () {
echo $(tput bold)$(tput setaf 9) $@ $(tput sgr 0)
}

shw_info () {
echo $(tput bold)$(tput setaf 4) $@ $(tput sgr 0)
}

shw_warn () {
echo $(tput bold)$(tput setaf 2) $@ $(tput sgr 0)
}
shw_err ()  {
echo $(tput bold)$(tput setaf 1) $@ $(tput sgr 0)
}

shw_grey &quot;Blablabla nothing interesting...&quot;
shw_norm &quot;I reclaim some attention, but still within normality &quot;
shw_info &quot;I'm an info message  I stand out&quot;
shw_warn &quot;We must fix our economy system&quot;
shw_err  &quot;Crash Boom Bang&quot;
[/sourcecode]

Note that each function will output only the first argument “$1?, and so a single text-chunk should be given as argument :)

If this was useful to you, or if you have a better trick, send a comment :)