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

## Installation

Download and install the 32-Bit version of [OpenSSL](https://slproweb.com/products/Win32OpenSSL.html)

* OpenSSL 32-Bit: [Win32OpenSSL-1_1_0j](https://slproweb.com/download/Win32OpenSSL-1_1_0j.exe)

## Configuration

* OpenSSL requires a "openssl.cnf" configuration file. Enter this command:

```
set OPENSSL_CONF=C:\OpenSSL-Win32\bin\openssl.cfg
```

## Create a certificate

Run

```
openssl req -new -nodes -keyout www_example_com.key -out www_example_com.csr -newkey rsa:2048
```

Reade more: <https://www.psw-group.de/support/?p=20/>

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
Optional company name: keep empty, press enter
```

* Order a real SSL certificate, e.g. from <https://letsencrypt.org/>, psw.net, GlobalSign, Sectigo, Thawte with the certificate request file: `www_example_com.csr`

## Install the Apache SSL certificate

* Open the file `C:\xampp\apache\conf\extra\httpd-ssl.conf` with Notepad++
* Add these lines:

```
SSLCertificateFile "conf/ssl.crt/certificate.crt"
SSLCertificateKeyFile "conf/ssl.key/www_example_com.key"
SSLCertificateChainFile "conf/ssl.crt/intermediate1.crt"
SSLCACertificateFile "conf/ssl.crt/root.crt"
```

* Restart apache

## Test the SSL certificate

To test the certificate online open: <https://www.sslshopper.com/ssl-checker.html#hostname=https://www.example.com/>
