---
title: Download link to the latest phpstan phar file
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

## Download with curl

```bash
curl https://github.com/phpstan/phpstan-shim/raw/master/phpstan.phar
```

To download the file into the `build/` directory use:

```bash
curl https://github.com/phpstan/phpstan-shim/raw/master/phpstan.phar --output build/phpstan.phar
```

## Download with Apache Ant

Add this to your `build.xml` file:

```xml
<target name="phpstan" description="PHP Static Analysis Tool - discover bugs in your code without running it">
    <mkdir dir="${basedir}/build"/>
    <get src="https://github.com/phpstan/phpstan-shim/raw/master/phpstan.phar"
         dest="${basedir}/build/phpstan.phar" skipexisting="true"/>
    <exec executable="php" searchpath="true" resolveexecutable="true" failonerror="true">
        <arg value="${basedir}/build/phpstan.phar"/>
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


