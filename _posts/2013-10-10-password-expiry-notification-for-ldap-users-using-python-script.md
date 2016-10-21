---
ID: 872
post_title: >
  Password Expiry Notification for LDAP
  Users using Python Script
author: Rahul Patil
post_date: 2013-10-10 13:01:14
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2013/10/10/password-expiry-notification-for-ldap-users-using-python-script/
published: true
dsq_thread_id:
  - "2078916951"
---
If you are managing LDAP Server and you want to notify users to change there password via email then <a href="https://github.com/rahulinux/ldapPasswdNotify.py/blob/master/ldapPasswdNotify.py" target="_blank">this script</a> is useful for you.

Before using this script, you need following things :
<ul>
	<li>By Default there is no restriction for Password Policy, we need to add schema for this. So you can refer following Link to Add <strong>pwdpolicy</strong> schema into your LDAP Server.</li>
</ul>
<ul>
	<li>
<h2><a href="http://partystartx.tk/old/ho//2013/09/26/how-to-implement-pwdpolicy-openldap/" target="_blank"><span><strong>How to implement pwdpolicy OpenLdap</strong></span></a></h2>
</li>
</ul>
<ul>
	<li>This Script is Using Python <strong>LDAP API, </strong>to perform faster LDAP Query and making it more simple. So you will need to install package <em><strong>python-ldap</strong></em>. which can simply install using Apla <strong>Yum</strong></li>
</ul>
<div>
<ul>
	<li>This Script is Tested in Python Version 2.4.3, if you are using Old Version, then this script will not work, you need to some modification to get job done.</li>
</ul>
</div>
<div></div>
<div>You can Download Updated Script, Directly from <a href="https://github.com/rahulinux/ldapPasswdNotify.py" target="_blank">GitHub</a></div>
<div></div>
<div><span>Thanks for My Friend <em>Sharad</em>, who get me <em>involved</em> in this issue<strong> </strong>and motivate me to create this small script. </span></div>
<div></div>
<div></div>
<strong>
Suggestions, comments, feedback and referrals are highly appreciated.
</strong>

[sourcecode lang="python"]
#!/usr/bin/env python
# Author :- Rahul Patil

import ldap
import datetime
import subprocess

#-----------------------------------------------------------------
# Provide Ldap DN Details to Perform Ldap Search using anonymouse
#-----------------------------------------------------------------
domain_name = 'linuxian.com'
email_domain = 'linuxian.com' # if your mail domain is different this will becom user@abc.com
connect_dc = 'ldap://localhost:389'

# Define number of days to send warning alert
# suppose you have define 14 days and pwd_max_age age is 45 days
# then it will notify users when 14 days remain to expire password
# it will send email to that user until user update the new password
pwd_warn_days = 14
# default password expire in  day as per ldap ppolicy
pwd_max_age = 45 # password should change in days
mail_subject = &quot;Ldap Password Expiry Details&quot;

mail_body = &quot;&quot;&quot;
Dear %s,
     Your password will expire in %s day(s). We're sorry for the
inconvenience, but we need you to change your password, your
last password date is %s.

Best Regards,
Linux Admin
&quot;&quot;&quot;

def get_user_details():
    	&quot;&quot;&quot; Return UID and Last Password Details &quot;&quot;&quot;
    	# Create bind dn eg. dc=example,dc=com
    	bind_dn = ','.join([ 'dc=' + d for d in domain_name.split('.') ])
    	l = ldap.initialize(connect_dc)
    	# Perform Ldap Search
    	return  l.search_s(
    			bind_dn,
    			ldap.SCOPE_SUBTREE,
    			'(uid=*)',['uid','pwdChangedTime']
    		)

def check_password_expiry():
	   &quot;&quot;&quot;Check each user ExpiryWarning
        if it more thans WarningDays then it will send Emails
        to that particuler user&quot;&quot;&quot;
      	for k,v in ldap_users:
		        uid = ''.join(v['uid'])
             	if 'pwdChangedTime' not in v:
			        # means password not changed yet from user creation date
			         pwd_expire_in_days = 0
             	  	 #print &quot;User &quot; + uid + &quot;  not Updated Password&quot;
             	try:
               	  	  l = ''.join(v['pwdChangedTime'])
             	except:
             	    	pass

             	if 'pwdChangedTime' in v:
             		# Date Calculation to get number of days remains to change password
		            d1 = datetime.date.today()
             		d2 = datetime.date(int(l[0:4]),int(l[4:6]),int(l[6:8]))
			        # to get number of days password has updated
             		days_of_password_change = (d1 - d2).days
             		d2 = d2.strftime('%d, %b %Y')
               		pwd_expire_in_days = pwd_max_age - days_of_password_change

             	if pwd_expire_in_days &lt;= pwd_warn_days:
			        p = subprocess.Popen(['mail', '-s', mail_subject, uid + '@' + email_domain],
						stdin=subprocess.PIPE)
			        p.communicate(mail_body % (uid,pwd_expire_in_days,d2))

if __name__ == '__main__':
        ldap_users = get_user_details()
        check_password_expiry()

[/sourcecode]