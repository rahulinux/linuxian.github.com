---
ID: 511
post_title: >
  Subversion with SSH + Trac Setup
  Mini-tutorial
author: Rahul Patil
post_date: 2012-12-01 00:00:46
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/12/01/subversion-with-ssh-trac-setup-mini-tutorial/
published: true
dsq_thread_id:
  - "2078920068"
---
Subversion with SSH + Trac Setup Mini-tutorial

This Document has been created for users who know basic know-how of SVN
<strong>Why this is required?</strong>
We have software Projects, were more developers work on same project from different locations, in default SVN setup, the developers detail like who made changes in repositories are not reflected, also it never asks for a password for committing any changes. We have to run svnserv service in SVN Server with default SVN, so for better security, we are going to configure svn+ssh+trac on Ubuntu. .Svn+ssh://. which uses the SSH protocol to secure communication between your Subversion client and the server. A technician with sufficient management rights on SVN Server will be able to configure/use all the steps discussed in this article, to easily accomplish the desired goals.
<strong>Description</strong>
We will Configure Subversion Over SSL which can be accessed via svn+ssh:// protocol. we will use system users for authentication, We are also going to Setup Access control list in SVN using .authz. database . Also we will setup Trac , which will enable us to browse SVN in WebGUI.

<strong>Key Benefit</strong>
. Secure SVN using SSH tunnel without running SVN Service On SVN Server
. Manage Access Control list in SVN
. It will Give Proper Info like Which User has Changes/Updated in Project
. Browse Repositories Via Trac in HTTPS Mode
. Password Less using SSH-Keys, it will create Public/Private Key
User can simply Download Own Public key from his Home directory
. Integrate Trac With SVN
. It will use and Authenticate System Users using PAM Module from Trac(Https)
. <a href="http://partystartx.tk/old/ho//download/svnuseradd.sh"> svnadduser.sh</a> script which will make work easy , as below :

1. This Script Created for add svnuser into your system
2. You can also add existing user into svngroup using this script
3. it will create SSH private and public keys for every each users ( Password less login)
4. it will gives you ssh pubic key in users own home directory, which can be load from windows machine using pageant
5. it will add "svn ssh-tunnel", "svn log file" for each user
6. tested and working in Ubuntu
7. Default User password "username@1234"
8. Default Shell /bin/sh with no-pty means user will not able to loing via putty,winscp it's only for svn and trac(https)

<strong>Install and Configure SVN</strong>

[crayon lang="bash"]

aptitude install subversion

[/crayon]

<strong>Initializing the repository</strong>

[crayon lang="bash"]

mkdir -p /svn/repositories/project1

[/crayon]

[crayon lang="bash"]

svnadmin create /svn/repositories/project1

[/crayon]

<strong>Generating a working copy</strong>

[crayon lang="bash"]

mkdir myproject
mkdir myproject/{tags,branches,trunk}
svn import -m "My files" myproject file:///svn/repositories/project1

[/crayon]

<strong>Create Group called .svngroup., so all users belong to this group can access svn</strong>

[crayon lang="bash"]

groupadd svngroup

[/crayon]

Download Below script for <a href="http://partystartx.tk/old/ho//download/svnuseradd.sh">svnuseradd.sh</a>

[crayon lang="bash"]

wget "http://partystartx.tk/old/ho//download/svnuseradd.sh" -O svnuseradd.sh

[/crayon]

Add New Users or Existing Users using svnuseradd script

[crayon lang="bash"]

bash svnuseradd.sh satish
bash svnuseradd.sh manish
bash svnuseradd.sh prathamesh
bash svnuseradd.sh shashank
bash svnuseradd.sh sudipta
bash svnuseradd.sh unni
bash svnuseradd.sh munna
bash svnuseradd.sh mahadev
bash svnuseradd.sh santosh

[/crayon]

Note: - all default password is .username@1234. you can reset it later
You can check user is added into svngroup or not

[crayon lang="bash"]

id santosh

# Output :
uid=1008(santosh) gid=1006(svngroup) groups=1006(svngroup)

[/crayon]

<strong>Give Permission svngroup to access your repositories</strong>

[crayon lang="bash"]

chgrp -R svngroup /svn/repositories/
chmod -R g+w /svn/repositories/

[/crayon]

<strong>Now you can list your project using SSH</strong>

[crayon lang="bash"]

svn list snv+ssh://@&lt;svn_server_ip_address&gt;/svn/repositories/project1

[/crayon]

<strong>Setup ACL in SVN</strong>
Open "/svn/repositories/project1/conf/svnserve.conf" and add below lines under "[general]" tag

[crayon lang="bash"]

auth-access = write
anon-access = none
realm = Safesquid_Realm

[/crayon]

Open "/svn/repositories/project1/conf/svnserve.conf" and add Configure as below

[crayon lang="bash"]

[groups]
devloper = manish,santosh,satish,prathmesh,rahul
support = unni,rahul,shashank
sales = sudipta
admin = root

[/]
* = r
@admin = rw

[/branches]
* = r
@devloper = rw

[/tags]
* =
@devloper = rw

[/trunk]
* =
@devloper = r

[/crayon]

Above ACL tested and working, you create your path based ACL, just refer below link for more details
http://svnbook.red-bean.com/en/1.7/svn.serverconfig.pathbasedauthz.html
About Trac
Trac is a lightweight project management tool that is implemented as a web-based application, written in the Python programming language. It emphasizes ease of use and low ceremony
First make sure all needed packages are installed.

[crayon lang="bash"]

aptitude install trac apache2 ssl-cert libapache2-mod-python

[/crayon]

Generate the certificate
Create a certificate which is valid for one year.

[crayon lang="bash"]

mkdir /etc/apache2/ssl

make-ssl-cert /usr/share/ssl-cert/ssleay.cnf /etc/apache2/ssl/apache.pem

[/crayon]

Configure Trac

[crayon lang="bash"]

mkdir -p /var/www/trac
trac-admin /var/www/trac initenv

[/crayon]

Note: - Press Blank Enter in all Option , we can change it later
Configure Trac to use SVN ACL files
Open "/var/www/trac/conf/trac.ini" with your favorite editor and do below changes

[crayon lang="bash"]

[components]
# Refrence Link
#
tracopt.perm.authz_policy.* = enabled
svnauthz.svnauthz.svnauthzplugin = enabled

# Refrence Link
# add below Options Under "[trac]" Tag
authz_file = /svn/repositories/project1/conf/authz

# Refrence Link
permission_policies = AuthzSourcePolicy, DefaultPermissionPolicy, LegacyAttachmentPolicy

repository_dir = /svn/repositories/project1

[/crayon]

Give Permission .www-data. which apache user to access ./var/www/trac.

[crayon lang="bash"]

chown www-data:www-data -R /var/www/trac/

[/crayon]

Install Pam Module for apache to authenticate Unix system users

[crayon lang="bash"]

apt-get install libapache2-mod-authnz-external pwauth

[/crayon]

Enable Module

[crayon lang="bash"]

ln -fs /etc/apache2/mods-available/authnz_external.load
/etc/apache2/mods-enabled/authnz_external.load

[/crayon]

Open "/etc/apache2/sites-enabled/trac" file and do changes as below

[crayon lang="bash"]

NameVirtualHost *:443

ServerName &lt;fqdn_of_your_server&gt;
AddExternalAuth pwauth /usr/sbin/pwauth
SetExternalAuthMethod pwauth pipe
SSLEngine On
SSLCertificateFile /etc/apache2/ssl/apache.pem
SSLProtocol all
SSLCipherSuite HIGH:MEDIUM

SetHandler mod_python
PythonInterpreter main_interpreter
PythonHandler trac.web.modpython_frontend
PythonOption TracEnv /var/www/trac
PythonOption TracEnvParentDir /var/www/trac
PythonOption TracUriRoot /trac
PythonOption TracEnv /var/www/trac
PythonOption TracLocale en_US.UTF8
PythonOption PYTHON_EGG_CACHE /tmp
Order allow,deny
Allow from all


AuthType Basic
AuthBasicProvider external
AuthExternal pwauth
AuthName "SVN Auth"
Require valid-user
[/crayon]

Enable trac site in apache

[crayon lang="bash"]

ln -fs /etc/apache2/sites-available/trac /etc/apache2/sites-enabled/trac

[/crayon]

Now Restart apache Service and browse http://&lt;server_ip&gt;/trac

[crayon lang="bash"]

/etc/init.d/apache2 restart

[/crayon]

Refers links
http://wiki.univention.de/index.php?title=Trac_for_UCS_3.0
http://trac.edgewall.org/ticket/9976
http://trac-hacks.org/wiki/TracSvnAuthzPlugin
http://trac.edgewall.org/wiki/TracFineGrainedPermissions#AuthzPolicy
http://svnbook.red-bean.com/en/1.7/svn.serverconfig.pathbasedauthz.html
http://www.pyxzl.net/store/authnz.php