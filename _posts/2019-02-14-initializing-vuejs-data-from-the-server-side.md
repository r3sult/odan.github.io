---
title: Initializing Vue.js data from the server side
layout: post
comments: true
published: true
description: 
keywords: vuejs, ssr, php, twig
---

With the help of the server-side rendered initial data set you get more speed, a better SEO score and of course more happy users. This demo does't make use of a special `Data Store` like Vuex, because I want to keep it lean and simple.

The idea is that the Vue.js `data` property is generated on the server (like the first html template) and then we place the content into the DOM in form of a JSON encoded string. When creating the Vue.js instance, we will fetch the JSON string and merge it with the existing `data` property. This works so fast that you won't have to wait any longer or see any flickering. 

In my example I use the Twig Template Engine from Symfony on the server side. 

### Preparing the templates

First we prepare our `app` element in a Twig template:

Content of file: home-index.twig

```twig
{% block content %}
    <div id="app"></div>
{% endblock %}
```

Within a vue file we add a `template` tag a `script` tag to create a Vue instance. 

Content of file: home-index.vue

```vue
<template id="user-template">
    <div id="content" class="container">
        <div class="row">
            <div class="col-md-12">
                User-ID: {{ user.id }}<br>
                Username: {{ user.username }}<br>
            </div>
        </div>
    </div>
</template>

<script>
    new Vue({
        el: "#app",
        template: '#user-template',
        data: {
            user: {},
        },
        created: function() {
            Object.assign(this, JSON.parse(document.getElementById('app-data').innerHTML.trim()));
        }
    });
</script>
```

### Loading the data on the client side

Lets take a closer look at the `created` event:

```javascript
created: function() {
    Object.assign(this, JSON.parse(document.getElementById('app-data').innerHTML.trim()));
},
```

In this case the the trimmed JSON string is getting fetched and parsed from the DOM element with the id `app-data`. Then `Object.assign` will merge the new data object to the Vue.js instance `data` property as soon as the component is created. 

### Encoding the data to JSON

The twig template just have to render the `data` variable into the correct html element. Therefore we create another template tag for the initial data. This is the place where the server generated `data` array will be encoded to JSON.

```twig
<template id="app-data">
    {{ data|json_encode(constant('JSON_HEX_QUOT') b-or constant('JSON_HEX_APOS'))|raw }}
</template>
```

In the next step we are embedding the vue file. The Twig `source` function returns the content of a vue file without rendering it. This is important because Twig and Vue use the same template syntax with`{{ }}`.


```twig
{% block content %}

    <div id="app"></div>

    <template id="app-data">
        {{ data|json_encode(constant('JSON_HEX_QUOT') b-or constant('JSON_HEX_APOS'))|raw }}
    </template>

    {{ source('Home/home-index.vue') }}

{% endblock %}
```

Note: This is just an example template, in real live this Twig template would be embeded in another layout template.

### Rendering the data on the server side

On the server side we fetch data from the database. Note that this example shows only a "static" array of data. In a real live app, the result set would come from a database or other data source.

Content of file: HomeIndexAction.php

```php
<?php

namespace App\Action;

use Psr\Http\Message\ResponseInterface;
use Slim\Http\Request;
use Slim\Http\Response;

class HomeIndexAction extends BaseAction
{

    public function __invoke(Request $request, Response $response): ResponseInterface
    {
        $user = [
            'id' => 1234,
            'username' => 'Max',
            'email' => 'max@example.com',
        ];

        $viewData = [
            'data' => [
                'user' => $user,
            ]
        ];

        return $this->render($response, 'Home/home-index.twig', $viewData);
    }
}
```

### Finished

Now Vue.js can start without waiting for the first Ajax response, and the "First Time to Interactive" is reduced to a minimum without any additional library.

Good luck :-).


![image](https://user-images.githubusercontent.com/781074/52744727-c459ad00-2fdd-11e9-90e4-d40cd40badb8.png)
