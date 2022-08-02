# Built-in utilities and stand-alone scripts

Some tools are included in the core package to ease management and development of new packages.


Utility scripts allow to achieve some tasks that are not handled by the ORM, for instance checking the consistency of a new package.

These act like some sort of plugins, written in PHP and located in the folder: `data/utils/`

This page lists an inventory of available scripts, grouped by category.



!!! note "Calling the scripts"
    Calls show in examples must be made at the root of eQual (where script *run.php* is located).

## Installation & Config utilities



## Package utilities

### Test package consistency

Consistency checks between DB and class as well as syntax validation for classes (PHP), views (HTML) and translation files (json)


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



## Rights management utilities



### Grant DB rights

Available rights: "create", "read", "update", "delete", "manage"

Only one right at a time can be granted to one group over one entity.

```bash
$ ./equal.run --do=group_grant --group=default --right=read --entity="core\User"
```







