# Built-in utilities and stand-alone scripts

Some tools are included in the `core` package to ease management and development of packages.


Utility scripts allow to achieve tasks that are not handled by the ORM.

These act like some sort of plugins, written in PHP and located in the folder: `packages/core/data` and `packages/core/actions`.

This page lists an inventory of available scripts, grouped by category.



!!! note "Calling the scripts"
    Calls shown in examples must be made at the root folder of eQual installation (where the `run.php` script is located).



## Installation & Config utilities


#### `db-connectivity`

| **PATH**        | `core\actions\test\db-connectivity.php`                      |
| --------------- | ------------------------------------------------------------ |
| **URL**         | `?do=test_db-connectivity`                                   |
| **CLI**         | `$ ./equal.run --do=test_db-connectivity`                    |
| **DESCRIPTION** | Tests connectivity to the DBMS server (check if we're able to establish a TCP/IP connection). |



#### `db-access`

| **PATH**        | `core\actions\test\db-access.php`                            |
| --------------- | ------------------------------------------------------------ |
| **URL**         | `?do=init_db`                                                |
| **CLI**         | `$ ./equal.run --do=init_db`                                 |
| **DESCRIPTION** | Tests access to the database specified in the config file. This controller uses db-connectivity before trying to access the database. |



#### `fs-consistency`

| PATH            | `core\actions\test\fs-consistency.php`                       |
| --------------- | ------------------------------------------------------------ |
| **URL**         | `?do=test_fs-consistency`                                    |
| **CLI**         | `$ ./equal.run --do=test_fs-consistency`                     |
| **DESCRIPTION** | Checks current installation directories integrity. The controller checks if all mandatory directories are present, and if their permissions allow the apache process to read/write as required. |



#### `init_db`
|**PATH**|`core\actions\init\db.php`|
|-|-|
|**URL**|`?do=init_db`|
|**CLI**|`$ ./equal.run --do=init_db`|
|**DESCRIPTION**|Creates a database using the details provided in config file. This controllers calls db-connectivity and if connection can be established with the host, it requests the creation of the database, if it does not exist yet.|



## Package utilities

### Test package consistency

Consistency checks between DB and class as well as syntax validation for classes (PHP), views and translation files (JSON).


```bash
$ ./equal.run --do=test_package-consistency --package=core --level=warn
```

> The level property has 3 options : 
>
> - **'error'** (ex: `missing property 'entity' in file:  "packages\/lodging\/views\/sale\booking\InvoiceLine.form.default.json"`)
> - **'warn'** (ex: `WARN  - I18 - Unknown field 'object_class' referenced in file "packages\/core\/i18n\/en\/alert\MessageModel.json"`
> - ***** (error & warn).

### Run package test cases

The controller checks the presence of test units for a given package and runs them, if any. (page :['Testing'](./testing.md))

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

Available rights are : create, read, update, delete, manage

<<<<<<< HEAD
- create 

- read

- update

- delete

- manage

They are also defined inside the `eq.lib.php`file with a value attached :

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

**Use** :

Those values are used, for example, inside `Permission.class.php`on the `rights`field.

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



