---
title: PHP Best Practices
date: 2018-04-05
layout: post
comments: true
published: true
description: 
keywords: 
---

## Principles and rules

All code must follow:

* Clean Code by Uncle Bob.
* DRY, KISS and SOLID.
* Continuous Refactoring. If you edit a method clean it: Boy scout rule.
* Drink water

## Coding styles

* All files PHP files must be styled with PSR-1, PSR-2.
* Maximum number of method lines: 25.
  * Except for long queries in repositories and test provider methods.
* Maximum number of class lines: 1000.
* Maximum line length 120 chars.
* Multiline IF condition is not allowed -> move it to a method or variable.
* `if ($var)` allowed only if `$var` is a boolean

Allowed:

```php
if (count(stringVar) > 0)
if (empty(stringVar))
```

Not allowed:

```php
if ($stringVar)
if ($integerVar)
if ($objecctVar)
```

* Always add full DocBlock for all classes and all methods. (PSR-5) 
* Add an empty line before return statements to increase code clarity, except when the return is alone inside a statement group (such as an if statement).

* Follow this [PHP, JS, CSS Coding Standard](https://gist.github.com/odan/0803ef7249b103167a03d8f978b8d4b4)

## Naming things

* Use english names only (variable, class, file, etc…)
* All variable must be `$lowerCamelCase`. Underscore is not allowed.
* All method names must be a verb

```php
doSomething()
getSomething()
deleteSomething()
clearSometing()
```

* If the return type is a boolean the name must be start with "is" or "has"

## Common rules

* Don't use `static::` if you don’t want to use late static binding (use `self::`).
* Don't use `DateTime` (not immutable), use only the `DateTimeImmutable` class.
* Don't use any `new` keyword in Services / Repos or a factory method.
  * All of them must be in the action and inject the through constructor with `PHP-DI`.
  * Except for: value objects, entities and parameter objects.
* Built in functions are allowed to use in any class level, they are mockable with PHPUnit function mocker.
* Don't use any kind of reflection.
* Don't use traits.  [Composition or inheritance](https://en.wikipedia.org/wiki/Composition_over_inheritance) is a way better solution.
* Don't use " (double quote) for strings (use sprintf for insert variable to a string).
* Don't use superglobals in any low level class (if it's possible don't use it anywhere).
* Class methods should be grouped in the class by the scope, order: `public`, `protected`, `private`.
* All acceptable method parameters must be used.
* Import every class at the beginning of the file with the `use` operator.
  * No fully qualified class inside in any class, not even if it's a type hint.
  * The only exception is the PHPUnit annotation, fully qualified necessary (known bug in PHPUnit).
* Use static code analysis to detect bugs and error. 
  * For example: phpstan and the PHPStorm Code Inspector (Code > Inspect code...).

## Dependency injection

* Direction: Controller / Action > Service > Repository.
* A Constructor can accept only dependencies as object.
* Scalar data types (string, int, float, array) are not allowed. Pass them as parameter object.

## Controller/Action

* No (business) logic allowed, expect for request data validation.
* This method can have only one service call after the validation. If you have to handle multiple service calls, you can require other services in the called service.
* Always use plural names for your controllers - for examples CarsController.
* If you want to use versions for your API, just create a subdirectory inside the Controllers folder.

## Service

* Move your business logic to services - in that way you will isolate them and you will be able to treat them as ports and adapters.
* All dependencies must be injected via constructor injection.
* If two methods are using completely different dependencies they must be separated into other service: [SRP](https://app.letscode.hu/videos/single-responsibility).
* A Service can accept another Service as an dependency.
* Client side only validation not allowed. All requests must be validated in the Service layer (backend).
* If the validation is very complex, move the validation to a special validation class.

## Repository

* No transactions in a repository, must be handled in higher level (Service).
* One method can execute one query.
* The return value can be primitive/object or list (array) of them.
* If you have more than one parameter for select method, you can create a custom method.
* sprintf for SQL is not allowed. Use prepared statements or a query builder.
* Always use singular names for your repositories - for example CustomerRepository.

### Method naming rules

* getById(id) 
  * Must return an object <Entity>
  * No match throws a `DomainException` (Domain means data domain here. [Read more](https://stackoverflow.com/a/1103021/1461181))
  
* findByEmail(email):
  * Will return <Entity>[] (can be empty array)
  
* create(UserEntity $userEntity):
  * Parameter must be the <Entity> object, filled up and id=null

## Entity

* An object with data and `id`. [Read more](https://martinfowler.com/bliki/EvansClassification.html)
* An entity does not necessarily have to be immutable.
* Use it to represent a database table or an object that corresponds to a database table. Big things like Customer, Ship, Rental Agreement.

## Value Object

* Must be immutable
* Can only be filled using the constructor.
* setter methods are not allowed. 
* A getter method name does not contain a a `get` prefix. example: `public function email(): string { return $this->email; }`
* wither methods are allowed. Example `public function withEmail(string $email): self { ... }`
* If you have to change a data, you have to create a new entity object (with new name, because it has changed).
* All properties must be `protected` or `private` accessed by the getter methods.
* Use it for little things like Date, Money, CustomerId. Responsible for keeping their state consistent [1](https://kacper.gunia.me/validating-value-objects/).

## Parameter Object

* You have a group of parameters that naturally go together. Replace them with an object. [Read more](https://refactoring.com/catalog/introduceParameterObject.html)

## Type Hinting

* All methods must have type hinting (without return add `: void` as type hint)
* Methods without return statement must declared with `void` as their return type.
* Don't mix data types for parameters and return types. (Except for `nullable`)

* Allowed return type hints:
  * integer: `int`
  * float: `float`
  * string: `string`
  * class: `SomeClass`
  * array: `array` (Use it as rarely as possible)
  * integer as array: `int[]`
  * float as array: `float[]`
  * string as array: `string[]`
  * bool as array: `bool[]`
  * class as array (list): `SomeClass[]`
  
## Tests

* One method can have more than one assert.
* A test method should start with `test` for example `testInstance()`.
* Method names should be separated in 3 parts: method name, what the method do and what will be the result.
  * testApiCallTthrowExpection
  * testApiCallPerfect
  * testApiCallReturnFalse

* If something is annotable, annotate it (like `@expectedException`)
* Add `@coversDefaultClass` to the test class. Test only ene class per test class.
* Don’t add `@covers` to a test method DocBlock. Xdebug will detect it correctly for you.

## Versioning

* Changes that belong together should also be committed together. Don't create micro commits.
* Create a `feature branch` for each new feature / bugix / pull request.

## Security

* **Do not commit any prod, staging, test or dev password, token or auth related keys. NO EXCEPTION!**
* Don't use `.env` files (for security reasons). Use a `env.php` file instead.
* Create a `env.php` file for each server environment (dev, staging, test, prod). Each file has its own values.

## PHPStorm

### Settings

* A dark color scheme is good for working in a dark room (or at night), and a bright color scheme is better for your eyes if you work with daylight.

### Plugins

* Use the PhpStorm Plugin: [Php Inspections (EA Extended)](https://plugins.jetbrains.com/plugin/7622-php-inspections-ea-extended-) to optimize your code.

## Links

* http://bestpractices.thecodingmachine.com/