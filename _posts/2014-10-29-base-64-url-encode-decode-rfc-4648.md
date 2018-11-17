---
title: Base 64 url encode/decode (RFC 4648)
layout: post
comments: true
published: true
description: 
keywords: php
---

```php
<?php
// Base 64 Encoding with URL and Filename Safe Alphabet.
// Details RFC 4648: http://tools.ietf.org/html/rfc4648#section-5
// License: MIT
function base64url_encode($data)
{
    $base64 = base64_encode($data);
    $base64 = strtr($base64, '+/', '-_');
    $base64 = str_replace('=', '', $base64);
    
    return $base64;
}
function base64url_decode($base64)
{
    $base64 .= str_repeat('=', (4 - (strlen($base64) % 4)));
    $base64 = strtr($base64, '-_', '+/');
    $result = base64_decode($base64);
    
    return $result;
}
```

