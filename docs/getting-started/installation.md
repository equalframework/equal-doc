# Installation



## Requirements

eQual requires the following dependencies:

- **PHP 7.1+** with following extensions : `mysqli` (mandatory) + `gd` `opcache` `zip` `tidy` (optional)
- **Apache 2+** or **Nginx**
- **MySQL 5+** compatible DBMS (up to MySQL 5.7 and MariaDB 10.3)





## Get eQual

- Download code as ZIP: 

  `wget https://github.com/cedricfrancoys/equal-framework/archive/master.zip`

**OR**

- Clone with Git :

  `git clone https://github.com/cedricfrancoys/equal-framework.git`
  
  



## Install 

### Windows

Under Windows, you can use any of the following tools for a ready-to-use MySQL and Apache services:

* [XAMPP 7.3](https://www.apachefriends.org/download.html)
* [WAMP Server 3.2+](https://www.wampserver.com/en/) 
* [DevServer 17.x](https://www.easyphp.org/easyphp-devserver.php)



### Ubuntu

### Fedora/Centos 

Open terminal and ensure that your operating system is updated with the latest patches
	`yum update`
Now install mysql server, php and apache
	`yum install httpd php mysql-server php-mysql`


Move the extracted directory to your document root

Not sure what you document root is using the below you can quickly find out
	`cat /etc/httpd/conf/httpd.conf | grep DocumentRoot`
	`mv equal-framework*/* /var/www/html/`

Now you may need to update the SELinux labels on the directories and files to httpd so that apache has the rights to serve the files. Using the below will do just this.
	`chcon -Rt httpd_sys_content_t /var/www/html/*`
Now you may need to update SELinux boolean to allow httpd to network_connect_db to allow it to make a connection to mysql.
	`setsebool -P httpd_can_network_connect_db 1`


## Host config


- Create a new virtual host using *.../equal-framework/public/* as the root path

If done correctly you should now see the eQual workench (where you can see and manipulate your package classes)



## Database Init

The database configuration is only for demo and more stringent passwords and setup for production uses.

* Open MySQL session as root – type the password upon request (if none set omit -p)
  	`mysql -uroot -p`
* Create eQual database
  	`create database equal character set utf8;`
* Create dedicated user for the database
  	`create user equal identified by 'password';`
* Grant the user full rights to the database
  	`grant all on equal.* to equal;`
  	
  	