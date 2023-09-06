---
title:	hide wp-admin page.
description: This instruction will guide you through hiding wp-admin login page from Bruteforce attackers.
type: Document

pubDate: 2018.10.18
heroImage: '../../assets/Wordpress.jpg'
category: 'Wordpress Tricks'
tags: ['JavaScript', 'css', 'HTML5', 'Wordpress','tricks']
draft: False

---
This instruction will guide you through hiding wp-admin login page from Bruteforce [^1] attackers.
## 404 Error File

`404 error file:`
Place these line of code in 404 error page. So when the attacker try to attempts to load wp-admin page. The page will load to error page which can display the ip address.
This is because, attacker IP address is blocked from root .htaccess file. 
Now let’s jump to .htaccess file how this is setup.

```php
<?php 
   $ip = $_SERVER['HTTP_CLIENT_IP']?$_SERVER['HTTP_CLIENT_IP']:($_SERVER['HTTP_X_FORWARDE‌​D_FOR']?$_SERVER['HTTP_X_FORWARDED_FOR']:$_SERVER['REMOTE_ADDR']);
   echo $ip;
 ?>

```
## Technique 1
### .htaccess[^2] :
Open  `.htaccss` file and add the code lines. 
```
#
order allow,deny
allow from 255.0.0.0
deny from 123.45.6.
allow from all

```
On these lines above:
: 250.0.0.0 - `allowed access (Replace with your IP Address)` 
: 123.45.6. -- `disabled IP address starting first three decimal 123.45.6.`  
: Allow from all - `rest of others can access`  

  

## Technique 2
`Rewrite Engine`: Add the codes to your htaccess file and repace the IP address `!^103\.63\.25\.52` with your IP address. 
```
#Only allow wp-login from this ip address
<IfModule mod_rewrite.c>
RewriteEngine on
RewriteCond %{REQUEST_URI} ^(.*)?wp-login\.php(.*)$ [OR]
RewriteCond %{REQUEST_URI} ^(.*)?wp-admin$
RewriteCond %{REMOTE_ADDR} !^103\.63\.25\.52$
RewriteRule ^(.*)$ - [R=403,L]
</IfModule>

```
On these lines above,
: `REQUEST_URI` for wp-admin and wp-login are only given permission to IP address `103.63.25.52` only.  
 You can add more allowed IP address by duplicating and edit the IP number to grant access to secondary access ass example shown below.

```
#Only allow wp-login from this ip address
<IfModule mod_rewrite.c>
RewriteEngine on
RewriteCond %{REQUEST_URI} ^(.*)?wp-login\.php(.*)$ [OR]
RewriteCond %{REQUEST_URI} ^(.*)?wp-admin$
RewriteCond %{REMOTE_ADDR} !^103\.63\.25\.52$
RewriteCond %{REMOTE_ADDR} !^103\.63\.25\.35$
RewriteRule ^(.*)$ - [R=403,L]
</IfModule>

```
Now access to wp-admin is only given to `103.63.25.52` and `103.63.25.35`.
  
  
  --------------------------------------

[^1]: A brute force attack is a trial-and-error method used to obtain information such as a admin username and passwoords.
[^2]: Just about every WordPress site has an .htaccess file, located in the 'root' or central directory. you can open this file using ftp client or ssh.