---
title: Setup Mysqldump Without Password in Cronjob
categories: Cronjob
description: Settingup mysqldump without password which can be very convinient Crontask.

pubDate: 2022.1.23
heroImage: '../../assets/placeholder-hero.jpg'
category: 'Linux'
tags: ['mysql','cronjob']

draft: False
---
![mysql-image](https://upload.wikimedia.org/wikipedia/en/6/62/MySQL.svg)  
If you execute mysqldump manually, mysqldump will prompt you for password. If you setup mysqldump via cronjob, you need to find a solution to disable the password. I’ve setup mysqldump to backup my db via cronjob without password. All you need to do is just to add a file in your home directory and it will disable the mysqldump password prompting

### To Setup Mysqldump Without Password in Cronjob, follow the steps below
Start your terminal and login to your server.
Create a .my.cnf file in the home director.
~~~bash
user@server:~ nano ~/.my.cnf
~~~
Copy the content below and replace with your mysql username and password.
~~~bash
[mysqldump]
user = mysqluser
password = secret
~~~
Now change the file permission to 600 to prevent other user from reading it
~~~bash
user@server:~ chmod 600 ~/.my.cnf
~~~
Now you can try to execute mysqldump and system will not prompt you for password by now
To setup the cronjob
~~~bash
user@server:~ crontab -e
~~~
Append the line below to your crontab and it will run mysqldump every night at 12:00am
~~~
0 * * * mysqldump -u mysqluser -h localhost --all-databases | gzip -9  > alldb.sql.gz > /dev/null 
~~~