# Configuration



## Database setup example

##### WAMP server
​	Open PHPMyAdmin as root user,
​	Create a database named **equal**,
​	Privileges > create a new user named **equaldba** and let "grant all access" checked

##### equal-framework/config
​	Open *default.inc.php*\* and update the following values :
```php
define('DB_DBMS', 'MYSQL'); 
define('DB_HOST', '127.0.0.1'); 
define('DB_PORT', '3306'); 
define('DB_USER', 'equaldb'); 	   	// adapt this
define('DB_PASSWORD', '123');  		// this (with your own password)
define('DB_NAME', 'equal');   		// and this
define('DB_CHARSET', 'UTF8'); 
```

\* If you want to properly override the default file, open *config-example.inc.php* and follow the instructions
To avoid requirement issues it's recommend to copy paste *default.inc.php* into *config.inc.php*, and edit from there



## What next?

See *Directory structure*

or *Quick Start*