---
title: Logging minor errors (like notice and warnings) with Monolog in Slim 3
date: 2018-10-28
layout: post
comments: true
published: true
description: 
keywords: slim, slimphp, php, monolog, logging
---

In Slim 3 all PHP exceptions and Slim Framework specific application errors can be handled by a custom[phpErrorHandler](https://www.slimframework.com/docs/v3/handlers/error.html). But sometimes you also have to log all minor errors like `notice` or `warning`. In this case it's also possible to handle and log all errors like this in a special middleware.

### Setup monolog

```
// container.php

use Monolog\Logger;
use Psr\Container\ContainerInterface as Container;
use Psr\Log\LoggerInterface;

$container[LoggerInterface::class] = function (Container $container) {
    $settings = $container->get('settings');
    $logger = new Logger($settings['logger']['name']);

    $level = $settings['logger']['level'];
    if (!isset($level)) {
        $level = Logger::ERROR;
    }
    $logFile = $settings['logger']['file'];
    $handler = new RotatingFileHandler($logFile, 0, $level, true, 0775);
    $logger->pushHandler($handler);

    return $logger;
};
```

### Setup the Slim erorr handler

Here is an example code to log all Exceptions in Slim 3 with Monolog.

```php
// container.php

use Psr\Container\ContainerInterface as Container;
use Psr\Log\LoggerInterface;
use Slim\Http\Request;
use Slim\Http\Response;

// Slim Framework application error handler
$container['errorHandler'] = function (Container $container) {
    $logger = $container->get(LoggerInterface::class);

    return function(Request $request, Response $response, Throwable $exception) use ($logger) {
        $logger->error($exception->getMessage());
    };
};

// PHP exceptions handler
$container['phpErrorHandler'] = function (Container $container) {
    return $container->get('errorHandler');
};
```

### Setup the the middleware to handle all minor errors

The problem is that Slim's default error handler will not handle all the PHP specific `warning` and `notice` errors for you.

To fix this, just add new middleware to handle all errors that cannot be handled by the Slim error handler:

```php
// middleware.php

use Psr\Log\LoggerInterface;
use Slim\Http\Request;
use Slim\Http\Response;

// Middleware to handle minor errors
$app->add(function (Request $request, Response $response, $next) {
    /** @var \Psr\Log\LoggerInterface $logger */
    $logger = $this->get(LoggerInterface::class);

    // error handler function
    $myHandlerForMinorErrors = function ($errno, $errstr, $errfile, $errline) use ($response, $logger) {
        switch ($errno) {
            case E_USER_ERROR:
                $logger->error("Error number [$errno] $errstr on line $errline in file $errfile");
                break;
            case E_USER_WARNING:
                $logger->warning("Error number [$errno] $errstr on line $errline in file $errfile");
                break;
            case E_USER_NOTICE:
                $logger->notice("Error number [$errno] $errstr on line $errline in file $errfile");
                break;
            default:
                $logger->notice("Error number [$errno] $errstr on line $errline in file $errfile");
                break;
        }
        
        // Optional: Write error to response
        //$response = $response->getBody()->write("Error: [$errno] $errstr<br>\n");

        // Don't execute PHP internal error handler
        return true;
    };

    // Set custom php error handler for minor errors
    set_error_handler($myHandlerForMinorErrors, E_NOTICE | E_STRICT);

    return $next($request, $response);
});
```

The expected logfile output should look like this:

```
[2018-10-28 10:10:05] app.NOTICE: Error number [8] Undefined index: nada on line 30 in file filename.php [] []
``` 
