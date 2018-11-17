---
title: Fixing a github repo which is marked as the wrong language
date: 2016-12-05
layout: post
comments: true
published: true
description: 
keywords: 
---

* Add a file .gitattributes
* Define a folder with vendor files (e.g. directory with jquery): `web/js/* linguist-vendored`
* Optional: Add documentation folder: `docs/* linguist-documentation`

Source: 
* https://help.github.com/articles/my-repository-is-marked-as-the-wrong-language/
* https://github.com/github/linguist
* https://github.com/github/linguist/issues/2396