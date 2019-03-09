---
title: How to use PHPStan in Travis CI
layout: post
comments: true
published: true
description: 
keywords: 
---

If you run PHPStan on every commit, you find errors in your code without actually running it. It catches whole classes of bugs even before you write tests for the code.

## Requirements

* PHP
* Travis CI (.travis.yml)
* [PHPStan-Shim](https://github.com/phpstan/phpstan-shim)

## Installation

phpstan-shim provides easy way to install PHPStan without the risk of conflicting dependencies.

Install the package

```
composer require --dev phpstan/phpstan-shim
```

## PHPStan configuration

* Create a empty file `phpstan.neon`

## Travis Setup

* Open `.travis.yml` and add this script command:

```yml
script:
  - vendor/bin/phpstan.phar analyse -l max -c phpstan.neon src --no-interaction --no-progress
```

* Commit the changes to github.

Done :-)
