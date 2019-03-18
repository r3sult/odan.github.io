---
title: Creating your first Slim Framework Application Part 2
layout: post
comments: true
published: false
description: 
keywords: php slim
---

The Slim Framework is a great micro frameworks for Web application, RESTful API's and Websites. 
This Tutorial shows how to create a very basic but flexible project for every use case.

## Table of contents

* [Requirements](#requirements)
* [Introduction](#introduction)
* [Single Action Controllers](#single-action-controllers)
* [Sessions](#sessions)
* [Business logic](#businsess-logic)
* [Repositories](#repositories)
* [Validation](#validation)
* [Handling Ajax requests](#handling-ajax-requests)

## Requirements

* PHP 7.x
* MySQL 5.7+
* Slim framework 3
* [Apache](https://gist.github.com/odan/dcf6c3155899677ee88a4f7db5aac284#install-apache-php-mysql)
* Apache [mod_rewrite](https://gist.github.com/odan/dcf6c3155899677ee88a4f7db5aac284#enable-apache-mod_rewrite) module
* [Composer](https://gist.github.com/odan/dcf6c3155899677ee88a4f7db5aac284#install-composer)

## Introduction

In the [first chapter](https://odan.github.io/2017/11/30/creating-your-first-slim-framework-application.html) we created a 
very basic app template. Now it's time to extend this project and add more logic and a more scalable architecture.

## Single Action Controllers

In real life we have to deal with a lot more then only 3 or 5 routes (url endpoints). In bigger applications 100 and more 
routes are very common. Another point is how to manage and organize all the different routings in a modern and OOP way. 

However, one thing is clear: with the Slim callbacks we can't get any further here, 
because the routing definitions including their code would simply get too big. 

In addition, the code must be made ready for refactoring so that changes to the code can be made later. 

Last but not least controller classes should stick to the SRP principle and do only one thing and 
not several things at the same time.

In classic MVC frameworks, several action methods are normally implemented for each controller class. 
The problem is that each action has different dependencies, although only one action is called at a time. 
A conventional controller class therefore has too many dependencies that must be resolved unnecessarily by the application. 

Too much responsibility therefore violates the SRP principle.

For this reason, we reduce the responsibility of a controller class to a single task (route) by providing 
the controller class with exactly one public method for processing the request and response. 

Here is an example of a very simple single action controller:

File: `src/Action/HomePingAction.php`

```php
namespace App\Action;

use Psr\Http\Message\ResponseInterface;
use Slim\Http\Request;
use Slim\Http\Response;

class HomePingAction
{
    public function __invoke(Request $request, Response $response): ResponseInterface
    {
        return $response->withJson(['success' => true]);
    }
}
```

To register a new action handler we add a routing definition in `routes.php` like this:

```php
$app->any('/ping', \App\Action\HomePingAction::class);
```

The advantage of this syntax is that our IDE now knows exactly which class is responsible for the 
route and provides us with full refactoring capabilities.

The static code analysis using `phpstan` will also benefit from this technique.

### Dependency injection

The first example was quite basic. In real life we need a database connection, a template engine, a logger and so on...

By default the Slim 3 framework will inject the container into the constructor of the Action class for us.

Then we just have to fetch the reqired dependencies from the container an put them into the class member variables.

Here we create a base class for all actions:

File: `src/Action/BaseAction.php`

```php
namespace App\Action;

use Cake\Database\Connection;
use Psr\Http\Message\ResponseInterface;
use Psr\Log\LoggerInterface;
use Slim\Container;
use Slim\Router;
use Slim\Views\Twig;

abstract class BaseAction
{
    /**
     * @var Router
     */
    protected $router;

    /**
     * @var Connection
     */
    protected $db;

    /**
     * @var Twig
     */
    protected $view;

    /**
     * Constructor.
     *
     * @param Container $container
     *
     * @throws ContainerException
     */
    public function __construct(Container $container)
    {
        $this->db = $container->get(Connection::class);
        $this->router = $container->get('router');
        $this->view = $container->get(Twig::class);
    }
}
```

Next we create a new action class and extend it from the our base class:

File: `src/Action/HelloAction.php`

```php
namespace App\Action;

use Psr\Http\Message\ResponseInterface;
use Slim\Container;
use Slim\Http\Request;
use Slim\Http\Response;

/**
 * Action.
 */
class HelloAction extends BaseAction
{
    /**
     * Action.
     *
     * @param Request $request the request
     * @param Response $response the response
     *
     * @return ResponseInterface the response
     */
    public function __invoke(Request $request, Response $response): ResponseInterface
    {
        $result = [
            'message' => 'Hello World,
            'now' => date('Y-m-d H:i:s'),
        ];

        return $response->withJson($result);
    }
}
```

Then we map the url to the action class in `routes.php`:

```php
$app->get('/hello', \App\Action\HelloAction::class);
```

Ok you can see, this was very easy. Great. But what if your action has more specific dependencies?
Very easy, add a custom `__construct` method to the action class and fetch more objects from the container.

Example file: `src/Action/UserEditAction.php`

```php
namespace App\Action;

use Psr\Log\LoggerInterface;

class UserEditAction extends BaseAction
{
    /**
     * @var LoggerInterface
     */
    protected $logger;

    /**
     * Constructor.
     *
     * @param Container $container The container
     */
    public function __construct(Container $container)
    {
        // call the parent constructor
        parent::__construct($container);

        // fetch more custom dependencies here
        $this->logger = $container->get(LoggerInterface::class);
    }

    public function __invoke(Request $request, Response $response): ResponseInterface
    {
        $this->logger->warning('my warning message');
    }
}
```




