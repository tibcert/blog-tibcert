---
title: Create a Cron Task in Ubuntu 16.04
description: Cron jobs are an incredibly useful Linux tool aimed at saving you time by scheduling tasks within your server. A programmed cron task will execute commands within a script by the minute, day, week or month.
type: Document

pubDate: 2022.01.14
heroImage: '../../assets/npm.jpg'
category: 'Linux'
tags: ['Ubuntu','Linux','cronjob','administration']

draft: False
---
Cron jobs are an incredibly useful Linux tool aimed at saving you time by scheduling tasks within your server. A programmed cron task will execute commands within a script by the minute, day, week or month. They can be scheduled to do many tasks including backing up your server’s files nightly, updating inventory orders in a database or even compressing files for migrating. Repetitive tasks become a cinch when incorporating a cron job. While there are numerous ways to run a cron task, we will be using the crontab option that is inherent within Ubuntu to set up a nightly backup of our website.


``Pre-flight``
Log in to your Ubuntu 16.04 LTS server, preferably not as root if you aren’t altering anything on the server level.
### Setting Up a Website Backup through Cron  

## Update your server  
As a best practice, we will update and upgrade our server with the following command.
~~~code
 apt-get update && apt-get upgrade
~~~
## Verify Cron
Verify if the cron package is installed correctly or not.

~~~code
dpkg -l cron
~~~
Our example output let’s us know that the cron package is installed, along with its version:
~~~code
||/ Name Version Architecture Description
+++-=========================-=================-=================-========================================================
ii cron 3.0pl1-128ubuntu2 amd64 process scheduling daemon
~~~
Install cron package if necessary.
~~~code
sudo apt-get install cron
~~~
Ensure that the cron service is running with the following command:
~~~code
systemctl status cron
~~~
Example Output:
~~~code
* cron.service - Regular background program processing daemon
Loaded: loaded (/lib/systemd/system/cron.service; enabled; vendor preset: enabled)
Active: active (running) since Sat 2018-10-27 02:53:20 EDT; 5 days ago
~~~
## Configure the cron job.
When you are logged in as your user, you are creating a cron job under that user. Creating a cron jobs owner is helpful when to know who is in charge of the cron as well as how to alter the cron job in the future.
~~~code
crontab -e
~~~
The system asks which editor you’d like to use; this tutorial is using option 2 (vim.basic).
~~~code
tom@host2:~$ crontab -e
no crontab for tom - using an empty one
Select an editor. To change later, run 'select-editor'.
1. /bin/ed
2. /usr/bin/vim.basic
3. /usr/bin/vim.tiny
Choose 1-3 []: 2
~~~
In this file, you’ll see # signs followed by the direction on how to use the file. Allow us a minute to explain the syntax needed to create a cron task.
~~~code
# Example of job definition:
# .---------------- minute (0 - 59)
# | .------------- hour (0 - 23)
# | | .---------- day of month (1 - 31)
# | | | .------- month (1 - 12) OR jan,feb,mar,apr ...
# | | | | .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# | | | | |
# * * * * * user-name command to be executed
~~~
Each set of digits designates the time for that section and follows the time set in your server The first set represents minutes, the next set hours, the next set day of the month and so on. You may be asking yourself what the asterisk mean. They represent every or all value for that set. So, if you put an asterisk on the month column, you’ll be telling the system to perform the tasks every month.

After the digits section is the /bin/sh and the type of file that should be executed, lastly, the final column assigns the command of which to run.

### Backup Cron:
Let’s say you want to backup your website files daily. Our example uses 4 am since server traffic is low during this time. Take note of the .sh extension, indicating a bash script. Use the appropriate extension for your script. Be sure to save the script when exiting with :wq
~~~code
crontab -e
~~~
After altering the ***/path/to/script/backup-script.sh*** path to match the location of your script, set the command in a new line, below the hashtag (#), like so:

# m h dom mon dow command
0 4 * * * /bin/sh /path/to/script/backup-script.sh

## Place Script in Path.
Most importantly is the creation and placement of your script. With our example script we are creating a backup that will compress files and directories, indicated by the /SOURCE/DIRECTORY/. The /SOURCE/DIRECTORY/ needs to be alter to the directory and files you wish to save (sometimes a website’s default directory path is /var/www/html), while the /DESTINATION/DIRECTORY/file.zip indicates where the compressed backup are stored. We will name the script backup-script.sh, and afterwards, we will create the script in the location of /path/to/script/backup-script.sh.
~~~code
vim backup-script.sh  
~~~  

~~~code
#!/bin/sh
zip -9pr /DESTINATION/DIRECTORY/file.zip /SOURCE/DIRECTORY/
~~~

```NOTE```:f you are running this script multiple times it will overwrite the ***file.zip*** each time, thus it runs it will only store one backup.

When the time of execution has passed, check your */DESTINATION/DIRECTORY/* to see ***file.zip***. YYou now have backups of your files running daily! It’s best to come up with a plan to offload your backups to another location other than your server. Pushing backups to an offsite location not only adds a layer of security but aids in saving server disk space. Check out our Cloud Object Storage for cost-effective storage solution that is accessible via API call. If you are having trouble with your cron check out our other helpful article, Troubleshooting: Cron Jobs.

