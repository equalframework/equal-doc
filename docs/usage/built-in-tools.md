# Built-in utilities and stand-alone scripts

Some tools are included in the `core` package to ease management and development of packages.


Utility scripts allow to achieve tasks that are not handled by the ORM.

These act like some sort of plugins, written in PHP and located in the folder: `packages/core/data` and `packages/core/actions`

This page lists an inventory of available scripts, grouped by category.



!!! note "Calling the scripts"
    Calls shown in examples must be made at the root folder of eQual installation (where the `run.php` script is located).

## Installation & Config utilities


test_db-connectivity
test_db-access

test_fs-consistency

### Init a database
Creates a database using the details provided in config file..

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
model_schema
model_view


### Manipulate data

?do=model_create
?do=model_update
?do=model_delete

?get=model_search
?get=model_read
?get=model_collect

## Rights management utilities

Available rights: "create", "read", "update", "delete", "manage"

### Groups


#### group_add-user

group_grant



Only one right can be granted at a time to one group over one entity.

```bash
$ ./equal.run --do=group_grant --group=default --right=read --entity="core\User"
```

group_revoke

