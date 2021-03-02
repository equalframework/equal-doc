# eQual Framework

eQual is a versatile, language-agnostic, and web-oriented framework, aiming to elegantly manage interactions between front-end Apps and Business Logic involved in modern Web Applications.



**Rock Solid Security**
	Secure every API endpoint with User Management, Role-Based Access Controls, SSO Authentication, JWT, CORS, and OAuth.

**Server-Side Scripting**
	Implement custom logic on the request or response of any API endpoint or quickly build your own custom APIs with JavaScript V8, Node.js, or PHP.

**Instant APIs Without Code**
	Automatically generate a complete set of REST APIs with live documentation for any SQL or NoSQL database, file storage system, or external service.



```php
<?php
// Tired of steep learning curves?
echo "This is a valid Contoller!"
```



## Why eQual

A lot of framework require a long time to master and, while learning, you find yourself struggling to achieve event the most simple things.



eQual has been designed to oppose as little constraints as possible to developers. While being a fully featured framework that has all you need to do almost anything, taking advantage of all the native features, only some of them or even none is entirely up to you.



Since you're reading this, you already know everything to start using it.

So, use it your way.



> Personally what I value the most in eQual is its high level of re-usability and its way of defining controllers by announcing what they do and what they expect, which relieves me from documenting boredom.

*Cédric Françoys*




## Sneak peek ?

### Using PHP controllers
`/public/packages/demo/data/simple.php`:

```php
<?php
/**
 * Zero learning-curve development
 *
 */
echo 'This is a valid HTTP controller';
```

`/public/packages/demo/data/reuse.php`:
```php
<?php
use config\QNLib;
/**
 * Code re-usability
 *
 */
echo eQual::run('get', 'demo_simple');
```

### Using HTTP request
`wget http://localhost/index.php?get=demo_simple`

> Response:
> Header excerpt:
> Status Code: 200 OK
> Content-Length: 26
> Content-Type: text/html; charset=UTF-8
> Body:
> This is a valid controller!

### Using CLI
```bash
$> php run.php --get=demo_simple
This is a valid controller!
```


When needed use existing components, or not. (using the ones you already know is encouraged!)
`/public/packages/demo/data/reuse.php`:

```php
<?php
use qinoa\http\HttpRequest;

/**
 * HTTP native support
 *
 */
list($params, $providers) = eQual::announce([
    'description'   => 'Get picture data from imgur API',
    'params'        => [
        'id' => [
            'description'   => 'Hash of the image to retrieve',
            'type'          => 'string',
            'required'      => true
        ]
    ],
    'response'      => [
        'content-type'  => 'application/json',
        'charset'       => 'utf-8'
    ],
    'providers'     => ['context']
]);

$request = new httpRequest("GET https://api.imgur.com/3/image/{$params['id']}");

$response = $request
            ->header('Authorization', "Client-ID 34030ab1f5ef12d")
            ->send();
            
$providers['context']
    ->httpResponse()
    ->body(['result' => $response->body()])
    ->send();
```



## In short

eQual uses only **3** kinds of **operations** 

1.  **'do'** something (Action handlers)
2.  **'get'** something (Data providers)
3.  **'show'** something (App providers)

**5 methods**

1. Create 
2. Read
3. Update
4. Delete
5. Search

and **a few native features** : 

* I/O as HTTP messages 
* Configuration Cascade 
* Dependency injection
* Services overload
* ORM & Collections support
* Data Adapter
* Authentication Management
* Access Control
* Unit testing
* Logging
* Debugging console





This framework is made for those who:
* don't want to always re-invent the wheel (but might occasionally enjoy it)
* dislike learning whole frameworks over and over again
* don't want to deal with dozens of files to achieve simple things


In most cases, you don't need everything a framework offers.

You might be interested in a few features but in order to take advantage of it, you have to comply with the framework specific logic and structure.


A learning curve that is actually more like learning stairs without having to start from scratch (or, said otherwise, one can use it without knowing it!)





* **client-server oriented**: intended for REST API development and back-end processing
	HTTP oriented
* **code reusability**: easy integration of existing controllers results (use controllers as functions)
* **code self-documented**: available tools invite to be descriptive about data structure (how it is structured, what is expected, what error
* **self-explanatory API**









