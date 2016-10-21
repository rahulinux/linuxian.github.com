---
ID: 174
post_title: 'How to solve GPT error (Error: Unable to open /dev/sda &#8211; unrecognised disk label.)'
author: Rahul Patil
post_date: 2012-09-10 13:23:35
post_excerpt: ""
layout: post
permalink: >
  http://linuxian.com/2012/09/10/how-to-solve-gpt-error-error-unable-to-open-devsda-unrecognised-disk-label/
published: true
dsq_thread_id:
  - "2079543081"
---
Sometimes, after connecting an internal or external hard drive to your system you will face GPT error. In my scenario I face following issue when adding new LUN from storage.

[sourcecode language="bash"]
root@server [~] parted /dev/sda
GNU Parted 1.8.1
Using /dev/sda
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) print free
Error: Unable to open /dev/sda - unrecognised disk label.
[/sourcecode]

Why this error comes, because "/dev/sda" has GPT partition label.

There are two types of partition table.
1) GPT
2) MSDOS

<strong>What is GPT ?</strong>
In computer hardware, GUID Partition Table (GPT) is a standard for the layout of the partition table on a physical hard disk.
Although it forms a part of the Extensible Firmware Interface (EFI) standard (Intel's proposed replacement for the PC BIOS),
it is also used on some BIOS systems because of the limitations of MBR partition tables, which restrict a disk and partition's size to a maximum of 2TB.
GPT allows for a maximum disk and partition size of 9.4 ZB.

<strong>What is MSDOS ?</strong>
The MSDOS partition table was introduced a very long time ago. The first sector of the disk is called the Master Boot Record (MBR) which contains two things:
The first 440 bytes is used to store the instructions that the computer will execute when the computer starts. These instructions are used to execute
boot loaders such as Lilo or Grub.

The second part contains 64 bytes which store the partition table. There is space for up to four primary partitions, each one is described in 16 bytes.
In each of these 16 byte partitions description, one of these two following systems can be used:
The CHS (cylinder/head/sector) mechanism was used in the past but it's not used any more because it can only address disks up to 8 GB.
Now the LBA (logical block addressing) is used because it can address up to 2 TB of data.
The consequence is that the msdos partition table can only support up to 4 primary partitions, and cannot address more than 2 TB of space.

<strong>How to Solve GPT error ?</strong>
we can solve this issue by replacing the GPT table with msdos

My initial though was to use fdisk. However, fdisk does not understand GUID Partition Tables and therefore cannot remove such tables. The other tool that does understand GPT is a tool called “parted”.

<strong>How to replace the GPT table with msdos ?</strong>

[sourcecode language="bash"]
parted /dev/sda
mklabel msdos
quit
[/sourcecode]

It creates a new disk label of type "msdos".

Reference link

http://www.sysresccd.org/Sysresccd-Partitioning-EN-The-new-GPT-disk-layout#Limitations_with_the_MSDOS_layout