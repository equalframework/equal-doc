# Installation



## Requirements

eQual requires the following dependencies:

- **PHP 7.1+** with following extensions : `mysqli` (mandatory) + `gd` `opcache` `zip` `tidy` (optional)
- **Apache 2+** or **Nginx**
- **MySQL 5+** compatible DBMS (up to MySQL 5.7 and MariaDB 10.3)



## Environment installation

### Windows

Under Windows, you can use any of the following tools for a ready-to-use MySQL and Apache services:

* [XAMPP 7.3](https://www.apachefriends.org/download.html)
* [WAMP Server 3.2+](https://www.wampserver.com/en/) 
* [DevServer 17.x](https://www.easyphp.org/easyphp-devserver.php)

Retrieve the path of the PHP executable:
```
where php
```
This will output something like `C:\wamp64\bin\php\php7.2.18\php.exe` 

Add the PHP binary to the PATH environment variable:
```
SET PATH=%PATH%;C:\wamp64\bin\php\php7.2.18
```

### Ubuntu

```bash
sudo apt update
sudo apt install php libapache2-mod-php
```

Make sure that the mod-rewrite module is enabled: 
```bash
sudo a2enmod rewrite
```

Restart Apache: 
```bash
sudo systemctl restart apache2
```
Retrieve the path of the PHP binary:
```bash
which php
```
This will output something like `/usr/bin/php` 

Add the PHP binary to the PATH environment variable:
```  
export PATH=$PATH:/usr/bin/php
```

### RHEL / Fedora / Centos 

Install Mysql server, PHP and Apache:
```bash
yum update
yum install httpd php mysql-server php-mysql
```



## Get eQual

- Download code as ZIP: 

  `wget https://github.com/cedricfrancoys/equal/archive/master.zip`

**OR**

- Clone with Git :

  `git clone https://github.com/cedricfrancoys/equal.git`



Copy the files to your webserver HTML directory.

Example : 

```
cp equal /var/www/html/
```



## Virtual host configuration

In the HTTP server config, create a virtual host that uses `/public` as DocumentRoot.

Example for Apache2:	

```
<VirtualHost *:80>
  ServerName equal.local
  DocumentRoot "c:/wamp64/www/equal/public"
  <Directory "c:/wamp64/www/equal/public/">
    Options +Indexes +Includes +FollowSymLinks +MultiViews
    AllowOverride All
    Require local
  </Directory>
</VirtualHost>
```

 

Of course, the related domain name must be set in your local hosts file:

* Windows :  `C:\Windows\System32\drivers\etc\hosts`
* Linux : `/etc/hosts`



You should now be able to query some operations using your browser:

http://equal.local/index.php?get=demo_hello



## eQual configuration

eQual expects at least one config file in the `/config` directory (if no `config.inc.php` file is found , then `default.inc.php` is used).

To create and customize your config file, start with copying `default.inc.php`

```
cp config/default.inc.php config/config.inc.php
```



Open `config.inc.php` and update the following constant to the values related to your environment:

```php
define('DB_DBMS',     'MYSQL'); 
define('DB_HOST',     '127.0.0.1'); 
define('DB_PORT',     '3306'); 
define('DB_USER',     'equaldb'); 	   	// adapt this
define('DB_PASSWORD', 'mypass');  		// this (with your own password)
define('DB_NAME',     'mydb');   		// and this
define('DB_CHARSET',  'UTF8'); 
```




## Database initialization

You should now have a properly configured environment.

To make sure the DBMS can be access, you can use the following controller : 
```
./equal.run --do=test_db-connectivity
```
Upon success this controller exits with no message (exit 0), and the database is created. If an error occurs, a JSON message is returned with a short description about the issue. Example:
```
{
    "errors": {
        "INVALID_CONFIG": "Unable to establish connection to DBMS host (wrong hostname or port)"
    }
}
```


The database can be created by using the `core_init_db controller` 

Either using your browser : [http://equal.local/?do=init_db](http://equal.local/?do=init_db)

or with the command line interface:

```bash
./equal.run --do=init_db
```



>  Alternatively, you can create the database manually : 
>
> 
> * Open MySQL session as root – type the password upon request (if none set omit -p)
  	`mysql -uroot -p`
> * Create eQual database
  	`create database mydb character set utf8;`

  	

## Package  initialization

In order to be able to manipulate entities, the related package needs to be initialized (each package contains the class definition of its own entities).
This can be done by using the `core_init_package` controller.

```
./equal.run --do=init_package --package=core
```
This controller should populate the database with the tables related to the specified package.

Now, you should be able to fetch data by using the default (core) controllers.
For instance : 
http://equal.local/?get=model_collect&entity=core\User



## API requests

A list of routes related to default API is defined in `/config/routing/api_default.json`
Here below are some examples of HTTP calls and their responses (in JSON):



Fetch the details of user[1] (admin).

GET http://equal.local/user/1
```
[
    {
        "id": 1,
        "name": "admin",
        "state": "instance",
        "modified": "2012-08-18T15:06:43+02:00"
    }
]
```



Fetch the full list of existing groups.

GET http://equal.local/users

```
[
    {
        "id": 1,
        "name": "admin",
        "state": "instance",
        "modified": "2012-08-18T15:06:43+02:00"
    },
    {
        "id": 2,
        "name": "cedric@equal.run",
        "state": "instance",
        "modified": "2014-09-04T12:21:34+02:00"
    }
]
```



Fetch the full list of existing groups.

GET http://equal.local/groups

```
[
    {
        "id": 1,
        "name": "root",
        "state": "",
        "modified": "2012-05-30T20:45:20+02:00"
    },
    {
        "id": 2,
        "name": "default",
        "state": "",
        "modified": "2012-05-30T20:45:20+02:00"
    }
]
```



Create a new group.

POST http://equal.local/group

```
{
    "entity": "core\\Group",
    "id": 3
}
```



Update the 'name' property of the group[3].

PUT http://equal.local/group/3?fields[name]=test

```
[]
```



Fetch the full list of existing groups.

GET http://equal.local/groups

```
[
    {
        "id": 1,
        "name": "root",
        "state": "",
        "modified": "2012-05-30T20:45:20+02:00"
    },
    {
        "id": 2,
        "name": "default",
        "state": "instance",
        "modified": "2021-07-05T12:40:49+02:00"
    },
    {
        "id": 3,
        "name": "test",
        "state": "instance",
        "modified": "2021-07-05T12:42:30+02:00"
    }
]
```