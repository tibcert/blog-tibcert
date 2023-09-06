---
title:	Post QR code Generator
description: This Instruction will guide you to make QR code Plugins to generate Post URL.

pubDate: 2018.10.30
heroImage: '../../assets/wp.png'
category: 'Wordpress Tricks'
tags: ['wordpress','Linux','AWS','plugin']

draft: False
---

## Introduction
QR codes are widely used on websites where they generally represent the URL of the website. When someone wants to read the content of your web page on their mobile phone, they can scan the QR code and which opens the URL in a browser instead of manually typing a long URL.

There are several types of QR codes, Model 1, Micro, IQR, SQRC and LogoQ. Model 1 is the most common type of QR code type and it’s the one we’ll be using in our example.


## Creating Dir and Plugin file
In my example I’ll name the plugin ‘qr-code-post’, you can name it anything you wish. You’ll first need to create a directory with plugin name and then a PHP file with the same plugin name.

```
qr-code-post/
	- qr-code-post.php
```
Put this code in the awesome-qr-code.php file:
``` php

<?php
/*
Plugin Name: QR Code Post
Plugin URI: Website link of Plugin
Description: Adds QR Code to WordPress Posts
Version: 1.0
Author: your name
Author URI: http://twitter.com
*/
```
## Including the QR Code Generator Library
`Option1:` Download and unpack[ QR Code Generator](http://www.phpclasses.org/package/6399-PHP-Generate-QR-Code-images-using-Google-Chart-API.html) inside your plugin directory. You will then have a file named `qrcode.php` in your directory.

`Option2: ` Inside your Plugin Directory create `qrcode.php` file and copy and paste the code below to your `qrcode.php`
```php
<?php  
/*************************************************************  
 * This script is developed by Arturs Sosins aka ar2rsawseen, http://webcodingeasy.com  
 * Fee free to distribute and modify code, but keep reference to its creator  
 *  
 * This class generate QR [Quick Response] codes with proper metadata for mobile  phones  
 * using google chart api http://chart.apis.google.com  
 * Here are sources with free QR code reading software for mobile phones:  
 * http://reader.kaywa.com/  
 * http://www.quickmark.com.tw/En/basic/download.asp  
 * http://code.google.com/p/zxing/  
 *  
 * For more information, examples and online documentation visit:   
 * http://webcodingeasy.com/PHP-classes/QR-code-generator-class  
 **************************************************************/  
class qrcode  
{  
    private $data;  
      
    //creating code with link mtadata  
    public function link($url){  
        if (preg_match('/^http:\/\//', $url) || preg_match('/^https:\/\//', $url))   
        {  
            $this->data = $url;  
        }  
        else  
        {  
            $this->data = "http://".$url;  
        }  
    }  
      
    //creating code with bookmark metadata  
    public function bookmark($title, $url){  
        $this->data = "MEBKM:TITLE:".$title.";URL:".$url.";;";  
    }  
      
    //creating text qr code  
    public function text($text){  
        $this->data = $text;  
    }  
      
    //creatng code with sms metadata  
    public function sms($phone, $text){  
        $this->data = "SMSTO:".$phone.":".$text;  
    }  
      
    //creating code with phone   
    public function phone_number($phone){  
        $this->data = "TEL:".$phone;  
    }  
      
    //creating code with mecard metadata  
    public function contact_info($name, $address, $phone, $email){  
        $this->data = "MECARD:N:".$name.";ADR:".$address.";TEL:".$phone.";EMAIL:".$email.";;";  
    }  
      
    //creating code wth email metadata  
    public function email($email, $subject, $message){  
        $this->data = "MATMSG:TO:".$email.";SUB:".$subject.";BODY:".$message.";;";  
    }  
      
    //creating code with geo location metadata  
    public function geo($lat, $lon, $height){  
        $this->data = "GEO:".$lat.",".$lon.",".$height;  
    }  
      
    //creating code with wifi configuration metadata  
    public function wifi($type, $ssid, $pass){  
        $this->data = "WIFI:T:".$type.";S:".$ssid.";P:".$pass.";;";  
    }  
      
    //creating code with i-appli activating meta data  
    public function iappli($adf, $cmd, $param){  
        $param_str = "";  
        foreach($param as $val)  
        {  
            $param_str .= "PARAM:".$val["name"].",".$val["value"].";";  
        }  
        $this->data = "LAPL:ADFURL:".$adf.";CMD:".$cmd.";".$param_str.";";  
    }  
      
    //creating code with gif or jpg image, or smf or MFi of ToruCa files as content  
    public function content($type, $size, $content){  
        $this->data = "CNTS:TYPE:".$type.";LNG:".$size.";BODY:".$content.";;";  
    }  
      
    //getting image  
    public function get_image($size = 150, $EC_level = 'L', $margin = '0'){  
        $ch = curl_init();  
        $this->data = urlencode($this->data);   
        curl_setopt($ch, CURLOPT_URL, 'http://chart.apis.google.com/chart');  
        curl_setopt($ch, CURLOPT_POST, true);  
        curl_setopt($ch, CURLOPT_POSTFIELDS, 'chs='.$size.'x'.$size.'&cht=qr&chld='.$EC_level.'|'.$margin.'&chl='.$this->data);  
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);  
        curl_setopt($ch, CURLOPT_HEADER, false);  
        curl_setopt($ch, CURLOPT_TIMEOUT, 30);  

        $response = curl_exec($ch);  
        curl_close($ch);  
        return $response;  
    }  
      
    //getting link for image  
    public function get_link($size = 150, $EC_level = 'L', $margin = '0'){  
        $this->data = urlencode($this->data);   
        return 'http://chart.apis.google.com/chart?chs='.$size.'x'.$size.'&cht=qr&chld='.$EC_level.'|'.$margin.'&chl='.$this->data;  
    }  
      
    //forcing image download 
    public function download_image($file){ 
        header('Content-Disposition: attachment; filename=QRcode.png'); 
        header('Content-Type: image/png'); 
        echo $file; 
    } 
	
	//save image to server
    public function save_image($file, $path = "./QRcode.png"){ 
        file_put_contents($path, $file);
    } 
}  
?>
```

Now we need to include that file inside `qr-code-post.php` right after the Plugin information:
```php
include("qrcode.php");
```

## Filter Post Content
Next, we need to add a QR code image to the end of every WordPress post. For that, we need to filter the post content and add HTML to the end of the content.

We can do this using ‘the_content’ filter. The the_content filter is used to filter the content of the post after it is retrieved from the database and before it is printed onto the screen. Note that the filter function must return the content after it is finished processing, or site visitors will see a blank page and other plugins that are also filtering the content may generate errors.

```php
function qr_code($content)
{
	$url = get_permalink();

	$qr = new qrcode();
	$qr->text($url);
	
	$html = "<p><b>QR Code:</b></p><p><img src='".$qr->get_link()."' border='0'/></p>";
	$content .= $html;

	return $content;
}

add_filter("the_content", "qr_code");
```

Let’s breakdown the above code:
1. `qr_code` is the filter function that takes the post content and filters it before the output is produced.
2. `get_permalink` function is used to get the complete URL of the current post.
3. We used the QR code library to generate a QR code image representing the permalink.
4. We added it to the end of the post content.

As you can see, it’s fairly simple if you want to do this yourself. However, you’d most likely only write code to generate a QR code if you are creating a plugin or theme. If you want to integrate QR codes to an existing website, then it would be much easier to install a ready made QR code plugin.
