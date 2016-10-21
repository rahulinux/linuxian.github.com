---
ID: 1467
post_title: Setup servers using Ansible
author: Rahul Patil
post_date: 2015-01-13 07:11:20
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2015/01/13/setup-servers-using-ansible/
published: true
dsq_thread_id:
  - "3415747862"
---
First we installed OS, then do OS hardening and package installation etc., this is tradition way of doing, now let's see how to do it by simplest way using Ansible.

<strong>What is Ansible ?</strong>

Ansible is a radically simple IT automation platform that makes your applications and systems easier to deploy. Avoid writing scripts or custom code to deploy and update your applications— automate in a language that approaches plain English, using SSH, with no agents to install on remote systems

<strong>In Summery:</strong>
 
    - Simple IT Automation tool 
    - It's Configuration Management tool
    - Provision tool --> Create New VM/(AWS)Instance
    - Package Installations
    - Deployment 

<strong>Why Ansible ? </strong>

    - YAMAL Based configuration, which is easy to read and understand
    - Agentless ( no need to install any package on remote machine because it's uses SSH )
    - Batteries included ( inbuilt modules available <http://docs.ansible.com/ansible/modules_by_category.html> )
    - Secure ( it's uses SSH protocol )
    - It's supports API ( http://docs.ansible.com/ansible/developing_api.html#detailed-api-example )

<strong>Where we need to Ansible ? </strong>

    - Manage multiple nodes from Single machine 
    - Setup Infra like Prod/UAT/DEV ( in Cloud like AWS, Rackspace, Google Cloud )
    - Integrate with Deployment tools like Jenkins for deploying applications (paid versions - bamboos)
    - Package managment, OS Hardening, User managment etc.
    - Ansible uses Push model, we need to push to apply any new changes every single time there is
      new update to remote machine.

    
<strong>## Terminology </strong>

    - Playbook : Plain YAML which where you defined list of action/tasks which we are going to performed 
    
    - Tasks    : Action you are going to perform like package installation or command etc 
    
    - Roles    : We can create our roles like webserver or database server which include the Playbook
    
    - Facts    : There are other places where variables can come from, but these are a type of variable that 
                 are discovered, not set by the user. Facts are information derived from speaking with
                 your remote systems. Example like get OS name, or IP adddress or hostname 
                 
    - Templates: It's like dynamic file which will store in remote machine with updating variable values.
                 Templates are processed by the Jinja2 templating language
    
    - Vars     : There are 2 types of Variables in Ansible
                 1. Playbook variable : which we defined in Playbook or while running playbook, it's globally use 
                    everywhere in playbook and it's also override the other varible which has same name.

                    ## following way to define variable in playbook
[code]
                    vars:
                       test: 'abc'
[/code]
                    ## Inside tasks ( Runtime variable ) 
[code]
                    - set_fact: test=&quot;abc&quot;
[/code]
                    
                    ## Or we can specify while running command, ( Runtime variable )
[code]
                    ansible-playbook -e test=&quot;abc&quot; playbook.yaml
[/code]
                                        
                 2. Defualt variable   : When we run playbook, it's try to search variable in some default 
                                         location which not found in playbook book variable.
                                         
                                         1. group_vars/hostname: this is hostbased variable, so first ansible 
                                            will try to search in hostbased variable

                                         2. group_vars/groupname: this is groupbased variable, if variable not 
                                            found in hostbased variable then it will try search in groupbased.

                                         3. group_vars/all: at the last it's try to search in "all" which 
                                            is plain YAML variable file.
                                            
                                         4. vars_file: or you can include your custom variable file. 
                                            
                                            
      - Inventory: There are two types of inventory in ansible
                   1. Static Inventory: plain text file where your all hosts defined with groups 
                     
                      Example:
[code]                      
                      # Hosts it's comments
                      192.168.0.2  ansible_ssh_user=root ansible_ssh_pass=redhat var1=test
                      192.168.0.3  ansible_ssh_user=root ansible_ssh_pass=redhat var1=test
                      192.168.0.10  ansible_ssh_user=root ansible_ssh_pass=redhat name=blabla

                   
                      # Groups it's comments
                      [webservers]
                      192.168.0.1
                      192.168.0.3
                   
                      [dbservers]
                      192.168.0.10
[/code]                      
                      ## To check hosts
[code]
ansible -i inv_file all --list-host  
# it will return all ips from inventory 


ansible -i inv_file webservers --list-host 
# it will return all ips from webservers groups 
[/code]                      

# To run uptime on all hosts ( this is add-hoc command )

[code]
ansible -i inv_file all -a 'uptime'
[/code]                   
                   2. Dynamic Invenotory: it's script which will return the hosts/groups details 
                      from any host providers like AWS, Docker, Rackspace etc.  
                      

<strong>Lets do some practical stuff </strong>

<strong>Goal</strong>

We will install ansible on your local machine and setup following things in remote machine
<ul>
	<li>OS Hardening</li>
	<li>Install base packages</li>
</ul>
<strong>Install Ansible on local machine</strong>

[code lang="bash"]
sudo apt-add-repository -y ppa:ansible/ansible
sudo apt-get update
sudo apt-get install -y ansible
[/code]

<strong>Create Playbook</strong>

We will create one common role, which will do following things

1. os_hardening : All things related to OS hardening
2. base_package : Install or compile required packages

Also there will be one single file to manage global variables, like in future if we need to change version of any application or links etc.

Now create one dir and cd into it then create yml files

[code lang="bash"]
mkdir deploy-nodes &amp;&amp; cd deploy-nodes
[/code]

<strong>Create playbook structure</strong>

[code lang="bash"]
mkdir -p roles/common/tasks
touch site.yml
touch roles/common/tasks/main.yml
touch roles/common/tasks/os_hardening.yml
touch roles/common/tasks/base_packages.yml
touch group_vars/all[/code]

So our final structure as below :

[code lang="plain"]
|-- ansible_hosts
|-- group_vars
|  '-- all
|-- roles
| '-- common
|  '-- tasks
|    |-- base_packages.yml
|    |-- main.yml
|    '-- os_hardening.yml
'-- site.yml
[/code]

Now let's configure the os_hardening.yml

There are many things you can do to secure your OS but for learning ansible, we will do basic things, like disable root user in ssh and adding MaxAuthTries 3

Let's first defined ssh related variables in vars.yml file

Edit "group_vars/all" and configure as below :

[code lang="python"]
---
sshd_config: '/etc/ssh/sshd_config'
[/code]

Note: file started with "---", because it YML file, so every YML file, should have this entry.

Configure the "roles/common/tasks/os_hardening.yml"

[code lang="python"]
---
  - name: SSHD# Disable Root login
    lineinfile:
        backup=yes
        state=present
        dest={{ sshd_config }}
        regexp='^PermitRootLogin'
        line='PermitRootLogin no'

  - name: SSHD# Updating MaxAuthTries to 3
    lineinfile:
        backup=yes
        state=present
        dest={{ sshd_config }}
        regexp='^MaxAuthTries'
        line='MaxAuthTries 3'

  - name: SSHD# Restarting ssh service
    service:
      name=ssh
      state=restarted
[/code]

<strong>Explanation :</strong>

1. First it will take backup destination file then search line starting with "PermitRootLogin" and replace with "PermitRootLogin no"
2. Again search and replace but if search not found then it will add "MaxAuthTries 3"
3. Finally it will restart ssh to affect the changes.
Configure "roles/common/tasks/base_package.yml"

[code lang="python"]
---
  - name: Install list of packages
    action:
       apt
       update_cache=yes
       cache_valid_time=600
       pkg={{item}}
       state=installed
    with_items:
    - unzip
    - build-essential
    - openssl
    sudo: true
    when:
       ansible_distribution == 'Debian' or
       ansible_distribution == 'Ubuntu'
[/code]

<strong>Explanation :</strong>

It will first do apt-get update if it is not run last 10 min ( cache_valid_time=600 ), then it will install mention packages.

Configure "roles/common/tasks/main.yml" file

[code lang="python"]
---
 - include: os_hardening.yml
 - include: base_packages.yml
[/code]

Configure "site.yml" file

[code lang="python"]
---
  - hosts: all
    sudo: true
    roles:
       - common
[/code]

Add your hosts to ansible_hosts file

[code]
[servers]
remote-ip-address ansible_ssh_user=remote-user ansible_sudo_pass=password ansible_ssh_pass=password
[/code]

Note: if there is any special character in your password then you need to escape it Ex. if password is: p@ssw1rd then use: p\@ssw1rd

Now let's run the ansible command to setup remote host

[code lang="bash"]
ansible-playbook -vvv -i ansible_hosts site.yml
[/code]

If you face "ssh fingerprint" issue then do following thing and run above command again:
<pre class="">
[code lang="bash"]
sudo sed -i.bkp 's/^#host_key_checking = False/host_key_checking = False/' /etc/ansible/ansible.cfg
[/code]

You can find this playbook on github, check <a href="https://github.com/rahulinux/ansible" target="_blank">this page</a>