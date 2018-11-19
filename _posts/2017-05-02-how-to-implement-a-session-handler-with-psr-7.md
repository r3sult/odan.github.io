---
title: How to implement a session handler with PSR-7
layout: post
comments: true
published: false
description: 
keywords: 
---

Pseudo code:

```php
$controller = function ($request, $blogModel, $commentsModel, $cookiesModel, $sessionModel) {
    // We can use $cookiesModel to read/set/delete cookies.
    $foo = $cookies->get('foo');
    $cookies->set('bar', 123, ...);
    $cookies->delete('baz');

    // We can use $sessionModel to start/destroy sessions, read/set/delete session state.
    $foo = $session->get('foo');
    $session->set('bar', 123);
    $session->delete('baz');

    // Imagine we have code here messing around with $blogModel, $commentsModel & building a view.
    $response = new View([
        'posts' => $blogModel->getLatest(3),
        'comments' => $commentsModel->getTopComments(10),
        'username' => $sessionModel->get('username'), // Just like any other model!
    ]);

    return $response;
};


$view = function () {
    // The view is not relevant in our use case. Sets entity headers, and body content.
};


$framework = function ($request) use ($controller) {
    $blogModel = new BlogModel(...);
    $commentsModel = new CommentsModel(...);
    $cookiesModel = new CookiesModel($request);
    $sessionModel = new SessionModel($cookiesModel); // Gets/sets session id via cookies.

    $view = $controller($request, $blogModel, $commentsModel, $cookiesModel, $sessionModel);

    $response = $view();
    $response = $response->withHeaders($cookies->getCookieHeaders());

    $response->render();
};

$framework(Request::fromGlobals()); // And here we go...

```

Source: https://www.reddit.com/r/PHP/comments/68th28/what_is_the_best_way_to_handle_sessions_with_adr/