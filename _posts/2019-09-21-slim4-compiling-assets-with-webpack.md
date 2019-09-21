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
/templates/user/user.js        the javascript for the user page
/templates/user/user.css       the css for user page
/templates/user/user.twig      the user page template
/public/                       the webroot
/public/index.php              the front controller
/public/assets                 the compiled assets
/public/assets/manifest.json   the generated manifest file (required for the twig extension)
```

## Creating the webpack.config.js File

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
It will then package everything together and - thanks to the first app argument - output final user.js and user.css files into the `public/assets` directory.

To install webpack and all dependencies, run:

```
npm install
```

Next, create a new `templates/user/user.js` file with some basic JavaScript and import some JavaScript:

```js
require('./user.css');

alert('Hello user');
```

And the new `templates/user/user.css` file:

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

Now install the Twig Webpack extension:

```
composer require fullpipe/twig-webpack-extension
```

Register the WebpackExtension Twig extension: 

```php
// ...
$basePath = $app->getBasePath();

// ...

$twig->addExtension(new \Fullpipe\TwigWebpackExtension\WebpackExtension(
    // must be a absolute path
    '/var/www/example.com/public/assets/manifest.json',
    // url path
    $basePath . '/assets/',
    $basePath . '/assets/'
));
```

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

**Tip:** Webpack can watch and recompile files whenever they change.
To stop the webpack watch process, press `Ctrl+C`.

```
npx webpack --watch
```


