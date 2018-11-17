---
title: This rewrite rules maps all *.html files to their *.html.php file.
date: 2017-05-24
layout: post
comments: true
published: true
description: 
keywords: 
---

For example: 

* index.html -> index.html.php
* about.html -> about.html.php

## .htaccess

```apacheconf 
RewriteEngine On

# Block *.html.php files
RewriteCond %{THE_REQUEST} ^(.*)\.php
RewriteRule ^ - [R=404,L]

# Map html to php file
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^(.*)\.html$ $1.html.php [QSA,L]

# Set default index page (optional)
RewriteRule ^$ index.html [L]
```

Source: https://www.reddit.com/r/PHPhelp/comments/6csg2s/how_to_get_server_to_parse_html_as_php_file/