---
ID: 208
post_title: Setup Basic Ldap Server
author: Rahul Patil
post_date: 2012-10-13 16:24:43
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/10/13/setup-basic-ldap/
published: true
dsq_thread_id:
  - "2084767200"
---
<strong>Introduction:</strong>
The OpenLDAP software provided in Linux distributions implements the Lightweight Directory Access Protocol (LDAP) as a client/server model. LDAP is designed to provide an efficient way to find and manage information. The OpenLDAP software and the packages it interfaces with provide tools to create a Directory Information Tree, a read-mostly database. This document shows you how to setup ldap server &amp;  the authentication services to use LDAP to retrieve needed information as well. the tools give you a representation of the database in a text format, the LDAP Data Interchange Format (LDIF).
Ldap works in Directory information tree see below example.
<a href="http://partystartx.tk/old/ho//wp-content/uploads/2012/10/linuxian_dit.png"><img class="alignleft size-full wp-image-209" title="linuxian_dit" alt="" src="http://partystartx.tk/old/ho//wp-content/uploads/2012/10/linuxian_dit.png" width="633" height="467" /></a>

<strong> Features</strong>
1. Centralized directory of useful information (user accounts, contacts, mail info, etc.)
a. Provides a Directory Information Tree (DIT) - hierarchy of data
b. Objects within the directory are unique and may have attributes (fields)
Note: LDAP DIT typically resembles: domain(DC) then OUs
2. Optimized for Reads
a. You certainly can write to LDAP objects
3. Redundant Configuration
a. Primary, secondary, tertiary, etc. servers
b. Writes take place on the 'primary' and changes are replicated to one or more partners
4. Namespace is similar to DNS: i.e. dc=altnix,dc=com DNS(altnix.com)
5. Supports AUTH encryption of clear-text AUTHs - LDAPS - TCP:636
6. Supports StartTLS over regular TCP:389 LDAP port - secures entire connection
7. Extensible - supports many attributes via schemas - /etc/openldap/schema
8. Data storage is independent of LDAP: default is DBM
9. Provides various tools: slap*(offline|back-end) - use when LDAP is NOT running
10. Provides various tools: ldap*(online|daily admin)
11. Separates binaries for: LDAP daemon (slapd) and replication (slurpd)

<strong>Getting Started Installation of Ldap on CentOs 5.7.</strong>

<strong>Step 1# Install openldap packages</strong>

[crayon language="bash"]

yum install *openldap*

[/crayon]

After installing openldap packages you will find ldap configuration file called slapd.conf

The main ldap server configuration files “/etc/openldap/slapd.conf”.

Edit the ldap server configuration file &amp; Configuration slapd.conf as below:

[crayon language="bash"]

suffix "dc=linuxian,dc=com"
rootdn "cn=admin,dc=linuxian,dc=com"
rootpw {SSHA}jEHotHFsejuV3VdaEP13tb9mRG8iXjkU

[/crayon]

To Create encrypted password use below command

[crayon language="bash"]

[root@ldap1 ~]# slappasswd
New password:
Re-enter new password:
{SSHA}zZEsGZwwqLfY8TQFiKUu5UZ10lAaw8SS

[/crayon]

Save file &amp; Copy DB_CONFIG example file to the proper directory

[crayon language="bash"]
cp /etc/openldap/DB_CONFIG.example /var/lib/ldap/DB_CONFIG
chown R ldap:ldap /var/lib/ldap/*
[/crayon]

Test slapd.conf file using:

[crayon language="bash"]
slaptest –u
[/crayon]

Start the ldap server and set to run at boot

[crayon language="bash"]
service ldap start
chkconfig ldap on
[/crayon]
<p style="text-align: left;" align="center"><strong>Configure Ldap</strong></p>
We need to setup and add ldap as below steps:

First we are going to add root dn that is altnix.com

We are going store all ldif files in “/opt/ldif/”

<strong>Create Root OU (Container) (DC) - using a pre-defined LDIF file</strong>

[crayon language="bash"]
mkdir /opt/ldif/ &amp;&amp; cd /opt/ldif/
[/crayon]

<strong>Create “altnix.ldif” file as below:</strong>

[crayon language="bash"]
dn: dc=linuxian,dc=com
dc: linuxian
objectClass: domain
objectClass: top
[/crayon]

<strong>Create organizational units: “people.ldif”</strong>

[crayon language="bash"]
dn: ou=people,dc=linuxian,dc=com
ou: people
objectClass: organizationalUnit
[/crayon]

<strong>Create first user “first_user.ldif”</strong>

[crayon language="bash"]
dn: uid=champu,ou=people,dc=linuxian,dc=com
uid: champu
cn: champu
objectClass: account
objectClass: posixAccount
objectClass: top
objectClass: shadowAccount
userPassword: test123
shadowLastChange: 15621
shadowMin: 0
shadowMax: 99999
shadowWarning: 7
loginShell: /bin/bash
uidNumber: 502
gidNumber: 502
homeDirectory: /home/champu
[/crayon]

<strong>Create Group “group.ldif”</strong>

[crayon language="bash"]
dn: ou=Group,dc=linuxian,dc=com
ou: Group
objectclass: organizationalUnit
[/crayon]

<strong>Create Group “champu_group.ldif”</strong>

[crayon language="bash"]
dn: cn=champu,ou=Group,dc=linuxian,dc=com
objectClass: posixGroup
objectClass: top
cn: champu
userPassword: test123
gidNumber: 502
[/crayon]

Note: - BLANK LINES ARE NOT ALLOWED IN LDIF FILE

Run below command to add in to ldap database

It will prompt for password.

[crayon language="bash"]
ldapadd -xD "cn=admin,dc=linuxian,dc=com" -W -f linuxian.ldif
ldapadd -xD "cn=admin,dc=linuxian,dc=com" -W -f people_ou.ldif
ldapadd -xD "cn=admin,dc=linuxian,dc=com" -W -f first_user.ldif
ldapadd -xD "cn=admin,dc=linuxian,dc=com" -W -f group.ldif
ldapadd -xD "cn=admin,dc=linuxian,dc=com" -W -f champu_group.ldif
[/crayon]

<strong>Verify your directory tree using below command </strong>

[crayon language="bash"]
ldapsearch -xb "dc=linuxian,dc=com" "(objectClass=*)"
[/crayon]

<strong>Configuring NFS Server for users home directory (like Roaming profile)</strong>

Configure /etc/exports as below:

[crayon language="bash"]
/home *(rw,sync)
[/crayon]

Run below command to export shares:

[crayon language="bash"]
exportfs -a
[/crayon]

Restart required services using:

[crayon language="bash"]
service portmap restart
service nfs restart
[/crayon]

Testing: Check if all is OK with

[crayon language="bash"]
rpcinfo -p
showmount -e localhost
[/crayon]

<strong>Ldap Client side Setup CentOs5/RHEL5</strong>

Add below line in /etc/fstab

[crayon language="bash"]
IP_OF_NFS_SERVER:/home/ /home/ nfs _netdev,defaults 0 0
[/crayon]

In client side first make sure following package is installed

authconfig openldap nss_ldap

run below command to setup ldap client

[crayon language="bash"]

authconfig --enableldap --enableldapauth --enablemkhomedir --ldapserver=IP_OF_LDAP_SERVER --ldapbasedn="dc=linuxian,dc=com" --update

[/crayon]

Now Login from client side.

<strong>Note: You need to create user’s home directory server end and export that using nfs manually at </strong>