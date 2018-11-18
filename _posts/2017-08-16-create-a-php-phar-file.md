---
title: Create a PHP Phar file
layout: post
comments: true
published: true
description: 
keywords: 
---

Step by step

Create a file: `app/index.php`

```php
<?php 
// Filename: app/index.php
// Optional load composer autoloader
//require_once __DIR__ . '/vendor/autoload.php';
echo "App started";
```

Create a file: `create-phar.php`

```php
<?php
// The php.ini setting phar.readonly must be set to 0
$pharFile = 'app.phar';

// clean up
if (file_exists($pharFile)) {
    unlink($pharFile);
}
if (file_exists($pharFile . '.gz')) {
    unlink($pharFile . '.gz');
}

// create phar
$p = new Phar($pharFile);

// creating our library using whole directory  
$p->buildFromDirectory('app/');

// pointing main file which requires all classes  
$p->setDefaultStub('index.php', '/index.php');

// plus - compressing it into gzip  
$p->compress(Phar::GZ);
   
echo "$pharFile successfully created";
```

### Usage

Run: `php create-phar.php`

> app.phar successfully created

## Start a phar file

Run: `php app.phar`

> App started