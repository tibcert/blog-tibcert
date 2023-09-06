---
title: Rsync- To Sync Two Apache Web Servers
description: In this article we will explained the various way to Sync Two Apache Web Servers/Websites Using Rsync. The main use of generating a mirror of Web Server with Rsync, suppose if the main web server fails, the backup server will take over it in order to reduce downtime of the website.

pubDate: 2020.01.10
heroImage: '../../assets/placeholder-hero.jpg'
category: 'Linux'
tags: ['Apache','administration']
---
## Steps to Sync Two Apache Web Servers/Websites Using Rsync
In this article we will explained the various way to Sync Two Apache Web Servers/Websites Using Rsync. The main use of generating a mirror of Web Server with Rsync, suppose if the main web server fails, the backup server will take over it in order to reduce downtime of the website.

### Merits of Syncing Web Servers
- It controls over the ownerships, permissions and special attributes during the data copy remotely.
- Transfers data by compression and decompression method resulting in lesser bandwidth.
- Verifies and deletes the files and directories which are deleted from the main web server.
- It synchronizes the bytes and blocks of changed data.
- Supports SSH protocol that transfer data in an encrypted manner.
How To Sync Two Apache Web Servers

Main Server used:
IP Address : 192.168.5.189
Hostname : server.linuxhelp1.com

Backup Server used:
IP Address : 192.168.5.190
Hostname : backup.linuxhelp1.com

## Install Rsync Tool
On Debian based systems
~~~ bash
  [root@backup ~]#apt-get install rsync
~~~
  
Use the following command to install Rsync on both the servers.
~~~ bash
[root@backup ~]# yum install rsync
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
* base: centos.webwerks.com
* extras: centos.webwerks.com
* updates: centos.webwerks.com
Resolving Dependencies
-->  Running transaction check
--->  Package rsync.x86_64 0:3.0.9-15.el7 will be updated
.
.
.
Updated:
rsync.x86_64 0:3.0.9-17.el7

Complete!
~~~
## Create a User to run Rsync
Either use rsync with root user or create an unprivileged user on main webserver to Rsync for security reasons
~~~ bash 
[root@server ~]# useradd linux
[root@server ~]# passwd linux
Changing password for user linux.
New password:
BAD PASSWORD: The password is shorter than 8 characters
Retype new password:
passwd: all authentication tokens updated successfully.
~~~
## Test Rsync Setup
Run the following command to test Rsync on the backup server.
~~~ bash
[root@backup ~]# rsync -avzhe ssh linux@192.168.5.189:/var/www /var/www
The authenticity of host ' 192.168.5.189 (192.168.5.189)'  can' t be established.
ECDSA key fingerprint is 03:b1:88:9e:3d:c6:eb:fe:38:6c:3f:90:06:51:4b:c9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added ' 192.168.5.189'  (ECDSA) to the list of known hosts.
linux@192.168.5.189' s password:
receiving incremental file list
www/
www/cgi-bin/
www/html/
www/html/index.html

sent 42 bytes received 263 bytes 21.03 bytes/sec
total size is 247 speedup is 0.81
~~~

## Automate Sync with SSH Keys
In order to automatic sync between remote and backup server, we need to generate Public/Private keys(passwordless).
To setup passwordless ssh logins for rsync
To use corn setup for rsync in ssh without login credentials. To do this generate a public and private key by executing the following commands on backups server
~~~ bash 
[root@backup ~]# ssh-keygen -t rsa -b 2048
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
c9:a4:59:9e:32:39:f7:14:92:1b:57:99:79:a0:98:c5 root@backup
The key' s randomart image is:
+--[ RSA 2048]----+
| .. o= |
| =Eo+ . |
| O + . |
| O B . |
| B S . |
| = o |
| . |
| |
| |
+-----------------+
~~~

Press Enter for Empty passphrase to synchronizing it without using password.

Share the public and private key to the main server so that the sync can be accessed in ssh without password.

~~~ bash 
[root@backup ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub root@192.168.5.189
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@192.168.5.189' s password:

Number of key(s) added: 1

Now try logging into the machine, with: " ssh ' root@192.168.5.189' " 
and check to make sure that only the key(s) you wanted were added.
~~~

Open and log in to system using " ssh root@192.168.5.189" ,and verify for the .ssh/authorized_keys.

~~~ bash 
[root@backup ~]# ssh root@192.168.5.189
Enter passphrase for key ' /root/.ssh/id_rsa' :
Last login: Fri May 6 14:49:28 2016
[root@server ~]#
~~~

### Cronjob scheduled Automate Sync
Run the following command to open and setup crontab file
~~~ bash 
[root@backup ~]# crontab &ndash e
5 * * * * rsync -avzhe root@192.168.5.189:/var/www /var/www
~~~
Above command opens /etc/crontab file and edit with the default editor.
