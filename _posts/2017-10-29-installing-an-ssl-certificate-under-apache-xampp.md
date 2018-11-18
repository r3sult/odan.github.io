---
title: Installing an SSL Certificate under Apache (XAMPP)
layout: post
comments: true
published: true
description: 
keywords: 
---

## Requirements

* XAMPP for Windows

## Setup OpenSSL

* Download Win32 OpenSSL: https://slproweb.com/products/Win32OpenSSL.html [Win32OpenSSL-1_0_2d.exe](https://slproweb.com/download/Win32OpenSSL-1_0_2d.exe)

Run this command:

```
set OPENSSL_CONF=c:\xampp\openssl\bin\openssl.cfg
```

## Create a certificate

Run

```
openssl req -new -nodes -keyout www_example_com.key -out www_example_com.csr -newkey rsa:2048
```

Reade more: https://www.psw-group.de/support/?p=20/

e.g.
```
Country: UK
State: XX
Locality name (City): London
Organisation Name: Acme AG
Organisation Unit: HeadOffice
Common-Name: www.example.com
E-Mail: info@example.com
Password: secret123
Opional company name: keep empty, press enter
```

* Order SSL cert at psw.net with file: www_example_com.csr
* Download and extract the ZIP file from psw.net

## Install the Apache SSL certificate

* Open file with Notepad++: httpd-ssl.conf
* Add the lines

```
SSLCertificateFile "conf/certs/2018/certificate/Sonstige (pem)/certificate.crt"
SSLCertificateKeyFile "conf/certs/2018/www_example_com.key"
SSLCertificateChainFile "conf/certs/2018/certificate/Sonstige (pem)/intermediate1.crt"
SSLCACertificateFile "conf/certs/2018/certificate/Sonstige (pem)/root.crt"
```

* Restart apache

## Test the SSL certificate

* Open: https://www.sslshopper.com/ssl-checker.html#hostname=https://www.example.com/