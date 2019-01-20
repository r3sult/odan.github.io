---
title: Download link to the latest PHPStan PHAR file
layout: post
comments: true
published: true
description: 
keywords: php, phpstan
---

I am using [Apache Ant](https://ant.apache.org/) to install my PHAR tools in my projects. Updating the PHAR tools for minor/bugfix versions in multiple projects can become quite an effort.

It would be great if phpstan could provide a major-version download URL in addition to the GitHub release artifact download url.

For example PHPUnit provides such an URL: <https://phar.phpunit.de/phpunit-7.phar> for the latest PHPUnit 7 PHAR version.

Now it is very easy to download the latest copy the PHAR from the [phpstan/phpstan-shim](https://github.com/phpstan/phpstan-shim) package.

The download link to the latest phpstan PHAR file is: 

* **<https://github.com/phpstan/phpstan-shim/raw/master/phpstan.phar>**

**Downside:** This link always points to the latest dev-master - an inherently unstable version with BC breaks.

The **official** and "more stable" solution is to install the [phpstan/phpstan-shim](https://github.com/phpstan/phpstan-shim) as dev dependency and use the PHAR file from there.

Install phpstan-shim with composer: 

```bash
composer require phpstan/phpstan-shim --dev`
```

When you want a new version, you can just run `composer update` and copy the PHAR from:
 
* `{basedir}/vendor/phpstan/phpstan-shim/phpstan.phar`


## Use PHPStan with Apache Ant

Add this to your `build.xml` file:

```xml
<target name="phpstan" description="PHP Static Analysis Tool - discover bugs in your code without running it">
    <mkdir dir="${basedir}/build"/>
    <exec executable="php" searchpath="true" resolveexecutable="true" failonerror="true">
        <arg value="${basedir}/vendor/phpstan/phpstan-shim/phpstan.phar"/>
        <arg value="analyse"/>
        <arg value="-l"/>
        <arg value="max"/>
        <arg value="src"/>
        <arg value="tests"/>
        <arg value="--no-interaction"/>
        <arg value="--no-progress"/>
    </exec>
</target>
```

Then run `ant phpstan`.


