---
ID: 521
post_title: svnuseradd.sh
author: Rahul Patil
post_date: 2012-11-30 18:50:43
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/11/30/svnuseradd-sh/
published: true
dsq_thread_id:
  - "2078921686"
---
The svnuseradd.sh script can be used to achieve the below requirements
<ul>
	<li>To add svnuser into your system</li>
	<li>add existing user into svngroup</li>
</ul>
<ul>
	<li>create ssh private and public keys for each users ( Password less login)</li>
</ul>
<ul>
	<li> creates ssh pubic key in users own home directory, which can be loaded from windows machine using pegagent</li>
</ul>
<ul>
	<li> enable "svn ssh-tunnel", "svn log file" for each user</li>
</ul>
<ul>
	<li> install usermin for user self password changer from webinterface</li>
</ul>
<ul>
	<li> tested and working in Ubuntu</li>
</ul>
<ul>
	<li> Default User password "username@1234"</li>
</ul>
<ul>
	<li> Default Shell "/bin/sh" but , user will not able to login via putty, winscp</li>
</ul>
[crayon lang="bash"]

#!/bin/bash

# svnuseradd.sh
#-----------------------------------------------------------------------------------------------------
# Author  : Rahul Patil&lt;linuxian.com&gt;
# Date    : Wed Nov 28 18:51:43 IST 2012
#-----------------------------------------------------------------------------------------------------

# Specify Your Repository/Project path
Project="/svn/repositories/project1/"
Svnserv_binary="$(which svnserve)"

# specify Your svngroup or use default
SvnGroup="svngroup"

# Difine ssh passkey if you want otherwise leave it
Pass_key=""

## Color Function
shw_norm () {
echo $(tput bold)$(tput setaf 2) $@ $(tput sgr 0)
}

shw_err ()  {
echo -e $(tput bold)$(tput setaf 1) $@ $(tput sgr 0)
}

# Check 1#
#if args less than zero then show_help
[[ $# -eq 0 ]] &amp;&amp; { useradd --help; exit; };

# check 2#
# if Project not specify then exit
[[ ! -d "${Project}" ]] &amp;&amp; {
shw_err "Project does not exist..nPlease define Project path in $0" &gt;&amp;2;
exit 1;
};

# check 3#
# if subversion not install then exit
[[ -z "${Svnserv_binary}" ]] &amp;&amp; {
shw_err  "Subversion not installed..nPlease install Subversion..." &gt;&amp;2;
exit 1;
};

# check 4#
# if puttygen not installed then install
if !  (which puttygen &gt;/dev/null 2&gt;&amp;1); then
shw_norm "puttygen installed...ninstalling puttygen"; sleep 2s &amp;&amp;
apt-get install putty-tools
fi

# check 5#
# if usermin not installed then install
if [ ! -f "/etc/usermin/miniserv.conf" ]; then
shw_err "Usermin is not Install..." &gt;&amp;2 &amp;&amp; sleep 1s &amp;&amp;
shw_norm "Installing Usermin For Selfpassword change for user" &amp;&amp; sleep 2s &amp;&amp;
apt-get install libnet-ssleay-perl libio-pty-perl apt-show-versions -y &amp;&amp;
wget http://prdownloads.sourceforge.net/webadmin/usermin_1.530_all.deb &amp;&amp;
dpkg -i usermin_1.530_all.deb
# remove unwated rights from usermin only keep password change options
cp /etc/usermin/webmin.acl{,-bkp$(date +%F)}
echo 'user: changepass' &gt; /etc/usermin/webmin.acl
fi

useradd_sshkey() {
# if group does not exists then create
grep -q "${SvnGroup}" /etc/group || groupadd ${SvnGroup}

# add new user else add existing user into svn group
useradd -s /bin/false -g "${SvnGroup}" -m "$@" &amp;&amp;
read User_name Home_Dir &lt;&lt;&lt;"$(awk -F: 'END{print $1,$6}' /etc/passwd)" ||
{ useradd -g "${SvnGroup}" $1; Input="$1" ;
read User_name Home_Dir &lt;&lt;&lt;"$(awk -F: "/$Input/ "'{print $1,$6}' /etc/passwd)"; };

# Set password
echo "${User_name}:${User_name}@1234" | /usr/sbin/chpasswd
# if home dir not exists then create
[[ ! -d "${Home_Dir}" ]] &amp;&amp; mkdir "${Home_Dir}"

# if .ssh dir not exists in uses home dir then create
[[ ! -d "${Home_Dir}/.ssh" ]] &amp;&amp; mkdir ${Home_Dir}/.ssh

# add svn ssh tunnel into user's authorized keys
echo -n "command="${Svnserv_binary} -t --tunnel-user=${User_name} -r ${Project} --log-file=/tmp/svnserver_${User_name}.log",no-port-forwarding,no-agent-forwarding,no-X11-forwarding,no-pty" &gt;&gt;
"${Home_Dir}"/.ssh/authorized_keys

# create private and public key using ssh-keygen without pass
ssh-keygen -P "${Pass_key}" -t rsa -b 1024 -f "${User_name}"_ssh.key

# connvert private key into ssh_key.ppk which can use to load into pagagent in windows
puttygen "${User_name}"_ssh.key -O private -o "${Home_Dir}"/"${User_name}"_ssh.ppk
echo -n ' ' &gt;&gt; "${Home_Dir}"/.ssh/authorized_keys
cat "${User_name}"_ssh.key.pub &gt;&gt; "${Home_Dir}/.ssh/authorized_keys"

# change permission
chown -R "${User_name}"  "${Home_Dir}"
chmod 700 -R  "${Home_Dir}"

}

useradd_sshkey "$@"

[/crayon]