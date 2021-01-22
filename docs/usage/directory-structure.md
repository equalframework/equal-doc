# Directory structure

To understand the framework, here is what you need to know about its folders

note: This page is currently non-exhaustive !



## Root directory structure

| File|  Description |
|-|-|
| .htaccess	        | Apache configuration file  used to prevent directory listing and handling url rewriting |
| qn.lib.php	| Boostrap library. Defines constants and global utilities |
| index.php	        | This script is also referred to as the dispatcher : its task is to include required libraries and to set the context. This is the main entry point.|
| run.php	| Server script for client-server mode|
| lib	        | Folder containing PHP libs and classes |
| public   | Folder containing installed packages (classes definition, translations, views, actions, …)|
| private   | Folder containing installed packages (classes definition, translations, views, actions, …)|
| bin   | Folder containing values of binary fields see BINARY_STORAGE_DIR |
| spool   | Dedicated to email sending |



The root folder is also the place where to place a `composer.json` file and the subsequent `/vendor` directory.

## public



| File        | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| .htaccess   | Apache configuration file  used to prevent directory listing and handling url rewriting |
| qn.lib.php  | Boostrap library. Defines constants and global utilities     |
| index.php   | This script is also referred to as the dispatcher : its task is to include required libraries and to set the context. This is the main entry point. |
| console.php | This is the only alternate entry point.                      |



​	The entry point of every project, you'll find **index.php** as well as the **packages** you will use



## config

​	**default.inc.php**, to configure your database and other parameters

​	**config-example.inc.php**, rename it as `config.inc.php` to use custom configuration

## lib

​	Libraries and services used as external resources

## run.php

​	Defines the DO, GET and SHOW behaviours for our queries, either by CLI, HTTP or PHP





## Package definition


An application is divided in several parts, stored in a package folder located under the /packages directory.
Each package might contain the following folders (underlined folders are mandatory).

| Folder| Role | URI key   |  Examples           |
|-|-|-|-|
| **__classes__**    | model          |                  | `core\User.class.php`, `core\Group.class.php`, `core\Permission.class.php` |
| **__apps__**       | user interface (view) | show       | core_manage, core_utils |
| **actions**    | action handler (controller) | do       | core_manage, core_utils |
| **data**    | data provider | get       | core_objects_browse, core_user_lang |
| **__views__**    | templates |        |  |
| **i18n**    | translations |        |  |
| **html**    | static html |        | static content, javascripts, stylesheets, images |



​	Each **package** is defined as follows :

```
mypackage/
	actions/		->	DO (post, put/patch, delete)
	apps/			->	package's front-end
	classes/		->	class & object definition
	data/			->	GET
	i18n/			->	translations
	init/
	views/
	config.inc.php	->	additional configuration
```

