---
title: Disable Caching with .htaccess
layout: post
comments: true
published: false
description: 
keywords: 
---

```htaccess
<filesMatch "\.(html|htm|js|css)$">
  FileETag None
  <ifModule mod_headers.c>
    Header unset ETag
    Header set Cache-Control "max-age=0, no-cache, no-store, must-revalidate"
    Header set Pragma "no-cache"
    Header set Expires "Wed, 11 Jan 1984 05:00:00 GMT"
  </ifModule>
</filesMatch>
```
