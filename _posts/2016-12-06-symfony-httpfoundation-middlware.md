---
title: Symfony HttpFoundation Middlware
date: 2016-12-06
layout: post
comments: true
published: true
description: 
keywords: 
---

Der Aufbau einer typische Symfony Middleware ist fast identisch mit einer PSR-7 middleware. 
Der einzige Unterschied ist das Symfony Request- und Response Objekt (siehe Namespace).

Eine Symfony-Middleware muss folgende Signatur besitzen:

```php
function (
    Request $request, // the request
    Response $response, // the response
    callable $next // the next callback
) {
 // ...
}
```

## Middleware (Beispiel)

```php
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

class MyMiddleware
{

   /**
    * Middleware.
    *
    * @param Request $request The request.
    * @param Response $response The response.
    * @param callable $next Callback to invoke the next middleware.
    * @return Response A response
    */
    public function __invoke(Request $request, Response $response, callable $next)
    {
        // do some fance stuff
        $response->setContent('Hello Middleware World!');

        // Call next calback and return response object
        return $next($request, $response);
    }
}
```

Als Nächstes benötigen wir einen Middleware-Dispacher der unsere callback-Funktionen ausführen soll.

```php
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

/**
 * Middleware dispatcher.
 *
 * @param Request $request Request objct
 * @param Response $response Response object
 * @param array $queue Array with middleware
 * @return Response response
 */
function relay(Request $request, Response $response, array $queue)
{
    $runner = function (Request $request, Response $response) use(&$queue, &$runner) {
        $middleware = array_shift($queue);
        if ($middleware) {
            return $middleware($request, $response, $runner);
        }
        return $response;
    };
    return $runner($request, $response);
}
```

In einer Queue definieren wir eine Liste von Middleware callables.

```php
// Create a queue array of middleware callables
$queue = [];
$queue[] = new MyMiddleware();
// add more...
```

Zum Schluss lassen wir die Queue durch den Middleware-Runner laufen und geben den Response aus.

```php
$request = Request::createFromGlobals();
$response = new Response('', 200);
$response = relay($request, $response, $queue);
$response->send();
```
## Demo App

* [Micro-App](https://github.com/odan/micro-app)
* https://github.com/odan/middleware

## Warum kein PSR-7 ?

Seit dem PSR-7 offiziell veröffentlicht wurde, sind nur wenig gute Implementierungen daraus entstanden. 
Zu der bekanntesten PSR-7 Implementierung gehört z.B. [zendframework/zend-diactoros](https://github.com/zendframework/zend-diactoros). 
In der Kritik stand das Interface-Konzept vor allem aufgrund der dogmatischen Value-Objects mit seinen 
"gewöhnungsbedürftigen" wither-Krücken. Auch der Zugriff auf GET- und POST-Daten via Array fühlt 
sich an wie Anno 1999. Bei jeder Änderung am Response-Objekt wird zuerst ein Klon mit dem neuen
Wert erzeugt. Das Erzeugen von neuen Objekten ist zwar billig, geht aber auf Kosten des 
Garbage Collectors und erzeugt memory leaks. Der gesamte Response wird in 
IO-Streams (php://temp) gespeichert. Die Verwendung des RAM's ist zwar schnell, 
führt jedoch bei grösseren Antworten >50 MB zu entsprechenden Problemen. 
Auch das direkte Streaming an Daten ist nicht möglich.

Bei all dem Dogma hat man es auch noch vergessen ein "interop" Session/Cookie-Interface zu definieren. 
Das spezielle Session-Handling mit $_SESSION hat vermutlich einfach nicht ins theoretische 
Gesamtbild gepasst und muss daher als Middleware implementiert werden. 
Von einem Standard Session-Interface kann man also noch lange träumen. 
Neuere PSR-7 Session Middleware Libraries wie z.B. [sessions/storageless](https://github.com/psr7-sessions/storageless) 
oder [PhpSession](https://github.com/oscarotero/psr7-middlewares#phpsession) sollen den Schmerz etwas lindern.

Für die praktische Anwendung von PSR-7 benötigt man einen Middleware Dispacher, 
wie [relayphp](http://relayphp.com/), der die einzelnen Middleware-Funktionen (callbacks) nacheinander 
ausführt und die Request/Response Objekte durchreicht. 
Eine echte "interop" Lösung von PHP-FIG konnte ich bisher nicht finden.

Bei all der Kritik muss man aber sagen, dass das Middleware-Konzept aus der PHP-Welt 
nicht mehr wegzudenken ist. Aus diesem Grund wollte ich es wagen, einen 
Middlware-Stack mithilfe der Symfony [HttpFoundation](http://symfony.com/doc/current/components/http_foundation.html) 
Komponente zu erstellen.

## Zusammenfassung

Dieses Konzept hat gezeigt, dass man auch mittels Symfony Komponenten einen
Middleware-Stack implementieren kann. Bei Fragen und Anregungen bitte ein Feedback hinterlassen.