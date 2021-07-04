# Installation



## Requirements

eQual requires the following dependencies:

- **PHP 7.1+** with following extensions : `mysqli` (mandatory) + `gd` `opcache` `zip` `tidy` (optional)
- **Apache 2+** or **Nginx**
- **MySQL 5+** compatible DBMS (up to MySQL 5.7 and MariaDB 10.3)



## Get eQual

- Download code as ZIP: 

  `wget https://github.com/cedricfrancoys/equal/archive/master.zip`

**OR**

- Clone with Git :

  `git clone https://github.com/cedricfrancoys/equal.git`



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

### Fedora/Centos 

Install Mysql server, PHP and Apache
```bash
yum update
yum install httpd php mysql-server php-mysql
```

Move the extracted directory to your document root

Not sure what you document root is using the below you can quickly find out
```bash
cat /etc/httpd/conf/httpd.conf | grep DocumentRoot
mv equal/* /var/www/html/
```

Now you may need to update the SELinux labels on the directories and files to httpd so that apache has the rights to serve the files. Using the below will do just this.
```bash
chcon -Rt httpd_sys_content_t /var/www/html/*
```
Now you may need to update SELinux boolean to allow httpd to network_connect_db to allow it to make a connection to mysql.
```bash
setsebool -P httpd_can_network_connect_db 1
```



## Virtual host configuration

Create a new virtual host and using `/public` as the root path

Example for Apache2:

```
<VirtualHost *:80>
  ServerName equal.local
  DocumentRoot "c:/dev/wamp64/www/equal/public"
  <Directory  "c:/dev/wamp64/www/equal/public/">
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

You can use the dedicated controller for creating the database.

Either using your browser : http://equal.local/?do=init_db

or with the command line interface:

```bash
./equal.run --do=init_db
```



Alternatively, you can create the database manually : 

* Open MySQL session as root – type the password upon request (if none set omit -p)
  	`mysql -uroot -p`
* Create eQual database
  	`create database equal character set utf8;`
* Create dedicated user for the database
  	`create user equal identified by 'password';`
* Grant the user full rights to the database
  	`grant all on equal.* to equal;`

  	