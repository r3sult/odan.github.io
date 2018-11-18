---
title: Framework configuration research
layout: post
comments: true
published: false
description: 
keywords: 
---

Naming Conventions for service and parameter names in the service container and for names of templates and configuration files.

## Symfony

* Naming convention: lowercase (lower_under), words separated by underscores, section separated by dot 
* Docs: http://symfony.com/doc/current/service_container/parameters.html
* Example: my_multilang.language_fallback
* More examples:
* http://symfony.com/doc/current/best_practices/configuration.html
* https://github.com/symfony/config/blob/master/Tests/Fixtures/Configuration/ExampleConfiguration.php 
* Naming Conventions: http://symfony.com/doc/current/contributing/code/standards.html#naming-conventions

## Zend
* Naming convention: lowercase (lower_under), words separated by underscores, section separated by dot
* Array: multidimensional
* Docs: http://zf2.readthedocs.io/en/latest/modules/zend.config.introduction.html
* Example (from ini file): database.params.host = 'db.example.com'
* Get a value: echo $data['database']['params']['host']['value'];

## Laravel 

* Naming convention: lowercase (lower_under), words separated by underscores, section separated by sub array
* Example: fallback_locale, log_level
* Get a value: $value = config('app.timezone');
* Set a value: config(['app.timezone' => 'America/Chicago']);
* Docs: https://laravel.com/docs/master/configuration
* Example: https://github.com/laravel/laravel/blob/master/config/database.php

## CakePHP 3.x

* Naming convention:
*  1. Level: capitalized (e.g. EmailTransport)
*  2+. Level: camelCase  (e.g. className)

* Set a value: Configure::write('App.baseUrl', env('SCRIPT_NAME'));
* Get a value: Configure::read('App.baseUrl');
* Example: https://github.com/cakephp/app/blob/master/config/app.default.php

## Slim

* Naming convention: camelCase  (e.g. className), section separated by sub array
* Set a value: $app = new \Slim\App(['settings' => 'displayErrorDetails' => true]);
* Get a value: $settings = $container->get('settings')['logger'];
* Docs: https://www.slimframework.com/docs/objects/application.html
* Docs: http://docs.slimframework.com/configuration/settings/

## Silex

* Naming convention: lowercase (lower_under), words separated by underscores, section separated by dot 
* Example: monolog.exception.logger_filter
* Example 2: http://silex.sensiolabs.org/doc/master/providers.html
* Example link: http://silex.sensiolabs.org/doc/master/providers/monolog.html
* Docs: http://silex.sensiolabs.org/doc/master/

## Phalcon

* Naming convention: camelCase  (e.g. className), section separated by sub array
* Example: controllersDir 
* Docs: https://docs.phalconphp.com/en/3.0.0/api/Phalcon_Config.html

## CodeIgniter

* Naming convention:  lowercase (lower_under)
* Example:  item_value  blog_settings
* Get value: $site_name = $this->config->item('site_name', 'blog_settings');
* Set value: $this->config->set_item('item_name', 'item_value');
* Docs: https://www.codeigniter.com/userguide3/libraries/config.html#anatomy-of-a-config-file

## Yii 2
* Naming convention: camelCase  (e.g. className), section separated by sub array
* Example: 'basePath' => dirname(__DIR__)
* Docs: http://www.yiiframework.com/doc-2.0/guide-concept-configurations.html

## Aura
* Naming convention: ???
* Example:  
* Docs: http://auraphp.com/framework/2.x/en/configuration/

## Fat-Free

* Naming convention: Mixed UPPERCASE and lowercase (lower_under)
* Example:  https://github.com/bcosca/fatfree/blob/master/config.ini
* Docs: 

## FuelPHP
* Naming convention: lowercase (lower_under), words separated by underscores, section separated by dot 
* Get value: Config::get('items_to_display') 
* Get value: Config::get('db.active');
* Set value: Config::set('blog.items_to_display', 5);
* Docs: http://fuelphp.com/dev-docs/classes/config.html

## nova-framework

* Naming convention: lowercase (lower_under)
* Example: 'color_scheme' => 'blue',
* Example:  https://github.com/nova-framework/framework/blob/3.0/app/Config/App.php