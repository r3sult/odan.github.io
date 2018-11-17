---
title: XAMPP - CURL SSL Setup
date: 2017-03-30
layout: post
comments: true
published: true
description: 
keywords: 
---

To fix the SSL certificate error message "SSL certificate error: unable to get local issuer certificate" try this:

* Download: http://curl.haxx.se/ca/cacert.pem
* Copy the file `cacert.pem` into the directory: C:\xampp\php\extras\ssl\
* Open `c:\xampp\php\php.ini` with Notepad++
* Search for the `[curl]` section
* Add the following lines:

 ```ini
[curl]
; A default value for the CURLOPT_CAINFO option. This is required to be an
; absolute path.
curl.cainfo="C:\xampp\php\extras\ssl\cacert.pem"
openssl.cafile="C:\xampp\php\extras\ssl\cacert.pem"
 ```
 
* Stop / start Apache

Click the Github ★ Star button :-)


Keywords: PHP, CURL, SSL, Twitter, CURLOPT_SSL_VERIFYPEER