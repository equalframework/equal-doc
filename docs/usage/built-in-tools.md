# Built-in utilities and stand-alone scripts

Some tools are included in the `core` package to ease management and development of packages.


Utility scripts allow to achieve tasks that are not handled by the ORM.

These act like some sort of plugins, written in PHP and located in the folder: `packages/core/data` and `packages/core/actions`.

This page lists an inventory of available scripts, grouped by category.



!!! note "Calling the scripts"
    Calls shown in examples must be made at the root folder of eQual installation (where the `run.php` script is located).



## Installation & Config utilities

- **?do=test_db-connectivity** (Tests connectivity to the DBMS server.)
- **?do=test_db-access** (Tests access to the database.)

- **?do=test_fs-consistency** (Checks current installation directories integrity)

### Init a database
Creates a database using the details provided in config file (**CLI**).

```bash
$ ./equal.run --do=init_db
```



## Package utilities

### Test package consistency

Consistency checks between DB and class as well as syntax validation for classes (PHP), views and translation files (JSON).


```bash
$ ./equal.run --do=test_package-consistency --package=core
```

### Run package test cases

```bash
$ ./equal.run --do=test_package --package=core
```


### Init a Package
Creates the tables matching the classes of the package in the database (based on generated SQL schema).

```bash
$ ./equal.run --do=init_package --package=core
```



## Data manipulation utilities

### Models meta data

Inside the`"packages/core/data/model"`folder :

- **?get=model_schema** (Returns the schema of given class (model) in JSON)
- **?get=model_view** (Returns the JSON view related to an entity (class model), given a view ID (<type.name>))

### Manipulate data

Inside the`"packages/core/actions/model"`folder :

- **?do=model_create** (Create a new object using given fields values)

- **?do=model_update** (Update (fully or partially) the given object)

- **?do=model_delete** (Deletes the given object(s))

  

Inside the`"packages/core/data/model"`folder :

- **?get=model_search** (Returns a list of identifiers of a given entity, according to given domain (filter), start offset, limit and order)

- **?get=model_read** (Returns values map of the specified fields for object matching given class and identifier)

- **?get=model_collect** (Returns a list of entities according to given domain (filter), start offset, limit and order)

  

## Rights management utilities

Available rights: 

- create 

- read

- update

- delete

- manage

Inside the `eq.lib.php`file :

```php
<?php
/**
     * Users & Groups permissions masks (value attached to the mask) 
     NB Permission mask: The addition of numbers never leads to a similar result
     */
    define('QN_R_CREATE',    1);
    define('QN_R_READ',      2);
    define('QN_R_WRITE',     4);
    define('QN_R_DELETE',    8);
    define('QN_R_MANAGE',   16);
```

Example :

Those values are used inside `Permission.class.php`on the `rights`field.

```php
<?php
'rights' => [
                'type' 	            => 'integer',
                'onupdate'          => 'onupdateRights',
                'description'       => "Rights binary mask (1: CREATE, 2: READ, 4: WRITE, 8 DELETE, 16: MANAGE)"
            ],
```



## Users

Inside the `"packages/core/actions/user"` folder :

- **?do=user_grant** (Grant additional privilege to given user)

Only one right can be granted at a time to one user over one entity.

```bash
$ ./equal.run --do=user_grant --group=default --right=read --entity="core\Task"
```



- **?do=group_revoke** (Revoke privilege from a given user)

Only one right can be revoked at a time to one user over one entity.



### Groups

The controllers are similar

Inside the `"packages/core/actions/group"` folder :

- **?do=group_grant** (Grant additional privilege to given group)

Only one right can be granted at a time to one group over one entity.

```bash
$ ./equal.run --do=group_grant --group=default --right=read --entity="core\Task"
```



- **?do=group_revoke** (Revoke privilege from a given group)

Only one right can be revoked at a time to one group over one entity.



