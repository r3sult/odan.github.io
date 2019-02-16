---
title: Correct owner and permissions of apache "var/www/html"
layout: post
comments: true
published: true
description: 
keywords: apache, ubuntu
---

Using Apache2 with Ubuntu may cause problems with permissions and FTP clients. 

Some CMSes and Wordpress is especially bad about that because it's actually in the code to use the web user.

BTW you should never need to use `root` for ftp. `www-data` the default apache user on ubuntu should own your web files/directory to work properly with many cmses.

So this is what has worked before and what we did for clients with the same issue. 

chown the whole web root as `www-data` for both `user and group`.

### Add your FTP user to the `www-data` group

```
usermod -a -G www-data username
```

... or create a new user:

```
sudo adduser username www-data
```

Replace usename with your SFTP client username.

### Change the group, owner and set the permissions

If your document root is `/var/www/html` run this to change ownership on all files and directories.

```
sudo chgrp www-data /var/www/html/
sudo chown -R www-data: /var/www/html/
sudo chmod -R 2774 /var/www/html/
```

You must login again with the SFTP client

Now this setup should allow you to use manage files and still allow the CMS ftp backend to still function and write to the direc. 
Let me know how that works for you.

### Read more

* <https://askubuntu.com/questions/244406/how-do-i-give-www-data-user-to-a-folder-in-my-home-folder>
* <https://gist.github.com/solancer/879105abb05eb7f13ce23e822ed70dd6>
* <https://askubuntu.com/questions/79565/how-to-add-existing-user-to-an-existing-group>
* <https://stackoverflow.com/questions/28918996/proper-owner-of-var-www-html>
