---
date:	2018.10.17
title:	PubLic/Private Key Pair on Linux server Using ssh
categories: Linux
description: The SSH protocol recommended a method for remote login and remote file transfer which provides confidentiality and security for data exchanged between two server systems. The SSH depends upon the use of public key cryptography. The OpenSSH server offers this kind of setup under Linux or Unix-like system. 

pubDate: 2018.10.17
heroImage: '../../assets/keys.webp'
category: 'Linux'
tags: ['ssh','security','administration']

draft: False
---


The SSH protocol recommended a method for remote login and remote file transfer which provides confidentiality and security for data exchanged between two server systems. The SSH depends upon the use of public key cryptography. The OpenSSH server offers this kind of setup under Linux or Unix-like system. This how-to covers generating and using ssh public keys for automated usage such as
- Automated Login using the shell scripts
- Making backups
- Run commands from the shell prompt and more
- Login without password

## The steps and commands are as follows:
1. On your local system type: `ssh-keygen`
2. Install public key into remote server:  
`ssh-copy-id user@remote-server-ip-name`
3. Use ssh for password less login:  
`ssh user@remote-server-ip-name`

Let us see all commands in details.

## Generating SSH Keys
First, log on to your workstation. For example, log on to workstation called admin.fbsd.nixcraft.org as vivek user. Please refer the following sample setup. You will be logged in, on your local system, AS THE USER you wish to make passwordless ssh connections.

To create the cryptographic keys on your local system powered by FreeBSD/Linux/macOS/ UNIX workstation, enter:  
```ssh-keygen -t rsa```
Assign the pass phrase (press [enter] key twice if you don’t want a passphrase). It will create 2 files in ~/.ssh directory as follows:
- `~/.ssh/id_rsa : identification (private) key`
- `~/.ssh/id_rsa.pub : public key`


## Copy a public key (~/.ssh/id_rsa.pub) to your server
Use the scp command to copy the id_rsa.pub (public key) from your local system to rh9linux.nixcraft.org remote server as authorized_keys file, this is know as, “installing the public key to server”:
```
scp ~/.ssh/id_rsa.pub vivek@rh9linux.nixcraft.org:~/.ssh/authorized_keys
```
Another option is to use the ssh-copy-id command as follows from your local workstation:
```
ssh-copy-id user@remote-box
ssh-copy-id -i ~/.ssh/id_rsa.pub vivek@rh9linux.nixcraft.org
```

## login to your remote server using SSH keys
From your local system (e.g. FreeBSD/macOS/Linux/Unix workstation) type the following command:
```
ssh user@remote-box
ssh vivek@rh9linux.nixcraft.org
```

## Changing the Passphrase
To change a passphrase for your ssh keys, use the ssh-keygen command as follows:  
`ssh-keygen -p`
OR
```
cd ~/.ssh/
ssh-keygen -f id_rsa -p
```
## How to use ssh-agen command
You can use the ssh-agent command to avoid continues passphrase typing at the CLI:
```
ssh-agent $SHELL
ssh-add
```
Now ssh server will not use prompt for the password. Above two commands can be added to your ~/.bash_profile file so that as soon as you login into workstation you can set the agent.

## Deleting the keys hold by ssh-agent
To list keys, enter:
`ssh-add -l`

To delete all keys, enter:
`ssh-add -D`

To remove specific key, enter:
`ssh-add -d key`