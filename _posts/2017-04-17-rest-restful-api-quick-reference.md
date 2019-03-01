---
title: REST, RESTful API Quick Reference
layout: post
comments: true
published: true
description: 
keywords: 
---

> A good API is not just easy to use but also hard to misuse.

## Ressources

* Version your API
  * Path: /v1/users
  * Subdomain: api.v1.example.com/users
  
* URI must be nouns, not verbs
  * /cars
  * /users
  * /books/{id}
* GET requests must never alter system/resource state (GET is readonly)
* All nouns are plurals
  * GET /users
  * DELETE /users/{id}
  * GET /users/{id}/reviews
  * POST /users/{id}/reviews
  * PUT /users/{id}/reviews/{rid}
* Map relations by sub-resources
  * GET /users/{id}/reviews
  * PUT /users/{id}/reviews/{rid}
* Allow for collections filtering, sorting and paging
  * GET /users?sort[]=-age&sort[]=+name
  * GET /users/{id}/reviews?published=1
  * GET /books?format[]=epub&format[]=mobi
  
## Security

* Allow only SSL (HTTPS) requests

## Authentication

* Use a standard and stateless authentication method:
  * API Key / Token (as part of the `Authorization` header)
  * Basic Authentication (Basic Auth)
* The OAuth protocol is not stateless, because it requires the user to pass credentials one time, and then maintain state of the user’s authorization on the server side, these are not considerations of the underlying HTTP protocol.
* If the authentication is unsuccessful, the status code `403` (Forbidden) is returned.

## HTTP Header

* Content-type: application/json

## HTTP Methods

HTTP Verb | HTTP Path | CRUD | [Method](https://github.com/watson-developer-cloud/api-guidelines/blob/master/swagger-coding-style.md#operationid) | Description | Response codes
--- | ---  | --- | --- | --- | ---
GET | /users | Index | getUsers or listUsers | Used for retrieving a list of resources. | 200 (OK), 404 (Not Found)
GET | /users/{id} | Read | getUserById | Used for retrieving a single resources. | 200 (OK), 404 (Not Found)
POST | /users | Create | createUser or addUser | Used for creating resources. | 201 (Created), 404 (Not Found), 409 (Conflict) if resource already exists.
PUT | /users/{id} | Update	| updateUser | Used for updating the resources. | 200 (OK), 204 (No Content), 404 (Not Found)
DELETE | /users/{id} | Delete | deleteUser | Used for deleting resources. | 200 (OK), 404 (Not Found)

## HTTP Status Codes

Status codes indicate the result of the HTTP request.

* 1XX - informational
* 2XX - success
* 3XX - redirection
* 4XX - client error
* 5XX - server error

Return meaningful status codes:

Code | Name| What does it mean?
--- | ---  | ---
200 | OK | All right!
201 | Created | If resource was created successfully.
400 | Bad Request | 4xx Client Error
401 |	Unauthorized | You are not logged in, e.g. using a valid access token
403 |	Forbidden	| You are authenticated but do not have access to what you are trying to do
404	| Not found	| The resource you are requesting does not exist
405	| Method not allowed | The request type is not allowed, e.g. /users is a resource and POST /users is a valid action but PUT /users is not.
409 | Conflict | If resource already exists.
422	| Unprocessable entity | Validation failed. The request and the format is valid, however the request was unable to process. For instance when sent data does not pass validation tests.
500	| Server error | 5xx Server Error. An error occured on the server which was not the consumer's fault.

## Error handling

### General server error

Send a `500 Internal Server Error` response.

**HTTP Statuscode:** 500

**Content:**

```json
{
  "error": {
    "message": "Application Error"
  }
}
```

**Content with error code:**

```json
{
  "error": {
    "code": 5001,
    "message": "Database connection failed"
  }
}
```

### General client error

Sending invalid JSON will result in a `400 Bad Request` response.

**HTTP Statuscode:** 400

**Content:**

```json
{
  "error": {
    "code": 400,
    "message": "Problems parsing JSON"
  }
}
```

### Validation errors

Sending invalid fields will result in a `422 Unprocessable Entity` response.

**HTTP Statuscode:** 422

**Content:**
```json
{
  "error": {
    "code": 422,
    "message": "Validation failed",
    "errors": [
      {
        "field": "email",
        "message": "Email is required"
      }
    ]
  }
}
```

## Sample calls

### jQuery

```javascript
// Get base url
var url = $('head base').attr('href')
```

A GET request:

```javascript
$.ajax({
    url: url + "users/1",
    type: "GET",
    cache: false,
    contentType: 'application/json',
    dataType: 'json'
}).done(function (data) {
    alert('Successfully');
    console.log(data);
}).fail(function (xhr) {
    var message = 'Server error';
    if (xhr.responseJSON && xhr.responseJSON.error.message) {
       message = xhr.responseJSON.error.message;
    }
    alert(message);
});
```

A POST request with payload:

```javascript
var data = {
    username: "max",
    email: "max@example.com"
};

$.ajax({
    url: url + "users",
    type: "POST",
    contentType: 'application/json',
    dataType: 'json',
    data: JSON.stringify(data)
}).done(function (data) {
    alert("Success");
    console.log(data);
}).fail(function (xhr) {
    if (xhr.status === 422) {
        // Show validation errors
        var response = xhr.responseJSON;
        alert(response.error.message);
        $(response.error.errors).each(function (i, error) {
           console.log("Error in field [" + error.field + "]: " + error.message);
        });
    } else {
        var message = 'Server error';
        if (xhr.responseJSON && xhr.responseJSON.error.message) {
           message = xhr.responseJSON.error.message;
        }
        alert(message);
    }
});
```

## Controllers

* Controller name: UserController

* **REST-API methods** (json request and response)
  * getUsers or findUsers
  * getUser
  * insertUser
  * updateUser
  * deleteUser
  
* **REST-API methods for sub-resources** (json request and response)
  * getUserReviews or findUserReviews
  * getUserReview
  * insertUserReview
  * updateUserReview
  * deleteUserReview
  
## Links

* <http://blog.restcase.com/7-rules-for-rest-api-uri-design/>
* <http://zalando.github.io/restful-api-guidelines/json-guidelines/JsonGuidelines.html>
