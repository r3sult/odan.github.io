---
title: PHP, JS, CSS Coding Standards
layout: post
comments: true
published: true
description: 
keywords: 
---

## Filenames

* Filenames are case sensitive
* Allowed charset: a-zA-Z0-9-.
* Spaces are not allowed

## PHP

### Coding styles

Follow PSR-1 and PSR-2 Coding Standards.

* [PSR-1 Basic Coding Standard](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-1-basic-coding-standard.md)
* [PSR-2 Coding Style Guide](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md)

## Filenames

* Extensions: .php
* Filename: PascalCase.php
* One class per file

### Class names

* Singular
* ClassName + "Controller"
* ModelName
*  "Abstract" + AbstractName
* InterfaceName + "Interface"

### Class method names

* camleCase
* Underscore is not allowed

### Global function names

* lowercase_under_score

### Method visibility

* Class methods must defined as public or protected

```php
public function getValue()
{
    // ...
}
```

### Variable names

* camelCase
* No data type prefix
* Arrays with a collection of data should be named plural
  
```php
<?php
$userName = 'admin';
$count = 100;
$amount = 123.45;
$i = 0;
$status = true;
$rows = array();
$customer = new Customer();
$db = $this->getDb();
```

### Special variable names

```php
<?php
$result            // Mixed return value
  
// Parts of file name and path
$file              // Full filename with directory, filename and extension (/tmp/file.txt)
$fileName          // Filename with extension (file.txt)
$extension         // Filename extension without dot (txt)
$baseName          // Filename without extension (file)
$path              // Full path to directory name without filename (/tmp)
$directory         // Name of a single folder (e.g. tmp) 

// Arrays
$params            // Array with function paramameters e.g. function demo($params) { ... }
$rows              // Array of rows (from select query)
$row               // A single row
$value = 'variant datatype';
```

### Array index names

* lowercase, underscore
* singular if data type is string, int, float or oject
* plural if data type is array 

```php
$rows = array();
$rows[0] = array();
$rows[0]['username'] = 'max';
$rows[0]['status'] = true;
```
or
```php
$rows = array();
$rows[0] = array(
    'username' => '...',
    'status' => true
);
```

### Comments

* Use [PHPDoc](https://www.phpdoc.org/docs/latest/getting-started/your-first-set-of-documentation.html)

## HTML

* Google HTML&JS Style Guide: https://google.github.io/styleguide/htmlcssguide.html

### Filenames

* Extensions: .html.php or .twig
* Name: lower-kebab-case.html.php, my-fancy-page.twig

## JavaScript

### Filenames

* Extension: .js
* Use [jQuery style](http://stackoverflow.com/a/7273431): lower-kebab-case.js
* Examples: bootstrap.js, jquery.min.js, jquery-3.2.1.min.js, foo-library.bar.js

### Documentation

* Use [JSDoc](http://usejsdoc.org/) 
* [JSDoc Wiki](https://en.wikipedia.org/wiki/JSDoc)

### Variables

* camelCase
* No data type prefix
* Arrays index names should be plural
  
```js
var userName = 'admin';
var total = 100;
var amount = 29.99;
var visible = true;
var config = {}
var rows = [];
var value = 'something';
```

### Element names

* Lowercase for object and array element names
* no prefix

```js
// good
var row = {};
row.id = 100;
row.username = 'admin';

// good
var row = {
    id: 100,
    username: 'admin'
};

// bad
var row = [];
row.Id = 100;
row.UserName = 'admin';
```

## CSS

### Filenames

* Extension: .css
* Format: lower-kebab-case.css
* Examples: bootstrap-theme.css, jquery-ui.min.css

### Names

* Class name: lower-kebab-case

```css
.my-super-class-name {}
```

* ID name: lower-kebab-case

```css
#email
#field-with-long-id
#my-modal
#anchor-name
```