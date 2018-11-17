---
title: XAMPP: How to enable PHP OPCache
date: 2017-02-05
layout: post
comments: true
published: true
description: 
keywords: 
---

XAMPP comes with PHP OPCache already included in the bundle, you just need to enable it. To enable the extension:

* Open the file with Notepad++: `c:\xampp\php\php.ini`

* Add this line near the extention section: `zend_extension=php_opcache.dll`

* Stop/Start Apache