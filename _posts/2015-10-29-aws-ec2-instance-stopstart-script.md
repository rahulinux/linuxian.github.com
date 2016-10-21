---
ID: 1515
post_title: AWS EC2 Instance stop/start script
author: Rahul Patil
post_date: 2015-10-29 05:54:02
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2015/10/29/aws-ec2-instance-stopstart-script/
published: true
dsq_thread_id:
  - "4269843099"
---
If you have UAT, Staging Infra setup on AWS and you want to easily stop/start them if anyone want to use from your team to save the cost then this guide will help you to achieve the same.
 
We have Staging and UAT infra on AWS, always Dev team or ops team need help to start all clusters whenever they want. 

I've created tag based setup which is managed by Ansible, let say you have cluster for MongoDB, then assigned tag is "{ 'Name': 'MongoDB', 'Env': 'UAT' }".

So here is Ansible Playbook which will stop/start instances, you must need to configure the "vars"  section in script 

Note: this script required Ansible EC2 Inventory script, check this <a href="http://docs.ansible.com/ansible/intro_dynamic_inventory.html" target="_blank">link </a>for configure the same.


[code lang="python"]
---
  ## For stop/start EC2 Instances in AWS using Tags
  #
  # Usage:
  #
  # For Starting
  # ansible-playbook -i /inventory/path/ec2.py /playbook_path.yml --tags=start
  #
  # For Stopping
  # ansible-playbook -i /inventory/path/ec2.py /playbook_path.yml --tags=stop
  # Note: Ignore the skipped output because it's skiping nodes which is not belong to the Env that you want to use
  # To suppress skipped use env export DISPLAY_SKIPPED_HOSTS=0
  #
  ####

  - hosts: localhost
    gather_facts: false
    vars:
        env: 'UAT'
        aws_id: &quot;AWS_ACCESS_ID&quot;
        aws_key: &quot;AWS_ACCESS_KEY&quot;
        region: &quot;REGION&quot;
        email_id: &quot;EMAIL_ID1,EMAIL_ID2&quot;
        from_id: &quot;no-reply@abc.com&quot;
        cc: &quot;CC_EMAIL_ID&quot;
        nagios: true
        monitoring_server: 'NAGIOS_SERVER_IP'
        smtp_server_ip: '127.0.0.1'
        smtp_server_port: '25'
        servers:
           - '{{ groups.tag_Name_Web }}'
           - '{{ groups.tag_Name_Appp }}'
           - '{{ groups.tag_Name_Mongo }}'

    tasks:
         - fail: msg='Please use to args --tags=start or --tags=stop'
           tags:
              - untagged

         - name: &quot;Starting Infra&quot;
           set_fact: action='running' msg='Started' tag='start'
           tags:
              - start

         - name: &quot;Stoping Infra&quot;
           set_fact: action='stopped' msg='Stopped' tag='stop'
           tags:
              - stop

         - name: Disabling alerts
           nagios: action=disable_alerts service=host  host='{% for ip in item %}{{ip}}{% if not loop.last%},{%endif%}{% endfor %}'
           delegate_to: &quot;{{ monitoring_server }}&quot;
           with_items:
                 - &quot;{{ servers }}&quot;
           when: &quot;{{ nagios }}&quot;
           tags:
             - stop

         - ec2:
              aws_access_key: &quot;{{ aws_id }}&quot;
              aws_secret_key: &quot;{{ aws_key }}&quot;
              instance_ids:  '{% for i in item %}{{ hostvars[i][&quot;ec2_id&quot;] }}{% if not loop.last%},{%endif%}{% endfor %}'
              state: '{{ action }}'
              region: '{{ region }}'
              wait: false
           with_items:
               - '{{ servers }}'
           register: status
           ignore_errors: true
           tags:
              - start
              - stop

         - mail:
               host=&quot;{{ smtp_server_ip }}&quot;
               port=&quot;{{ smtp_server_port }}&quot;
               subject=&quot;{{ env }} Infra has been {{ msg }}&quot;
               body=&quot;{{ env }} Infra has been {{ msg }}&quot;
               from=&quot;{{ from_id }}&quot;
               to=&quot;{{ email_id }}&quot;
               cc=&quot;{{ cc }}&quot;
               charset=utf8
           when: &quot;{{ status | changed }}&quot;
           tags:
              - start
              - stop
[/code]

If you are not using NagiOS then you can simply set nagios: false or if you are using then you need to Add password less login from the server where you are running this script to NagiOS server.