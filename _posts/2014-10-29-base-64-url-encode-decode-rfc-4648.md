---
title: Base 64 url encode/decode (RFC 4648)
layout: post
comments: true
published: true
description: 
keywords: php
---

```php

// Base 64 Encoding with URL and Filename Safe Alphabet.
// Details RFC 4648: http://tools.ietf.org/html/rfc4648#section-5
// License: MIT
function base64url_encode($data)
{
    return str_replace('=', '', strtr(base64_encode($data), '+/', '-_'));
}

function base64url_decode($base64)
{
    return base64_decode(strtr(str_repeat('=', (4 - (strlen($base64) % 4))), '-_', '+/'));
}
```

