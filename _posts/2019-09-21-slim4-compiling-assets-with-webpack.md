---
title: Slim 4 - Compiling Assets with Webpack
layout: post
comments: true
published: true
description: 
keywords: slim4 php webpack assets js css
---

## Table of contents

* [Requirements](#requirements)
* [Introduction](#introduction)
* [Directory structure](#directory-structure)
* [Webpack setup](#webpack-setup)
* [Twig Webpack extension setup](#twig-webpack-extension-setup)
* [Creating assets](#assets)
* [Compiling assets](#compiling-assets)
* [Useful tips](#useful-tips)
  * [Recompiling on change](#recompiling-on-change)
  * [Loading jQuery with Webpack](#loading-jquery-with-webpack)
  * [Loading Bootstrap with Webpack](#loading-bootstrap-with-webpack)
  * [Loading Fontawesome with Webpack](#loading-fontawesome-with-webpack)
  * [Loading SweetAlert2 with Webpack](#loading-sweetalert2-with-webpack)

## Requirements

* [Slim 4](http://www.slimframework.com/docs/v4/start/installation.html)
* [NPM](https://nodejs.org/en/download/)

## Introduction

If you've ever been confused and overwhelmed about getting started with Webpack and asset compilation, you should read further. 
In this tutorial we are using [Webpack](https://webpack.js.org) to bundle (compile, combine and minimize) web assets like JavaScript modules and CSS files.


## Directory structure

```
/                              the root of your project
/composer.json
/webpack.config.js             the main config file for Webpack
/templates/                    the twig templates and page specific assets
/templates/home/               
/templates/user/
/public/                       the webservers document root
/public/index.php              the front controller (application entry point)
/public/assets                 the compiled assets (webpack output)
/public/assets/manifest.json   the generated manifest file (required by the webpack twig extension)
```

## Webpack setup

Create a new `package.json` file at the root of your project. This file lists the packages your project depends on:

```json
{
    "name": "my-app",
    "version": "1.0.0",
    "license": "MIT",
    "private": true,
    "dependencies": {
    },
    "devDependencies": {
        "clean-webpack-plugin": "^3.0.0",
        "copy-webpack-plugin": "^5.0.4",
        "css-loader": "^3.2.0",
        "mini-css-extract-plugin": "^0.8.0",
        "optimize-css-assets-webpack-plugin": "^5.0.3",
        "terser-webpack-plugin": "latest",
        "webpack": "^4.40.2",
        "webpack-assets-manifest": "^3.1.1",
        "webpack-cli": "^3.3.9",
        "webpack-manifest-plugin": "^2.0.4"
    }
}

```

Create a new `webpack.config.js` file at the root of your project. This is the main config file for Webpack:

```js
const path = require('path');
const webpack = require('webpack');
const {CleanWebpackPlugin} = require('clean-webpack-plugin');
const CopyPlugin = require('copy-webpack-plugin');
const ManifestPlugin = require('webpack-manifest-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');
const TerserJSPlugin = require('terser-webpack-plugin');

module.exports = {
    entry: {
        'home/home-index': './templates/home/home-index.js'
        // here you can add more entries for each page or global assets like jQuery and bootstrap
        // 'layout/layout': './templates/layout/layout.js'
    },
    output: {
        path: path.resolve(__dirname, 'public/assets'),
    },
    optimization: {
        minimizer: [new TerserJSPlugin({}), new OptimizeCSSAssetsPlugin({})],
    },
    performance: {
        maxEntrypointSize: 1024000,
        maxAssetSize: 1024000
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [MiniCssExtractPlugin.loader, 'css-loader']
            }
        ],
    },
    plugins: [
        new CleanWebpackPlugin(),
        new ManifestPlugin(),
        new MiniCssExtractPlugin({
            ignoreOrder: false
        }),
    ],
    watchOptions: {
        ignored: ['./node_modules/']
    },
    mode: "development"
};

```

They key part is `entry`: This tells Webpack to load the `templates/home/home-index.js` file and follow all of the require statements. 
It will then package everything together and - thanks to the first `user/user` parameter - output final `home-index.js` and `home-index.css` files into the `public/assets/home/` directory. Later you can add more page-specific JavaScript or CSS to the `entry` object.

Note that Webpack uses the [TerserWebpackPlugin](https://webpack.js.org/plugins/terser-webpack-plugin/) to minify your JavaScript and the [OptimizeCSSAssetsPlugin](https://github.com/NMFR/optimize-css-assets-webpack-plugin) to minify your CSS files.

To install webpack and all dependencies, run:

```
npm install
```

### Twig Webpack extension setup

Now we install the Twig Webpack extension with composer:

```
composer require fullpipe/twig-webpack-extension
```

Register the WebpackExtension Twig extension: 

```php
// $app is the Slim 4 App instance
// We need the basePath to configure the correct public assets path
$basePath = $app->getBasePath();

// ...

$twig->addExtension(new \Fullpipe\TwigWebpackExtension\WebpackExtension(
    // must be a absolute path
    '/var/www/example.com/public/assets/manifest.json',
    // url path for js
    $basePath . '/assets/',
    // url path for css
    $basePath . '/assets/'
));
```

* The first parameter (manifestFile) defines the location of youre `manifest.json` file. You can also use `__PATH__` here.
* The second parameter (publicPathJs) defines the public path for the js files.
* The third parameter (publicPathCss) defines the public path for the css files.

## Creating assets

Next, create a new `templates/home/home-index.js` file with some basic JavaScript and import some CSS with `require`:

```js
require('./home-index.css');

alert('Hello World!');
```

Create the new `templates/home/home-index.css` file:

```css
body {
    background-color: lightgray;
}
```

Create a twig template file `templates/layout/layout.twig` with this content:

{% raw %}
```twig
<!DOCTYPE html>
<html>
    <head>
        <!-- ... -->
        
        {% block css %}{% endblock %}
    </head>
    <body>
        <!-- ... -->
        {% block content %}{% endblock %}

        {% block js %}{% endblock %}
    </body>
</html>
```
{% endraw %}

Create a twig template file `templates/home/home-index.twig` with this content:

{% raw %}
```twig
{% extends "layout/layout.twig" %}

{% block css %}
    {% webpack_entry_css 'home/home-index' %}
{% endblock %}

{% block js %}
    {% webpack_entry_js 'time/time-index' %}
{% endblock %}

{% block content %}

Welcome

{% endblock %}
```
{% endraw %}

Notice: The 'home/home-index' must match the first key of the `entry` item in webpack.config.js.

The `webpack_entry_css` and `webpack_entry_js` will fetch the url from the webpack `manifest.json` file and outputs the appropriate html link tags. For example:

```html
<link type="text/css" href="/assets/home/home-index.css" rel="stylesheet">
<script type="text/javascript" src="/assets/home/home-index.js"></script>  
```

## Compiling assets

To build the assets for development, run:

```
npx webpack --mode=development
```

To compile and minify the assets for production, run:

```
npx webpack --mode=production
```

**Congrats!** You now have three new files:

* `public/assets/home/home-index.js`  (holds all the JavaScript for your "home/home-index" entry)
* `public/assets/home/home-index.css` (holds all the CSS for your "home/home-index" entry)
* `public/assets/manifest.json` (holds all the entries and filenames)

## Useful tips

### Recompiling on change

Webpack can watch and recompile files whenever they change.

```
npx webpack --watch
```

To stop the webpack watch process, press `Ctrl+C`.

Read more:
* [Webpack Watch and WatchOptions](https://webpack.js.org/configuration/watch/)

### Loading jQuery with Webpack

jQuery uses a a global variable `window.jQuery` and the alias `window.$`. The problem ist that Webpack will wrap all modules within a closure function to protect the global scope. For this reason we have to bind the jQuery instance to the global scope manually.

To install jQuery, run:

```
npm install jquery
```

The browser must load jQuery before other jQuery plugins can be used.
For this reason, I would recommend bundling jQuery into a generally available asset file.

Add a new weback entry in `webpack.config.js`:

```js
module.exports = {
    entry: {
        'layout/layout': './templates/layout/layout.js',
        // ...
    },
    // ...
};
```

Bind jQuery to the global scope in your webpack entry point `templates/layout/layout.js`:

```js
window.jQuery = require('jquery');
window.$ = window.jQuery;
```

Add the assets {% raw %}`{% webpack_entry_css 'layout/layout' %}`{% endraw %} and {% raw %}`{% webpack_entry_js 'layout/layout' %}`{% endraw %} to the Twig template `layout/layout.twig`:

{% raw %}
```twig
<!DOCTYPE html>
<html>
    <head>
        <!-- ... -->
        
        {% webpack_entry_css 'layout/layout' %}
        
        {% block css %}{% endblock %}
        
        {% webpack_entry_js 'layout/layout' %}
    </head>
    <body>
        <!-- ... -->
        {% block content %}{% endblock %}

        {% block js %}{% endblock %}
    </body>
</html>
```
{% endraw %}

Compile all assets:

```
npx webpack
```

### Loading Bootstrap with Webpack

Bootstrap 4 uses jQuery and Popper.js for JavaScript components (like modals, tooltips, popovers etc).
You have to [setup jQuery for Webpack](#loading-jquery-with-webpack) first.

To install Bootstrap, run:

```
npm install bootstrap
```

Import boostrap in a global available webpack entry point like: `templates/layout/layout.js`:

```js
window.jQuery = require('jquery');
window.$ = window.jQuery;

import 'bootstrap';
import 'popper.js';
import 'bootstrap/dist/css/bootstrap.css';
```

Compile all assets:

```
npx webpack
```

### Loading Fontawesome with Webpack

[Fontawesome](https://fontawesome.com/) is the world's most popular and easiest to use icon set.

To install Fontawesome, run:

```
npm install @fortawesome/fontawesome-free
```

We want to copy the Fontawesome fonts automatically into the assets directory. The copy-webpack-plugin copies individual files or entire directories, which already exist, to the build directory.

To install the copy-webpack-plugin, run:

```
npm install copy-webpack-plugin --save-dev
```

Import Fontawesome in a global available webpack entry point like: `templates/layout/layout.js`:

You can import all fontawesome icons...
```js
import '@fortawesome/fontawesome-free/js/all';
```

... or you can import only a specific set of icons:

```js
import '@fortawesome/fontawesome-free/js/fontawesome';
import '@fortawesome/fontawesome-free/js/v4-shims';
import '@fortawesome/fontawesome-free/js/regular';
import '@fortawesome/fontawesome-free/js/solid';
import '@fortawesome/fontawesome-free/js/brands';
```

To copy the fonts into the `assets/webfonts/` directory, add this entry to your `webpack.config.js` file:

```js
const CopyPlugin = require('copy-webpack-plugin');
```

```js
  plugins: [
        // ...
        new CopyPlugin([
            // Fontawesome
            {from: './node_modules/@fortawesome/fontawesome-free/webfonts/', to: 'webfonts/'},
        ]),
    ],
```

Compile all assets:

```
npx webpack
```

### Loading SweetAlert2 with Webpack

[SweetAlert2](https://sweetalert2.github.io/) is a beautiful, responsive, customizable and accessible replacement for JavaScript's popup boxes.

To install SweetAlert2, run:

```
npm install sweetalert2
```

Import the sweetalert2 module and bind `Swal` to the global scope:

```js
window.Swal = require('sweetalert2');
```

Compile all assets:

```
npx webpack
```

Example:

```js
Swal.fire(
  'Good job!',
  'You clicked the button!',
  'success'
);
```
