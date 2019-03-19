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
* [Dependency injection](#dependency-injection)
* [Repositories](#repositories)
* [Business logic](#business-logic)
* [Validation](#validation)
* [Handling Ajax requests](#handling-ajax-requests)
* [Transformers](#transformers)

## Requirements

* PHP 7+
* MySQL 5.7+
* Slim Framework 3

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

> Of course, I know, that injecting a container inside something is an anti-pattern. But in Slim 3 it is like it is.

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

Example file: `src/Action/LoggerAction.php`

```php
namespace App\Action;

use Psr\Http\Message\ResponseInterface;
use Psr\Log\LoggerInterface;
use Slim\Container;
use Slim\Http\Request;
use Slim\Http\Response;

class LoggerAction extends BaseAction
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

## Repositories

The `Active Record Pattern` is an so called anti-pattern, because it voilates the Single Responsibility Principle of SOLID.

Instead we are implementing our persistend oriented Repositores as a [Data Mapper](https://designpatternsphp.readthedocs.io/en/latest/Structural/DataMapper/README.html), which follows the Single Responsibility Principle.

A repository improves code maintainability, testing and readability by separating `business logic` from `data access logic` and provides centrally managed and consistent access rules for a data source. Each public repository method represents a query. The return values represent the result set of a query and can be primitive/object or list (array) of them. Database transactions must be handled on a higher level (domain service) and not within a repository.

Quick summary:

* Communication with the database.
* Place for the data access logic (query logic).
* This is no place for the business logic! Use domain services for the complex business and domain logic.

### Example

Here is an example of a base repository class:

Filename: `src/Repository/BaseRepository.php`

```php
<?php

namespace App\Repository;

use Cake\Database\Connection;
use Cake\Database\Query;

/**
 * Repository (persistence oriented).
 */
abstract class BaseRepository implements RepositoryInterface
{
    /**
     * Connection.
     *
     * @var Connection
     */
    protected $connection;

    /**
     * Constructor.
     *
     * @param Connection $db
     */
    public function __construct(Connection $connection)
    {
        $this->connection = $connection;
    }

    /**
     * Create a new query.
     *
     * @return Query
     */
    protected function newQuery(): Query
    {
        return $this->connection->newQuery();
    }

    /**
     * Create a new select query.
     *
     * @param string $table The table name
     *
     * @return Query A select query
     */
    protected function newSelect(string $table): Query
    {
        $query = $this->newQuery()->from($table);

        if (!$query instanceof Query) {
            throw new RuntimeException('Failed to create query');
        }

        return $query;
    }
    
    /**
     * Executes an UPDATE statement on the specified table.
     *
     * @param string $table the table to update rows from
     * @param array $data values to be updated [optional]
     *
     * @return Query Query
     */
    protected function newUpdate(string $table, array $data = []): Query
    {
        return $this->newQuery()->update($table)->set($data);
    }

    /**
     * Executes an UPDATE statement on the specified table.
     *
     * @param string $table the table to update rows from
     * @param array $data values to be updated
     *
     * @return Query Query
     */
    protected function newInsert(string $table, array $data): Query
    {
        $columns = array_keys($data);

        return $this->newQuery()->insert($columns)
            ->into($table)
            ->values($data);
    }

    /**
     * Create a DELETE query.
     *
     * @param string $table the table to delete from
     *
     * @return Query Query
     */
    protected function newDelete(string $table): Query
    {
        return $this->newQuery()->delete($table);
    }
    
    /**
     * Fetch row by id.
     *
     * @param string $table Table name
     * @param int $id ID
     *
     * @return array Result set
     */
    protected function fetchById(string $table, int $id): array
    {
        return $this->newSelect($table)
            ->select('*')
            ->where(['id' => $id])
            ->execute()
            ->fetch('assoc')?: [];
    }

    /**
     * Fetch row by id.
     *
     * @param string $table Table name
     * @param int|string $id ID
     *
     * @return bool True if the row exists
     */
    protected function existsById(string $table, $id): bool
    {
        return $this->newSelect($table)
            ->select('id')
            ->andWhere(['id' => $id])
            ->execute()
            ->fetch('assoc') ? true : false;
    }

    /**
     * Fetch all rows.
     *
     * @param string $table Table name
     *
     * @return array Result set
     */
    protected function fetchAll(string $table): array
    {
        return $this->newSelect($table)->select('*')->execute()->fetchAll('assoc') ?: [];
    }
}
```

The repository interface:

Filename: `src/Repository/RepositoryInterface.php`

```php
<?php

namespace App\Repository;

interface RepositoryInterface
{
}
```

A concrete repository which is responsible for user data could look like this:

Filename: `src/Domain/User/UserRepository.php`

```php
<?php

namespace App\Domain\User;

use App\Repository\BaseRepository;
use DomainException;

class UserRepository extends BaseRepository
{
    /**
     * Finds a user from by the given User ID and returns a User object located in memory.
     *
     * @param int $userId the user id
     *
     * @throws DomainException
     *
     * @return User the User business object
     */
    public function getById(int $userId): User
    {
        $row = $this->fetchById('users', $userId);

        if (empty($row)) {
            throw new DomainException(sprintf('User not found: %s', $userId));
        }

        return User::fromArray($row);
    }
}
```

**Return types**

In some cases, it makes more sense to return only a simple array of data or objects. In other cases (like an insert), it makes more sense to simply return the last inserted ID as an integer. For deletions, it is recommended to return only a boolean value such as `true`.

**Method names**

Use the prefix `get` to indicate that this method must return a value, otherwise throw an exception.
If the select method can return an empty result set, the method name starts with `find` and doesn't throws an exception.

**Examples**

* findUserById(int $id)
* getUserById(int $id)
* findActiveUsers(): array
* insertUser(array $data): int
* deleteUser(int $userId): bool
* updateUser(int $userId, array $data): bool

## Business logic

The business logic (e.g. calulation, validation, file creation etc.) should be placed in service classes.

The service class is invoked directly by the controller layer (in our case the action method).

Normally, a service class reads or changes data from the database. 
For this reason, we must provide the service class with a `Repository`.
You could consider these two classes (service and repository) as bundled, because they form a kind of symbiosis. 

**Example**

Filename: `src/Domain/User/UserEdit.php`

```php
<?php

namespace App\Domain\User;

class UserEdit
{
    /**
     * @var UserRepository
     */
    private $userRepository;

    /**
     * Constructor.
     *
     * @param UserRepository $userRepository The repository
     */
    public function __construct(UserRepository $userRepository)
    {
        $this->userRepository = $userRepository;
    }

    /**
     * Get user by ID.
     *
     * @param int $userId The user ID
     *
     * @return User The data
     */
    public function getUserById(int $userId): User
    {
        return $this->userRepository->getById($userId);
    }
}

```

## Validation

For security reasons, server-side validation is a MUST. Client-side validation is useful for the usability.

There are countless concepts and validation libraries to help you validate the potentially dangerous user input.

Slim does not have a validation functionality.

We have to decide for ourselves which is the best solution for our specific requirements. 

One of the most popular validation engines for PHP is [Respect\Validation](https://github.com/Respect/Validation).
You can try it and maybe it's a good solution for you.

Personally, I don't use a special validation library, because PHP itself offers more flexible and better possibilities for input validation. The only solution that **really works** for me in a large enterprise application was [Martin Fowler's validation concept](https://martinfowler.com/articles/replaceThrowWithNotification.html).

If you like to read more about this topic, please contact me or write a comment.

## Handling Ajax requests

Today we have multiple different possiblilities to invoke an Ajax request from the client side like jQuery, Axios or the native `fetch` function. I still prefer jQuery bacause it's bulletproof and simply works in any browser.

### Client side


### Server side
