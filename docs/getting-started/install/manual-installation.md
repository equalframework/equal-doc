# Manual Installation

#### Requirements

eQual requires the following dependencies:

- **PHP 7.1+** with following extensions : `mysqli` (mandatory) + `gd` `opcache` `zip` `tidy` (optional)
- **Apache 2+** or **Nginx**
- **MySQL 5+** compatible DBMS (MySQL or MariaDB)



#### 1. Environment setup


##### Ubuntu

Installing the LAMP stack under Ubuntu is straightforward:

```bash
sudo apt update
sudo apt install apache2 mysql-server php libapache2-mod-php
```

Make sure that the mod-rewrite module is enabled: 
```bash
sudo a2enmod rewrite
```

Restart Apache: 
```bash
sudo systemctl restart apache2
```
OR
```bash
sudo service apache2 restart
```


Retrieve the path of the PHP binary:

```bash
which php
```
This will output something like `/usr/bin/php`.

Add the PHP binary to the PATH environment variable:
``` bash 
export PATH=$PATH:/usr/bin/php
```

##### RedHat / Fedora / Centos 

Install Mysql server, PHP and Apache:
```bash
yum update
yum install httpd php mysql-server php-mysql
```

##### Windows

Under Windows, you can use any of the following tools for a ready-to-use WAMP environment :

* [XAMPP 7.3](https://www.apachefriends.org/download.html)
* [WAMP Server 3.2+](https://www.wampserver.com/en/) 
* [DevServer 17.x](https://www.easyphp.org/easyphp-devserver.php)

Retrieve the path of the PHP executable:
```bash
where php
```
This will output something like `C:\wamp64\bin\php\php7.4.26\php.exe` 

Add the PHP binary to the PATH environment variable:
```bash
SET PATH=%PATH%;C:\wamp64\bin\php\php7.4.26
```



#### 2. Getting eQual

- Download code as ZIP: 

  `wget https://github.com/equalframework/equal/archive/master.zip`

**OR**

- Clone with Git :

  `git clone https://github.com/equalframework/equal.git`


Copy the files to your webserver HTML directory.

Example : 

```bash
cp equal /var/www/html/
```



#### 3. Virtual host configuration

Within the documentation pages, we refer to the installation that runs on a local web server using `equal.local`as servername  (accessible through http://equal.local).

If this is the first time you install eQual, we suggest you use that domain name to make things easier.

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

Remember to map the domain name with an IP address in your local hosts file:

* Linux : `/etc/hosts`
* Windows :  `C:\Windows\System32\drivers\etc\hosts`


Example:
```
127.0.0.1 equal.local
```

To make sure everything is setup properly, try to request the hello controller by browsing to http://equal.local/index.php?get=demo_hello.

You should get the simple output "hello universe". If not, review carefully the previous steps of the installation.



#### 4. File permissions
Make sure that the `/var/www/` directory and its content have `www-data:www-data` as owner:

All checks against mandatory folders and file permissions can be done using the following command : 

```bash
$ ./equal.run --do=test_fs-consistency
```

