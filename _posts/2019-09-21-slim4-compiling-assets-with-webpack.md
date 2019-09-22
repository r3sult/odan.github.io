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
    "devDependencies": {
        "clean-webpack-plugin": "^3.0.0",
        "css-loader": "^3.2.0",
        "mini-css-extract-plugin": "^0.8.0",
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
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const ManifestPlugin = require('webpack-manifest-plugin');
const {CleanWebpackPlugin} = require('clean-webpack-plugin');

module.exports = {
    entry: {
        'user/user': './templates/user/user.js'
    },
    output: {
        path: path.resolve(__dirname, 'public/assets'),
    },
    module: {
        rules: [{
            test: /\.css$/,
            use: [
                {loader: MiniCssExtractPlugin.loader},
                'css-loader'
            ]
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

They key part is `entry`: this tells Webpack to load the `templates/user/user.js` file and follow all of the require statements. 
It will then package everything together and - thanks to the first `user/user` parameter - output final `user.js` and `user.css` files into the `public/assets/user/` directory.

To install webpack and all dependencies, run:

```
npm install
```

## Add assets

Next, create a new `templates/user/user.js` file with some basic JavaScript and import some CSS with `require`:

```js
require('./user.css');

alert('Hello user');
```

Create the new `templates/user/user.css` file:

```css
body {
    background-color: lightgray;
}
```

Create a twig template file `templates/user/user.twig` with this content:

{% raw %}
```twig
<!DOCTYPE html>
<html>
<head></head>
<body>
{% webpack_entry_css 'user/user' %}
{% webpack_entry_js 'user/user' %}
</body>
</html>
```
{% endraw %}

The `webpack_entry_css` and `webpack_entry_js` will fetch the url from the webpack `manifest.json` file and outputs the appropriate html tags. For example:

```html
<link type="text/css" href="/assets/user/user.css" rel="stylesheet">
<script type="text/javascript" src="/assets/user/user.js"></script>  
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

* `public/assets/user.js`  (holds all the JavaScript for your "user/user" entry)
* `public/assets/user.css` (holds all the CSS for your "user/user" entry)
* `public/assets/manifest.json` (holds all the entries and filenames)

## Useful tips

Webpack can watch and recompile files whenever they change.

```
npx webpack --watch
```

To stop the webpack watch process, press `Ctrl+C`.

Read more:
* [Webpack Watch and WatchOptions](https://webpack.js.org/configuration/watch/)

