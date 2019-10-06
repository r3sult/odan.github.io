---
title: Slim 4 - Cheatsheet and FAQ 
layout: post
comments: true
published: true
description: 
keywords: slim4 php
---

## Table of Contents

* [Documentation](#documentation)
* [Slim 4 Changes](#slim-4-changes)
* [Installation](#installation)
* [Skeletons](#skeletons)
* [Error 404](#error-404)
* [Retrieving the current route](#retrieving-the-current-route)
* [Retrieving the current route arguments](#retrieving-the-current-route-arguments)
* [Accessing the RouteParser](#accessing-the-routeparser)
* [Retrieving the base path](#retrieving-the-base-path)
* [Reading the response body](#reading-the-response-body)
* [Receiving input](#receiving-input)
* [CSRF protection](#csrf-protection)
* [SameSite cookies](#samesite-cookies)
* [Dependency Injection](#dependency-injection)

## Documentation

* <https://www.slimframework.com/docs/v4/>

## Slim 4 Changes

* <https://github.com/slimphp/Slim/wiki/Slim-4-Roadmap>
* <https://github.com/slimphp/Slim/issues/1686>
* <http://www.slimframework.com/2019/04/25/slim-4.0.0-alpha-release.html>

## Installation

* <http://www.slimframework.com/docs/v4/start/installation.html>

## Skeletons

* <https://github.com/slimphp/Slim-Skeleton>
* <https://github.com/odan/slim4-skeleton>
* <https://github.com/adriansuter/Slim4-Skeleton>

## Error 404

In the beginning many people have an issue with the error message: **Error 404 (Not found)**

First, make sure that you added the `RoutingMiddleware`:

```php
$app = AppFactory::create();

// Add Routing Middleware
$app->addRoutingMiddleware();

// ...

$app->run();
```

Second, make sure that the correct base path is set:

```php
$app->setBasePath('/my-base-path');
```

[Run Slim 4 From a Sub-Directory](http://www.slimframework.com/docs/v4/start/web-servers.html#run-from-a-sub-directory)

This library can be used as a helper to determine the correct base path:

* <https://github.com/selective-php/basepath>

## Retrieving the current route

```php
$route = \Slim\Routing\RouteContext::fromRequest($request)->getRoute();
```

## Retrieving the current route arguments

```php
$routeArguments = \Slim\Routing\RouteContext::fromRequest($request)->getRoute()->getArguments();
```

## Accessing the RouteParser

```php
$routeParser = \Slim\Routing\RouteContext::fromRequest($request)->getRouteParser();
```

## Retrieving the base path

```php
$basePath = \Slim\Routing\RouteContext::fromRequest($request)->getBasePath(),
```

## Reading the response body

```php
$body = (string)$request->getBody();
```

If the request body is still empty, it could be an bug or an issue with chunked requests:

* <https://www.jeffgeerling.com/blog/2017/apache-fastcgi-proxyfcgi-and-empty-post-bodies-chunked-transfer>

## Receiving input

To receive the submitted JSON / XML data you have to add the `BodyParsingMiddleware`:

```php
$app = AppFactory::create();

$app->addBodyParsingMiddleware(); // <--- here

// ...

$app->run();
```

More details: <https://akrabat.com/receiving-input-into-a-slim-4-application/>

## CSRF protection

Take a look a the "official" [Slim-CSRF](https://github.com/slimphp/Slim-Csrf) package. 

## SameSite cookies

With SameSite Cookies (available since PHP 7.3) you may no longer need CSRF protection:

* [CSRF is (really) dead ](https://scotthelme.co.uk/csrf-is-really-dead/)
* [SameSite Cookie Middlware](https://github.com/selective-php/samesite-cookie)

## Dependency Injection

As a general rule:

> Your entire app should be unaware of the container.

Injecting the container is an **anti-pattern**. Please declare all class dependencies in your constructor explicitly instead.

Why is injecting the container (in the most cases) an anti-pattern?

In Slim 3 the [Service Locator (anti-pattern)](https://blog.ploeh.dk/2010/02/03/ServiceLocatorisanAnti-Pattern/) was the default "style" to inject the whole (Pimple) container and fetch the dependencies from it. Please don't do this anymore!

* The Service Locator **hides the real dependencies** of your class. 
* The Service Locator violates the [Inversion of Control (IoC)](https://en.wikipedia.org/wiki/Inversion_of_control) principle of [SOLID](https://en.wikipedia.org/wiki/SOLID).

Q: How can I make it better? 

A: Use [Composition over inheritance](https://en.wikipedia.org/wiki/Composition_over_inheritance) and constructor [dependency injection](http://fabien.potencier.org/what-is-dependency-injection.html). Dependency injection is a programming practice of passing into an object it’s collaborators, rather the object itself creating them. 

Since **Slim 4** you can use modern [Dependency Injection Container (DIC)](http://fabien.potencier.org/do-you-need-a-dependency-injection-container.html) like [PHP-DI](http://php-di.org/) and [league/container](https://container.thephpleague.com/) with the awesome [autowire](https://container.thephpleague.com/3.x/auto-wiring/) feature. This means: Now you can declare all dependencies explicitly in your constructor and let the DIC inject these dependencies for you. 

To be more clear: "Composition" has nothing to do with the "Autowire" feature of the DIC. You can use composition with pure classes and without a container or anything else. The autowire feature just uses the [PHP Reflection](https://www.php.net/manual/en/book.reflection.php) classes to resolve and inject the dependencies automatically for you.
