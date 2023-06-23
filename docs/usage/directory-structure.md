###### 



# Directory structure

To understand the framework, here is what you need to know about its folders



## Root directory structure

```
/
├── bin
├── lib
├── packages
├── public
│   ├── .htaccess
│   └── index.php
├── spool
├── eq.lib.php
└── run.php
```



| **FILE** | **DESCRIPTION** |
|-|-|
| .htaccess	        | Apache configuration file  used to prevent directory listing and handling url rewriting |
| eq.lib.php	| Boostrap library. Defines constants and global utilities |
| index.php	        | This script is also referred to as the dispatcher : its task is to include required libraries and to set the context. This is the main entry point.|
| run.php	| Server script for client-server mode|
| lib	        | Folder containing eQual library  (mostly classes and services definitions) |
| packages   | Folder containing installed packages (classes definition, translations, views, actions, …)|
| public   | Root public folder for the web server |
| bin   | Folder containing values of binary fields see BINARY_STORAGE_DIR |
| spool   | Dedicated to email sending |



The root folder is also the place where to place a `composer.json` file and the subsequent `/vendor` directory.

## public

| **FILE**    | **DESCRIPTION**                                              |
| ----------- | ------------------------------------------------------------ |
| .htaccess   | Apache configuration file  used to prevent directory listing and handling url rewriting |
| index.php   | This script is also referred to as the dispatcher : its task is to include required libraries and to set the context. This is the main entry point. |
| console.php | This is the only alternate entry point.                      |
| assets      | static content, javascripts, stylesheets, images, config, translation |
| assets/env  | eQual Configuration file.                                    |
| assets/i18n | Translation files.                                           |



The entry point of every project, you'll find **index.php** as well as the **packages** you will use



## config

​	**`default.inc.php`**, to configure your database and other parameters

​	**`config-example.inc.php`**, rename it as `config.inc.php` to use custom configuration

## lib

​	Libraries and services used as external resources

## run.php

​	Defines the DO, GET and SHOW behaviors for our queries, either by CLI, HTTP or PHP



## Packages

An application is divided in several parts, stored in a package folder located under the `/packages` directory.
Each package might contain the following folders (underlined folders are mandatory). 

The package_name must be in **lowercase**.

Each **package** is defined as follows :

```
package_name
├── classes
│   └── */*.class.php
├── actions
│   └── */*.php
├── apps
│   └── */*.php
├── data
│   └── */*.php
├── tests
│   └── */*.php
├── init
│   └── *.json
├── views
│   ├── 
│   └── *.json
├── i18n
│   └── *
│       └── *.json
├── manifest.json         
├── config.inc.php
└── readme.md
```



| **FOLDER** | **ROLE** | **URI KEY** | **EXAMPLES**                                                                       |
|-|-|-|------------------------------------------------------------------------------------|
| classes    | model          |                  | `core\User.class.php`, `core\Group.class.php`, `core\Permission.class.php` |
| actions    | action handler (controller) | do       | core_manage, core_utils                                                            |
| apps       | applications related to the package |        | auth, apps                                                                         |
| data    | data provider | get       | core_objects_browse, core_user_lang                                                |
| test    | test units | do | `default.php`                                                                      |
| init    | initialize the package with data (**requires**:`import=true`) or routes | do | `core_Group.json`                                                                  |
| views    | templates |        | `User.form.default.json`, `User.list.default.json`                                 |
| i18n    | translations |        | `User.json`                                                                        |
| assets | static html |        | static content, javascripts, stylesheets, images                                   |



##### manifest.json

The manifest is a file containing informations about the package and its apps:

```
├── name*						-> PACKAGE  
├── depends_on   
├── apps						-> APPS
	└── ├── name**
        ├── description
        ├── url
        ├── icon
        ├── color
        ├── access
        	└── ├── groups
```

**Package**

| **PROPERTY** | **ROLE** |  **EXAMPLES**  |
|-|-|---|
| name* | name of the package | `"core", "finance"` |
| depends_on | packages that need to be instanciated preventively | `["core", "finance"]` |
| apps       | applications related to the package | `auth, apps` |

**Apps**

| **PROPERTY** | **ROLE** |  **EXAMPLES**  |
|-|-|---|
| name** | name of the application | `APPS_APP_SETTINGS` |
| description | description of the application |  |
| url    | url of the application | `/auth` |
| icon (optional) | material icons representing the application | `settings` |
| color (optional) | color attributed to the application | `#FF9741` |
| access/groups | groups giving access to the application | `setting.default.user` |
