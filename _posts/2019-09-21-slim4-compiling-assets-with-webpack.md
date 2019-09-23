---
title: Slim 4 - Compiling Assets with Webpack
layout: post
comments: true
published: true
description: 
keywords: slim4 php webpack assets js css
---

## Requirements

* [Slim 4](http://www.slimframework.com/docs/v4/start/installation.html)
* [Slim Framework Twig View](https://github.com/slimphp/Twig-View) `^3.0.0-beta`
* [Twig Webpack extension](https://github.com/fullpipe/twig-webpack-extension)
* [NPM](https://nodejs.org/en/download/)

## Introduction

If you've ever been confused and overwhelmed about getting started with Webpack and asset compilation, you should read further. 
In this tutorial we are using [Webpack](https://webpack.js.org) to bundle (compile, combine and minimize) web assets like JavaScript modules and CSS files.


## Directory structure

```
/                              the root of your project
/composer.json
/webpack.config.js             the main config file for Webpack
/templates/
/templates/user/user.js        javascript for the user page
/templates/user/user.css       css for the user page
/templates/user/user.twig      the user page twig template
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
        "@fortawesome/fontawesome-free": "^5.7.0",
        "bootstrap": "^4.2.1",
        "datatables.net-bs4": "^1.10.19",
        "datatables.net-responsive-bs4": "^2.2.3",
        "datatables.net-select-bs4": "^1.3.0",
        "jquery": "^3.3.1",
        "popper.js": "^1.14.7",
        "sweetalert2": "^8.13.0"
    },
    "devDependencies": {
        "clean-webpack-plugin": "^3.0.0",
        "css-loader": "^3.2.0",
        "mini-css-extract-plugin": "^0.8.0",
        "optimize-css-assets-webpack-plugin": "^5.0.3",
        "webpack": "^4.40.2",
        "webpack-assets-manifest": "^3.1.1",
        "webpack-cli": "^3.3.9",
        "webpack-manifest-plugin": "^2.0.4",
        "terser-webpack-plugin": "latest"
    }
}

```

Create a new `webpack.config.js` file at the root of your project. This is the main config file for Webpack:

```js
const path = require('path');
const TerserJSPlugin = require('terser-webpack-plugin');
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const ManifestPlugin = require('webpack-manifest-plugin');
const {CleanWebpackPlugin} = require('clean-webpack-plugin');

module.exports = {
    entry: {
        'home/home-index': './templates/home/home-index.js'
        // here you can add more entries for each page or global assets like jQuery and bootstrap
        // ...
    },
    output: {
        path: path.resolve(__dirname, 'public/assets'),
    },
    optimization: {
        minimizer: [new TerserJSPlugin({}), new OptimizeCSSAssetsPlugin({})],
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [MiniCssExtractPlugin.loader, 'css-loader']
        }],
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

## Add assets

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

* The first parameter (manifestFile) defines the location of youre `manifest.json` file.
* The second parameter (publicPathJs) defines the public path for the js files.
* The third parameter (publicPathCss) defines the public path for the css files.

To build the assets for development, run:

```
npx webpack --mode=development
```

To compile and minify the assets for production, run:

```
npx webpack --mode=production
```

**Congrats!** You now have three new files:

* `public/assets/home/home-index.js`  (holds all the JavaScript for your "user/user" entry)
* `public/assets/home/home-index.css` (holds all the CSS for your "user/user" entry)
* `public/assets/manifest.json` (holds all the entries and filenames)

## Useful tips

Webpack can watch and recompile files whenever they change.

```
npx webpack --watch
```

To stop the webpack watch process, press `Ctrl+C`.

Read more:
* [Webpack Watch and WatchOptions](https://webpack.js.org/configuration/watch/)

