---
title: REST, RESTful, REST-like API Resources
date: 2017-01-30
layout: post
comments: true
published: true
description: 
keywords: 
---

## Paper
* Roy Fielding's paper on REST (where the word comes from) - https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm
* REST APIs must be hypertext-driven - http://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven
* Dissertation: Architectural Styles and
the Design of Network-based Software Architectures - http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm

## Blog posts
* **Richardson Maturity Model** - https://martinfowler.com/articles/richardsonMaturityModel.html
* HATEOAS 101: Introduction to a REST API Style (video & slides)- https://apigee.com/about/blog/technology/hateoas-101-introduction-rest-api-style-video-slides
* Learn REST: A RESTful Tutorial - http://www.restapitutorial.com/
* Good Practices in API Design - https://swaggerhub.com/blog/api-design/api-design-best-practices/
* 7 Tips for Designing a Better REST API - http://www.kennethlange.com/posts/7_tips_for_designing_a_better_rest_api.html
* Joshua Bloch: Bumper-Sticker API Design - https://www.infoq.com/articles/API-Design-Joshua-Bloch
* Best Practices for Designing a Pragmatic RESTful API - http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api
* Act Three: The Maturity Heuristic - https://www.crummy.com/writing/speaking/2008-QCon/act3.html
* There’s a model hiding in your REST API - http://transmission.vehikl.com/theres-a-model-hiding-in-your-rest-api/
* The Ultimate Guide to API Design - https://blog.qmo.io/ultimate-guide-to-api-design/#documentation
* RESTful Webservices (german) - https://www.mittwald.de/blog/webentwicklung-webdesign/webentwicklung/restful-webservices-1-was-ist-das-uberhaupt

## What is HATEOAS ?

* Wiki - https://en.wikipedia.org/wiki/HATEOAS

Humans can follow links, even as a website goes through significant changes. Code can follow links, but can't make clear independent decisions when significant changes happen to the API.

## Specifications

`There is no REST specification, REST is a architecture style.`

Read more: http://stackoverflow.com/questions/19884295/soap-vs-rest-differences/19884975#19884975

* Specification for REST APIs (OpenAPI) - http://swagger.io/specification/

## REST API Guidelines

* Microsoft REST API Guidelines - https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md
* Google API Design Guide - https://cloud.google.com/apis/design/
* RESTful APIs - http://tech.sparkfabrik.com/2017/03/04/php-rest-tools-showdown-series---part-1-really-restful-apis/
* Response Handling - https://blogs.mulesoft.com/dev/api-dev/api-best-practices-response-handling/
* HTTP Status Codes - http://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml
* Cient errors (400, 422) - https://developer.github.com/v3/#client-errors

## Books

* https://www.amazon.de/REST-HTTP-Entwicklung-Integration-Architekturstil/dp/3864901200
* https://www.amazon.de/RESTful-Web-APIs-Leonard-Richardson/dp/1449358063
* https://www.amazon.de/RESTful-Web-Services-Leonard-Richardson/dp/0596529260
* https://www.amazon.com/REST-Design-Rulebook-Mark-Masse/dp/1449310508
* RESTful Web Services Cookbook - http://shop.oreilly.com/product/9780596801694.do

## Tools
* Generate JSON schema from JSON - http://jsonschema.net/#/
* Graphical JSON Schema Editor (XMLSpy) - https://www.altova.com/download-json-schema-editor.html
* JSON Schema - http://json-schema.org/

## Documentation
* API documentation with swagger - http://swagger.io/
* https://bocoup.com/blog/documenting-your-api

## Building a so called "REST" API in PHP
* Building a Simple API with Vanilla PHP - https://gist.github.com/odan/e791bb29e2a1eab4ac6f2be4bbf86e12
* Building a Simple API in PHP using Slim & Eloquent - http://www.bradcypert.com/building-a-restful-api-in-php-using-slim-eloquent/
* Building a Simple API in PHP using Lumen - http://www.bradcypert.com/building-a-simple-api-in-php-using-lumen/

## Calling a so called "REST" API in PHP

* Guzzle - http://docs.guzzlephp.org/en/latest/
* Guzzle - http://guzzle3.readthedocs.io/
* Using GuzzlePHP with RESTful API's - http://josephralph.co.uk/using-guzzle-with-restful-apis-digitalocean-api/
* Httpful  - http://phphttpclient.com/
* unirest - Lightweight HTTP Request Client Libraries - http://unirest.io/
* CURL - http://stackoverflow.com/a/16701318
* Curl and PHP - http://stackoverflow.com/a/21271348
* POSTing JSON Data With PHP cURL - https://lornajane.net/posts/2011/posting-json-data-with-php-curl

## REST alternatives

* JSON-RPC 2.0 - http://www.jsonrpc.org/specification
* SOAP Webservice (XML) - https://en.wikipedia.org/wiki/SOAP
* JSON-LD / JSON for Linking Data - http://json-ld.org/
* gRPC - http://www.grpc.io/
* Micro API - http://micro-api.org/
* JSON API - http://jsonapi.org/
* Hydra - http://www.markus-lanthaler.com/hydra/
* OpenAPI Specification (v3.0) - https://www.openapis.org/blog/2017/01/24/a-new-year-a-new-specification
* GraphQL - http://graphql.org/

## Drawbacks
* REST is the new SOAP - https://medium.freecodecamp.org/rest-is-the-new-soap-97ff6c09896d
* RESTful APIs, the big lie - https://mmikowski.github.io/the_lie/
* When REST isn't Good Enough - https://www.braintreepayments.com/blog/when-rest-isnt-good-enough/
* Why is GitHub using GraphQL (and not REST)? - https://developer.github.com/early-access/graphql/#why-is-github-using-graphql

## Discussions
* https://news.ycombinator.com/item?id=13706618
* https://news.ycombinator.com/item?id=10042969