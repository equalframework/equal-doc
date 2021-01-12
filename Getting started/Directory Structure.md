# Directory structure

To understand the framework, here is what you need to know about its folders



## public

​	The entry point of every project, you'll find **index.php** aswell as the **packages** you will use
​	Each **package** is defined as follows :
​    ​    mypackage/
​    ​    ​    actions/		->	DO (database manipulations like create, delete, ...)
​    ​    ​	apps/			 ->	SHOW (app's html/css is there)
​    ​    ​    classes/*		->	
​    ​    ​    data/				->	GET (retrieving server's data)
​    ​    ​    i18n/				 ->	translation files
​    ​    ​    init/					->	
​    ​    ​    views/*	       	->	
​    ​    ​    config.inc.php	->	custom configuration specific to the package

​	\* mandatory component

## config

​	**default.inc.php**, to configure your database and other parameters
​	**config-example.inc.php**, rename it as config.inc.php to use custom configuration

## lib

​	Libraries and services used as external ressources

## run.php

​	Defines the DO, GET and SHOW behaviors for our queries, either by CLI, HTTP or PHP





## What next?

See *Quick Start*

