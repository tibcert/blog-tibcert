---
title:	Htaccess Tips & Tricks 
description: Some people might not aware of the power of htaccess, I have 12 htaccess methods in this article which some of them are essential tricks and tips to protect your webserver against malicious attacks and other would able to perform simple tasks efficiently such as redirection and web server optimization.

pubDate: 2018.10.17
heroImage: '../../assets/book.jpg'
category: 'Linux'
tags: ['server', 'administration']
draft: False
---

Some people might not aware of the power of htaccess, I have 12 htaccess methods in this article which some of them are essential tricks and tips to protect your webserver against malicious attacks and other would able to perform simple tasks efficiently such as redirection and web server optimization.   

## 1. Redirect browser to https (ssl)
Add following snippet to your htaccess and redirect entire website to https.
```
RewriteEngine On
RewriteCond %{HTTPS} !on
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}

```
## 2. Redirect non-www to www using mod_rewrite  module
```

Option +FollowSymLinks
RewriteEngine on
rewritecond %{http_host} ^mywebsite.com [nc]
rewriterule  ^(.*)$ https://www.mywebsite.com/$1 [r=301,nc]

```

## 3. Allow/Deny Access to Websites
htaccess file can allow or deny access of website or a folder or files in the directory in which it is placed by using order, allow and deny keywords.
`Allowing access to only 192.168.3.1 IP`
```
Order Allow, Deny
Deny from All
Allow from 192.168.3.1
OR
Order Allow, Deny
Allow from 192.168.3.1
```
`Denying access to only one IP Address`   
```
Order Allow, Deny
Deny from 192.168.3.1
Allow from All
OR
Order Deny, Allow
Deny from 192.168.3.1
```



## 4. Enable GZip compression
#### How to enable GZip compression for Files to save site’s bandwidth.
This is a common observation that heavy sites generally run bit slowly than light weight sites that take less amount of space. This is just because for a heavy site it takes time to load the huge script files and images before displaying them on the client’s web browser.
This is a common mechanism that when a browser requests a web page, server provides the browser with that webpage and now to locally display that web page, the browser has to download that page and then run the script inside that page.
What GZip compression does here is saving the time required to serve a single customer thus increasing the bandwidth. The source files of the website on the server are kept in compressed form and when the request comes from a user then these files are transferred in compressed form which are then uncompressed and executed on the server. This improves the bandwidth constrain.   


Following lines can allow you to compress the source files of your website but this requires mod_deflate.cmodule to be installed on your server.
```
<IfModule mod_deflate.c>
AddOutputFilterByType DEFLATE text/plain
AddOutputFilterByType DEFLATE text/html
AddOutputFilterByType DEFLATE text/xml
AddOutputFilterByType DEFLATE application/html
AddOutputFilterByType DEFLATE application/javascript
AddOutputFilterByType DEFLATE application/x-javascript
</IfModule>
```
## 5. Prevent Access to your php includes files
If you have a directory containing PHP includes, that you do not wish to be accessed directly from the browser, there is a way of disabling the directory using Mod_Rewrite. 
```

## Enable Mod Rewrite, this is only required once in each .htaccess file
RewriteEngine On 
RewriteBase / 
## Test for access to includes directory
RewriteCond %{THE_REQUEST} ^[A-Z]{3,9}\ /includes/ .*$ [NC] 
## Test that file requested has php extension 
RewriteCond %{REQUEST_FILENAME} ^.+\.php$ 
## Forbid Access 
RewriteRule .* - [F,NS,L]
```
## 6. Directory Index Use
The directoryindex command allows you to specify a default page to display when a directory is accessed. For instance, if a visitor requests a directory on your web site, you can specify the file to load when the directory is accessed (if a filename is not specified in the initial request). For example, to display a 'index.html' file rather than showing directory listings or to load a 'index.php' file rather than an 'index.html' file.
```
DirectoryIndex index.html
```
The above lines tell the Apache Web Server to display the 'index.html' file, whenever the directory containing this .htaccess file (or any subdirectory) is accessed.
We can setup a directoryindex to call multiple files using the following text:
```
DirectoryIndex index.html index.cgi index.php
```
The above lines tell the Apache Web Server to display the 'index.html' file as the directoryindex, if this file is not available then display 'index.cgi', and if this is not available then display 'index.php'.
If not of the specified files are available, the Apache Web Server will revert to it's default settings, either displaying an error message, a directory listings not available message, or displaying the directory listings of files and directories (this can be prevented which we discuss in section 'Prevent viewing of directory listings').

## 7. Hot Link Prevention
Hot link prevention refers to stopping web sites that are not your own from displaying your files or content, e.g. stopping visitors from other web sites. This is most commonly used to prevent other web sites from displaying your images but it can be used to prevent people using your JavaScript or CSS (cascading style sheet) files. The problem with hot linking is it uses your bandwidth, which in turn costs money, hot linking is often referred to as 'bandwidth theft'.
```
RewriteEngine on
RewriteCond %{HTTP_REFERER} !^$
RewriteCond %{HTTP_REFERER} !^http://(www\.)?yourdomain.com/.*$ [NC]
RewriteRule \.(gif|jpg|css)$ - [F]
```
Above lines  set-up hot link prevention for '.gif', '.jpg' and '.css' files
```
RewriteEngine on
RewriteCond %{HTTP_REFERER} !^$
RewriteCond %{HTTP_REFERER} !^http://(www\.)?yourdomain.com/.*$ [NC]
RewriteRule \.(gif|jpg)$ http://www.yourdomain.com/hotlink.jpg [R,L]
```
The above lines tell the Apache Web Server to block all links to '.gif' and '.jpg' files which are not from the domain name 'http://www.yourdomain.com/' and to display the file 'http://www.yourdomain.com/hotlink.jpg' instead. Before uploading your .htaccess file ensure you replace 'yourdomain.com' with the appropriate web site address.

## 8. Make Media files are downloaded instead of Played
It is possible to ensure that any media files are treated as a download, rather than to be played by the browser.
```
AddType application/octet-stream .zip .mp3 .mp4
```
## 9. Block author id from url
`http://websitename.com/?author=1`
```
# END WordPress
#1 Block User ID enumeration
<IfModule mod_rewrite.c>
RewriteCond %{QUERY_STRING} ^author=([0-9]*)
RewriteRule .* http://paljordawa.com/? [L,R=302]
</IfModule>
```
## 10. Block visitors based on refering Domain

```
# block visitors referred from indicated domains
<IfModule mod_rewrite.c>
 RewriteEngine on
 RewriteCond %{HTTP_REFERER} scumbag.com [NC,OR]
 RewriteCond %{HTTP_REFERER} wormhole.com [NC,OR]
 RewriteRule .* - [F]
 
</ifModule>
```
## 11. Disable execcution of Scripts to secure Directories
```
AddHandler cgi-script .php .pl .py .jsp .asp .htm .shtml .sh .cgi
Options -ExecCGI
```
## 12.  SKIP THE DOWNLOAD DIALOGUE
Usually when you try to download something from a web server you get a request asking whether you want to save the file or open it. To avoid that you can use the below code on your .htaccess file
```
AddType application/octet-stream .pdf
AddType application/octet-stream .zip
AddType application/octet-stream .mov
```
