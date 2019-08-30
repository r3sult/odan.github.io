---
title: Creating a strictly typed collection of objects in PHP
layout: post
comments: true
published: false
description: 
keywords: php array generics
---

## Table of contents

* [Intro](#intro)
* [Creating a value object](#creating-a-value-object)
* [Creating a collection class](#creating-a-collection-class)
* [Usage](#usage)

<a name="intro">

## Intro </a>

[Generics](https://wiki.php.net/rfc/generic-arrays) are still a long way off and whether they will ever come like this is questionable.

Did you already know? You don't need generics, because PHP has everything to implement type-safe and future-proof collection classes.

We only need the features everyone should know since PHP 7: Arrays and [Type declarations](https://www.php.net/manual/en/functions.arguments.php#functions.arguments.type-declaration). 

Type declarations were also known as "type hints" in PHP 5. Type declarations allow functions to require that parameters are of a certain type at call time. If the given value is of the incorrect type, then an error is generated.

<a name="creating-a-value-object">

## Creating a value object <a name="intro">

First, we create a struct to store our data:

```php
<?php

final class User
{
    /**
     * @var int
     */
    public $id;

    /**
     * @var string
     */
    public $username;
}
```

<a name="creating-a-collection-class">

## Creating a collection class

Now we create a collection class to collect and retrieve our "array" of user objects.

Example:

```php
final class UserList
{
    /**
     * @var User[] The users
     */
    private $users = [];

    /**
     * Add user to list.
     *
     * @param User $user The user
     *
     * @return void
     */
    public function addUser(User $user): void
    {
        $this->users[] = $user;
    }

    /**
     * Get all users.
     *
     * @return User[] The users
     */
    public function all(): array
    {
        return $this->users;
    }
}
```

<a name="usage">

## Usage

Let's just create a new (and empty) collection object:


```php
$users = new UserList();
```

Then we add some new User objects to the collection:
```php
$users->addUser(new User(1, 'admin'));

$users->addUser(new User(2, 'max'));
```

Now we can iterate over the collection with the method `$users->all()`:

```php
foreach ($users->all() as $user) {
    echo sprintf("ID: %s, Username: %s\n", $user->getId(), $user->getUsername());
}
```