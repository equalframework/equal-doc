# Dependency injection

Located in the folder: `data/utils/` ->

```php
<?php
list($params, $providers) = announce([
    'description'	=>	"",
    'response'      => [
        'content-type'  => 'application/json',
        'charset'       => 'utf-8',
        'accept-origin' => '*'
    ],    
    'params' 		=>	[
    	[...]
    ],    
    'providers'     => ['context', 'orm']   // list of services to inject    
]);
```

  



For instance, here is how the Access controller and Authentication manager can be overridden:

```php
  'providers' => [
	'auth'   => custom\AuthenticationManager,
	'access'  => custom\AccessController
  ]
```



Access control is handled inside the `equal\orm\Collection` class