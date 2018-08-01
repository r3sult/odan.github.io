---
title: Signing an XML file with a .pfx certificate
date: 2018-08-01
layout: post
comments: true
published: false
description: 
keywords: php openssl signing
---

## Requirements

* PHP 7.x
* openssl extension

## Intro



## Full example

```php
// read the pfx file
$cert_store = file_get_contents('localhost.pfx');

// just pass the correct password here
$status = openssl_pkcs12_read($cert_store, $cert_info, '12345678');

if (!$status) {
    throw new RuntimeException(__('Invalid pasword'));
}

$public_key = $cert_info['cert'];
$private_key = $cert_info['pkey'];

// get the private key
$pkeyid = openssl_get_privatekey($private_key);

if (!$pkeyid) {
    throw new RuntimeException(__('Invalid private key'));
}

// read the xml file content
$data = file_get_contents('myfile.xml');

// compute signature with SHA-512
$status = openssl_sign($data, $signature, $pkeyid, OPENSSL_ALGO_SHA512);

if (!$status) {
    throw new RuntimeException(__('Sign failed'));
}

// save the signatue binary data
file_put_contents('signature.txt', $signature);

// free the key from memory
openssl_free_key($pkeyid);

// check signature
$binary_signature = file_get_contents('signature.txt');

$ok = openssl_verify($data, $binary_signature, $public_key, OPENSSL_ALGO_SHA512);

echo "check: ";
if ($ok == 1) {
    echo "signature ok (as it should be)\n";
} elseif ($ok == 0) {
    echo "bad (there's something wrong)\n";
} else {
    echo "ugly, error checking signature\n";
}
```
