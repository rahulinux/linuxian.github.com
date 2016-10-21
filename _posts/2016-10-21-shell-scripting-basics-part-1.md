---
ID: 1578
post_title: '# Shell Scripting Basics Part-1'
author: Rahul Patil
post_date: 2016-10-21 15:58:23
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2016/10/21/shell-scripting-basics-part-1/
published: true
---
# Shell Scripting Basics Part-1

Those who are new to programming/scripting, you need to know some basic programming concepts, so post will help to you understand the same

#Concepts 1 - Data Types


## Data Types


In Computer Science a data type or simply type is a classification identifying one of various types of data, such as real, integer or Boolean, that determines the possible values for that type, the operations that can be done on values of that type, the meaning of the data, and the way values of that type can be stored


Examples:
- Numbers (`int`), (e.g., 7, 3.14)
- Booleans (`bool`) (true or false) (1 or 0 )
- Characters (`Char`) ('a', 'b', ... 'z', '1', '2', ... '9', '!', '^', etc)
- Arrays (a list of data (all of the Same Data Type!))


---


# Concepts 2 - Variables


## Variables


Variables play a very important role in most programming languages.
A variable allows you to store a value by assigning it to a name, which can be used to refer to the value later.


To assign a variable, use one equals sign ( e.g. `=` )


Examples:


Try in Shell
```
x=1
printf "%d\n" $x
```


in AWK


```
awk 'BEGIN{ x=1; printf "%d\n", x }'
```


signal equal sign is assignment operator. **Warning, while the assignment operator looks like the traditional mathematical equals sign, this is NOT the case. The equals operator is '=='**


Lets take example of useradd script with variable and without variable.


### Without variable


edit `add_user.sh`


```
#!/bin/bash


useradd champu
echo "champu:password" | chpasswd


mkdir /home/champu
cp -a /etc/skel/.bash* /home/champu/
chown -R champu:champu /home/champu/
```


Now think if you want to add one more user using above script, you have to change champu user to everywhere in script, this is where importance of variale comes into the picture..


Now check following script with variable.


### With variable

Note: In latest `useradd` command, it create home directory, but in older version, we need to create home directory. so following example just for understanding the variable. if you face error like directory already exists, then ignore it.

```
#!/bin/bash

user_name=champu
user_pass=password


useradd $user_name
echo "$user_name:$user_pass" | chpasswd

mkdir /home/$user_name
cp -a /etc/skel/.bash* /home/$user_name
chown -R champu:champu /home/$user_name
```


If you know the more commands, then you can make script with less code of lines


```
#!/bin/bash

user_name=champu
user_pass=password


useradd $user_name
echo "$user_name:$user_pass" | chpasswd
mkhomedir_helper $user_name
```


---


Concept 3 Relational/Conditinal Operators


### Exit status


A relational operator checks the relationship between two operands


As system administrator, if you need to understand the relational operators, then you can think like if command fail and successed


Lets run command `ls` and check the exit status using `echo $?`


```
ls
echo $?
```
The output is zero, that mean commmand is successfully ran.


Now lets run unknown command which does not exists


```
aljdfls
echo $?
```


The output will be non-zero.. that means command is failed.


Similarly in programming if any condition return `0` means success and non zero means fail. so you need to check `return` value of the program.


Now lets use the Conditional/Relational operator in shell


```
name="champu"
[[ $name == "champu" ]]
echo $?
```


| Operator| Meaning of Operator| Status |
|:------:|:---------------:|:-----------------------:|
| == | Equal to 5 == 3 | returns 0|
| > | Greater than 5 > 3| returns 0|
|< |Less than 5 < 3 | returns 1|
| != | Not equal to 5 != 3| returns 0|
| >= | Greater than or equal to 5 >= 3| returns 0|
|<= | Less than or equal to 5 <= 3| return 1|
---