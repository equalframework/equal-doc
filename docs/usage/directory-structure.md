# Directory structure

To understand the framework, here is what you need to know about its folders

note: This page is currently non-exhaustive !



## public

​	The entry point of every project, you'll find **index.php** aswell as the **packages** you will use

​	Each **package** is defined as follows :

```
mypackage/
	actions/		->	DO (post, put/patch, delete)
	apps/			->	package's front-end
	classes/*		->	class & object definition
	data/			->	GET
	i18n/			->	translations
	init/
	views/*
	config.inc.php	->	additional configuration
```

​	\* mandatory component

## config

​	**default.inc.php**, to configure your database and other parameters

​	**config-example.inc.php**, rename it as config.inc.php to use custom configuration

## lib

​	Libraries and services used as external ressources

## run.php

​	Defines the DO, GET and SHOW behaviors for our queries, either by CLI, HTTP or PHP
