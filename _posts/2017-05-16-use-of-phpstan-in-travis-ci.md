---
title: Use of PHPStan in Travis CI
date: 2017-05-16
layout: post
comments: true
published: true
description: 
keywords: 
---

If you run PHPStan on every commit, you find errors in your code without actually running it. It catches whole classes of bugs even before you write tests for the code.

## Requirements

* Travis CI (.travis.yml)
* Ant (build.xml) Ant is preinstalled on Travis CI.

## Setup

* Add a new ant target in `build.xml`

```xml
<target name="phpstan" description="PHP Static Analysis Tool - discover bugs in your code without running it">
    <mkdir dir="${basedir}/build"/>
    <get src="https://github.com/phpstan/phpstan/releases/download/0.9.2/phpstan.phar"
         dest="${basedir}/build/phpstan.phar" skipexisting="true"/>
    <exec executable="php" searchpath="true" resolveexecutable="true" failonerror="true">
        <arg value="${basedir}/build/phpstan.phar"/>
        <arg value="analyse"/>
        <arg value="-l"/>
        <arg value="5"/>
        <arg value="${basedir}/src"/>
        <arg value="${basedir}/tests"/>
        <arg value="--no-interaction"/>
        <arg value="--no-progress"/>
    </exec>
</target>
```

* Open `.travis.yml` and add this script command:

```yml
script:
  - ant phpstan
```

* Test

```bash
$ ant phpstan
```

* Commit the changes to github.

Done :-)