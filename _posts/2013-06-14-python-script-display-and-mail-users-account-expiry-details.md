---
ID: 581
post_title: >
  Python Script Display and Mail users
  account expiry details
author: Rahul Patil
post_date: 2013-06-14 10:54:24
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2013/06/14/python-script-display-and-mail-users-account-expiry-details/
published: true
dsq_thread_id:
  - "2078918415"
---
Are you Managing N Number of Users and want to check Account Expires and send notification to administrator through Gmail ?

Here is a Python script which will send notification to administrator, which accounts are going to expire in 7 days. You are free to use and modify this script for your work.
Sample Output :
<pre lang="python" line="1">#!/usr/bin/env python
#---------------------------------------------------------
# Name          :- checkUsrAccount_Expiry.py v 0.1 Copyleft (c) :)
# Pupose        :- To check the user account expire status in Linux, unix, BSD etc
# Author        :- Rahul Patil
# Created       :- 12 Jun 2013
# Version       :- 0.1
# License       :- free
#---------------------------------------------------------

# Report `checkUsrAccount_Expiry.py` bugs to loginrahul90@gmail.com

import re
import socket
import smtplib
import datetime

#---------------------------------------------------------
# Specify Mail Login Credencial
#---------------------------------------------------------
host_name = socket.gethostname()
sender = 'your_mail_id@gmail.com'
password = 'yourpassword'
recipient = 'loginrahul90@gmail.com'   # set admin email ID
subject = 'Account Expiry Details of {!r}'.format(host_name)
min_days = 20   # set min days

#---------------------------------------------------------
# Specify SMTP Server
#---------------------------------------------------------
server = 'smtp.gmail.com'
port = 587

#---------------------------------------------------------
body1 = "{:&lt;8s} :: {:&lt;2n} days || Last date :: {:3s}"
body2 = "{:&lt;8s} :: Expired on {:3s}"
msg = []

#---------------------------------------------------------
# Sent Mail
#---------------------------------------------------------
def mailIt(body):
        body = "" + 'rn
<!--8s-->'.join(body) + ""
        headers = ["From: " + sender,
                    "Subject: " + subject,
                    "To: " + recipient,
                    "MIME-Version: 1.0",
                    "Content-Type: text/html"]
        headers = "rn".join(headers)
        session = smtplib.SMTP(server, port)
        session.ehlo()
        session.starttls()
        session.ehlo
        session.login(sender, password)
        print "nnSending Mail..."
        session.sendmail(sender, recipient, headers + "rnrn" + body)
        print "Sent Successfullly !!"
        session.quit()

total_expired_users = 0
total_expring_users = 0
expr_list = {}

#---------------------------------------------------------
#  Convert Unix timestamp to Readable Date/time
#---------------------------------------------------------
def convUnixTime(t):
        return datetime.datetime.fromtimestamp(t*60*60*24)

#---------------------------------------------------------
# Read shadow file and check for account expriry
#---------------------------------------------------------
with open( "/etc/shadow" ) as shadow:
        for aLine in shadow:
                filed = aLine.split(":")
                Ac = filed[7]
                try:
                        Ac = int(Ac)
                        exprdate = convUnixTime(Ac)
                        Ac=1+( exprdate - datetime.datetime.today()).days
                        l=[Ac,exprdate]
                except ValueError:
                        pass
                else:
                        if Ac &lt;= min_days:
                                expr_list[filed[0]]=l
                        if Ac &lt;= 0:
                                total_expired_users += 1
                        else:
                                total_expring_users += 1

#---------------------------------------------------------
# Check Account Expry days and format it Properly Output
#---------------------------------------------------------
def AccountExprDetails():
        msg = []
        t1 = total_expired_users
        t2 = total_expring_users
        if t1 != 0:
                msg.append("====&gt;&gt;Following {!r} Account has been Expired".format(t1))
                index = t1
        else:
                index = -1
        for usr,days in expr_list.iteritems():
                dt=str(days[1])
                if   days[0] &lt;= 0:
                        msg.insert(1,body2.format(usr,dt))
                elif days[0] &lt;= min_days:
                        msg.extend([body1.format(usr,days[0],dt)])
        if t2 != 0:
                msg.insert(index+1,"====&gt;&gt;Following {0} Account will be Expire in {1} Days".format(t2,min_days))
        return msg

msg = AccountExprDetails()

if __name__ == "__main__":
       if len(msg):
                print 'n'.join(msg)
                mailIt(msg)
       else:
                print "No User Account has been Expire"

[code lang="plain"]
====&gt;&gt;Following 3 Account has been Expired
test     :: Expired on 2013-06-01 05:30:00
vinayak  :: Expired on 1970-01-01 05:30:00
ram      :: Expired on 2013-06-14 05:30:00
====&gt;&gt;Following 4 Account will be Expire in 20 Days
suraj    :: 1  days || Last date :: 2013-06-15 05:30:00
javed    :: 1  days || Last date :: 2013-06-15 05:30:00
shyam    :: 1  days || Last date :: 2013-06-15 05:30:00

Sending Mail...
Sent Successfully !!
[/code]

You can find Updated Script from <a href="https://github.com/rahulinux/checkUsrAccount_Expiry.py/blob/master/checkUsrAccount_Expiry.py">this </a>page

Have fun :-)