---
title: XAMPP - Oracle Driver Setup (v12)
layout: post
comments: true
published: true
description: 
keywords: 
---

## Requirements

This driver requires the Microsoft Visual C++ Redistributable. The redistributable can easily be downloaded on the Microsoft website as x86 or x64 edition. Notice: On a x64 computer the x86 **AND** the x64 version must be installed.

[Visual C++ Redistributable für Visual Studio 2015](https://www.microsoft.com/en-us/download/details.aspx?id=52685)

## Setup

Testest with PHP 5.6, 7.0, 7.1, 7.2

* [Download the Oracle Instant Client for Microsoft Windows (32-bit) v.12](http://www.oracle.com/technetwork/topics/winsoft-085727.html)
* Unzip file: `instantclient-basiclite-nt-12.2.0.1.0.zip`
* Copy all *.dll files: to `c:\xampp\php`
* Copy all *.dll files to `c:\xampp\apache\bin` (Yes, a second copy!)
* Make sure that the file `c:\xampp\php\ext\php_oci8_12c.dll` exists.
* Enable php extension in php.ini: `extension=php_oci8_12c.dll` (For PHP 7.2+ use `extension=oci8_12c`)
* Restart Apache

## Known issues

Especially the `WAMP` users reported that they still get the following error message: 

`PHP Warning: PHP Startup: Unable to load dynamic library 'oci8_12c'`.

In this case try to download the correct dll files from this link:

* <http://windows.php.net/downloads/pecl/releases/oci8/2.1.8> => php_oci8-2.1.8-7.2-ts-vc15-x86.zip

## Connection test
```php
<?php

error_reporting(E_ALL);

if (function_exists("oci_connect")) {
    echo "oci_connect found\n";
} else {
    echo "oci_connect not found\n";
    exit;
}

$host = 'localhost';
$port = '1521';

// Oracle service name (instance)
$db_name     = 'DBNAME';
$db_username = "USERNAME";
$db_password = "123456";

$tns = "(DESCRIPTION =
	(CONNECT_TIMEOUT=3)(RETRY_COUNT=0)
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = $host)(PORT = $port))
    )
    (CONNECT_DATA =
      (SERVICE_NAME = $db_name)
    )
  )";
$tns = "$host:$port/$db_name";

try {
    $conn = oci_connect($db_username, $db_password, $tns);
    if (!$conn) {
        $e = oci_error();
        throw new Exception($e['message']);
    }
    echo "Connection OK\n";
    
    $stid = oci_parse($conn, 'SELECT * FROM ALL_TABLES');
    
    if (!$stid) {
        $e = oci_error($conn);
        throw new Exception($e['message']);
    }
    // Perform the logic of the query
    $r = oci_execute($stid);
    if (!$r) {
        $e = oci_error($stid);
        throw new Exception($e['message']);
    }
    
    // Fetch the results of the query
    while ($row = oci_fetch_array($stid, OCI_ASSOC + OCI_RETURN_NULLS)) {
        $row = array_change_key_case($row, CASE_LOWER);
        print_r($row);
        break;
    }
    
    // Close statement
    oci_free_statement($stid);
    
    // Disconnect
    oci_close($conn);
    
}
catch (Exception $e) {
    print_r($e);
}
```

### More infos and links

* <https://blogs.oracle.com/opal/installing-xampp-for-php-and-oracle-database>
