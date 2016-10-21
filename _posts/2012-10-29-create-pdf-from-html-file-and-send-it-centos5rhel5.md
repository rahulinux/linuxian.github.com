---
ID: 249
post_title: 'Create PDF from HTML file and Send it [CentOs5/RHEL5]'
author: Rahul Patil
post_date: 2012-10-29 19:01:53
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/10/29/create-pdf-from-html-file-and-send-it-centos5rhel5/
published: true
dsq_thread_id:
  - "2078922059"
---
<a href="http://www.htmldoc.org/">Htmldoc </a>and <a href="http://caspian.dotconf.net/menu/Software/SendEmail/">sendEmail </a>both are Open-source tools , below script is created using this tools , i hope it may be usefully for you.

[sourcecode language="bash"]
 #!/usr/bin/env sh
#################################################
#	 			html2pdf_send.sh                #
#			 written by Rahul Patil				#
#			   &lt;www.linuxian.com&gt;				  #
#												#
# 		Generate html to pdf and send mail		#
# 		  only for CentOs5 and RHEL5			#
#		it will check and automatically			#
#		download and  install sendEmail			#
#			  htmldoc application				#
#	    using sendEmail it's also store			#
#		loggin infomormation to log file		#
#											    #
#################################################

# Define sender's detail  email ID
 From_Mail=&quot;your_email@gmail.com&quot;

# Sender's Username and password account for sending mail
Sndr_Uname=&quot;${From_Mail}&quot;
Sndr_Passwd=&quot;your_password&quot;

# Define recepient's email ID
To_Mail=&quot;to_send@gmail.com&quot;

# Define CC to (Note: for multiple CC use ,(comma) as seperator )
# CC_TO=&quot;loginrahul90@gmail.com&quot;

# Define mail server for sending mail [ IP:PORT or HOSTNAME:PORT ]
# RELAY_SERVER=&quot;ganpatuniversity.ac.in:25&quot;
RELAY_SERVER=&quot;smtp.gmail.com:587&quot;

# Subject
Subject=&quot;HTML to PDF Reports Test&quot;

# output file will be create in current dir where this scipt is execulted
Output_file=&quot;$(date +%F)_report.pdf&quot;
Output_file_link=&quot;/tmp/todays_report.pdf&quot;

# sendEmail download link
download_sendEmail=&quot;http://caspian.dotconf.net/menu/Software/SendEmail/sendEmail-v1.56.tar.gz&quot;

# path of latest support file
send_file=&quot;${Output_file_link}&quot;

# Mail Body
function MSG() {
 cat &lt;&lt;_EOF
 Dear Sir,
 Please find the attachment of PDF File
_EOF
 }

# store loggin information in below log file
Log_File=&quot;/var/log/sendmail.log&quot;

# input html fil
Input_File=&quot;$2&quot;

# check sendmail dir exists or not if not check create it
Log_dir=&quot;$(dirname ${Log_File})&quot;
if [ ! -d &quot;${Log_dir}&quot; ]; then
 mkdir &quot;${Log_dir}&quot;
fi

# below function will check the sendEmail binary if not exists then
# it will download sendEmail binary to appropriate location

check_sendmail() {

if [ ! -x &quot;/usr/bin/sendEmail&quot; ]; then
 echo &quot;sendEmail not installed&quot;
 echo &quot;Installing sendEmail...&quot;
 sleep 1s
 wget -cnd &quot;${download_sendEmail}&quot; -O /tmp/sendemail.tar.gz &gt;/dev/null 2&gt;&amp;1
 tar -xzf /tmp/sendemail.tar.gz  --wildcards  *sendEmail
 install --mode=744 sendEmail*/sendEmail /usr/bin/
 yum install perl-Net-SSLeay perl-Net-SMTP-SSL -y
fi
 }

Show_help() {

cat &lt;&lt;_EOF
 Usage  : $0 &lt;options&gt;    &lt;input_html_file&gt;

Options:
 -g    to generate only reports
 -gs   to generate and send report

_EOF
 }

check_input_file() {
if [ -z &quot;${Input_File}&quot; ]; then
	 echo &quot;ERROR: Input file not found&quot;
	 exit 1
	 elif [ &quot;${Input_File: -5}&quot; != &quot;.html&quot; ]; then
	 echo &quot;ERROR: Input file is not a html&quot;
	 exit 1
fi
 }

Html_doc=&quot;/usr/bin/htmldoc&quot;
check_html_doc() {
if [ -z &quot;${Html_doc}&quot; ]; then
	 echo &quot;Html doc not installed&quot;
	 echo &quot;installing htmldoc&quot;
	 wget -cnd http://pkgs.repoforge.org/htmldoc/htmldoc-1.8.27-2.el5.rf.i386.rpm
	 rpm -ivh htmldoc-1.8.27-2.el5.rf.i386.rpm
	 # rpm -ivh wkhtmltopdf-0.9.9-1.el5.i386.rpm
	 [[ $? -eq 0 ]] || { echo &quot;RPM's not found in current dir&quot;; exit 1; }
fi
 }

Generate_pdf () {
check_input_file
check_html_doc
&quot;${Html_doc}&quot; --webpage -f $Output_file $Input_File  --bodycolor white --size universal --textcolor black --pagemode document --pageeffect effect
ln -fs &quot;`readlink -f ${Output_file}`&quot; &quot;${Output_file_link}&quot;
echo &quot;PDF file Succussfully Created  &quot;`readlink -f ${Output_file}`&quot;&quot;
}

Send_Mail() {
check_sendmail
/usr/bin/sendEmail -v -f ${From_Mail}
					 -t ${To_Mail} -u &quot;${Subject}&quot;
					 -m `MSG`
					 -xu &quot;${Sndr_Uname}&quot;
					 -xp &quot;${Sndr_Passwd}&quot;
					 -o tls=auto
					 -a &quot;${Output_file_link}&quot;
					 -s &quot;${RELAY_SERVER}&quot;
					 -cc &quot;${CC_TO}&quot;
					 -l &quot;${Log_File}&quot;
 }

Main() {

case $1 in
		 -g)
		 Generate_pdf
		 ;;
		 -gs)
		 Generate_pdf
		 Send_Mail
		 ;;
		 *)
		 Show_help
		 ;;
esac
}

Main $*

[/sourcecode]