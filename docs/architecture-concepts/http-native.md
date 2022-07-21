# HTTP native

eQual complies with HTTP standards : it natively analyzes HTTP messages to route client requests to the appropriate controller and outputs resulting data as an HTTP response.

It can be seamlessly used in both CLI or ReSTful API context.



![](https://files.yesbabylon.org/document/436b78f104ffd4c0146a642967d6c3ef)



## Controllers

Any invocation made to an eQual-driven server is handled as an HTTP request (even within a CLI context) and relayed to the appropriate controller which, in turn, produces an HTTP response.

Controllers are nothing more than regular PHP scripts.

Here is an example of a minimalist controller:

```
<?php
echo "Hello World";
```
When invoked, this controller produces the following HTTP response :
```
HTTP/1.1 200 OK
Server: Apache/2.2.14
Content-Type: text/html
Connection: Closed
Content-Length: 11

Hello World
```



## Invoking controllers

Controllers are considered as **operations** that run specific sets of instructions based on parameters provided in the HTTP request.

For convenience, controllers can also be invoked in a CLI context, or directly within a PHP script.

**Examples :** 

HTTP request

```
GET http://equal.local/?get=demo_simple
```

CLI command

```bash
$ ./equal.run --get=demo_simple
```

PHP script

```php
<?php
echo eQual::run('get', 'demo_simple');
```



A controller invocation consists of two parts: 

* The first part tells what kind of operation must be performed:
    * `key` must be one of the following : 
        * **GET** some data (`/?get=...`)
        * **DO** something (`/?do=...`)
        * **SHOW** some formatted content (`/?show=...`)
    * `value` specifies the name of the **package** to be invoked (must be the name of a subfolder of the `packages` directory), as well as the **script** to be called(a package may have several scripts - stored in the subfolders `action`, `data`, or `apps`)
* The second part is either the body or a series of parameters



Here is an example of a controller invocation :  
``` 
http://equal.local/?get=model_collect&entity=core\Group
```

In this example, the main entry point (`index.php`) will try to execute the following script : `packages/core/data/model/collect.php` 



If no controller matches the received request, a response with a 404 status is returned.

Example:

http://equal.local/?do=foo
```
{
    "errors": {
        "UNKNOWN_OBJECT": "Unknown ACTION_HANDLER (do) public:foo"
    }
}
```



## Default controller

The main config file (`config/config.inc.php`) allows to define a default package, and a default App can be defined for every package.  


```php
<?php
define('DEFAULT_PACKAGE', 'core');
```

In turn, each package can define a default App in its own config file (i.e. `packages/equal/config.inc.php`)
```php
<?php
namespace config;
define('DEFAULT_APP', 'workbench');
```

This allows to make the root entry point  redirect to a webapp (in this example:  `http://equal.local/index.php?show=core_workbench'`)

