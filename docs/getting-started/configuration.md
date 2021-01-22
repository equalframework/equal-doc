# Configuration



## Database setup example

##### WAMP server

​	Open PHPMyAdmin as root user,

​	Create a database named **equal**,

​	Privileges > create a new user named **equaldba** and let "grant all access" checked



##### equal-framework/config

​	Open *default.inc.php* and update the following values :

```php
define('DB_DBMS', 'MYSQL'); 
define('DB_HOST', '127.0.0.1'); 
define('DB_PORT', '3306'); 
define('DB_USER', 'equaldb'); 	   	// adapt this
define('DB_PASSWORD', '123');  		// this (with your own password)
define('DB_NAME', 'equal');   		// and this
define('DB_CHARSET', 'UTF8'); 
```

Please note that directly editing *default.inc.php* is fine if you only need to configurate your database access, but overall it's bad practice

What you want to do instead is create a *config.inc.php* file, and copy-paste the content of *default.inc.php*. From there you can safely apply custom changes



### What next?

See [*Quick Start*](quick-start.md)