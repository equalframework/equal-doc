![](assets/img/equal_logo_w_bg.png)

# eQual Framework

eQual is a versatile, language-agnostic, and web-oriented framework, aiming to elegantly manage interactions between front-end Apps and Business Logic involved in modern Web Applications.

**Natively Secure** - Benefit from native HTTP Auth support (JWT, CORS) to secure every endpoint with User Management, Role-Based Access Controls, or custom Access management.

**Server-Side Scripting** - Implement custom logic on any route endpoint to quickly build micro-services and  APIs of your own.

**Instant APIs Without Code** - Automatically generate a complete set of REST API routes with live documentation, for any kind of service.


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



> Personally what I value the most in eQual is its high level of re-usability and its way of defining controllers by announcing what they do and what they expect, which relieves me from documenting boredom and memory blackouts.

*Cédric Françoys*




## Sneak peek ?

### Using PHP controllers
`/public/packages/demo/data/simple.php`:

```php
<?php
/**
 * (near) Zero learning-curve development
 *
 */
echo 'This is a valid HTTP controller';
```

When needed, use existing components, or not. (using the ones you already know is encouraged!)
`/public/packages/demo/data/reuse.php`:

```php
<?php
/**
 * Code re-usability
 *
 */
echo run('get', 'demo_simple');
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
#!/bin/bash
./equal.run --get=demo_simple
```

> This is a valid controller!


## Real life action
```bash
#!/bin/bash
./equal.run --get=demo_image
```

`/packages/demo/data/image.php`:

```php
<?php
use equal\http\HttpRequest;

/**
 * HTTP native support
 *
 */
list($params, $providers) = eQual::announce([
    'description'   => 'Get picture data from imgur.com using imgur API.',
    'params'        => [
        'id' => [
            'description'   => 'Hash of the image to retrieve',
            'type'          => 'string',
            'default'       => 'a5NE1kW'
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

**5 methods** : Create, Read, Update, Delete, Search

and **a few native features** : 

* I/O as HTTP messages 
* Configuration Cascade 
* Dependency injection
* Services overload
* ORM & Collections support
* Data Adapter
* Authentication Management
* Access Controlling
* Unit testing
* Logging
* Debugging console



## More


This framework has been designed for those who:  

* don't want to always re-invent the wheel (but might occasionally enjoy it)
* dislike learning whole frameworks over and over again
* don't want to deal with dozens of files to achieve simple things




In most cases, you don't need everything a framework offers: You might be interested in a few features but in order to take advantage of it, you have to comply with the framework specific logic and structure.


eQual learning curve is actually more like learning stairs : one can use it without having to start from scratch or even without knowing it.



### Key features

* **client-server oriented**: HTTP native support, intended for REST API development and back-end processing.
* **code reusability**: easy integration of existing controllers results (controllers can be used as functions).
* **self-documented code**: available tools invite to be descriptive about data structure: how it is structured, what is expected, what errors can occur.
* **self-explanatory API** : each route comes with a description (announcement) of its dependencies and expected parameters, which eases greatly the communication between front-end and back-end developers.

