# Generic cheat-sheet

This section aims to answer common questions through easy to understand examples

```[eQ]``` is alway refering to your absolute root path to eQual



## eQual functionalities

### Console

```http
[eQ]/console.php
```

### Workbench

```http
[eQ]?show=workbench
```



## CLI commands

Should be located at the root of eQual (where *run.php* is)

#### Grant DB rights

Avalaible rights: "create", "read", "update", "delete", "manage"

You can grant only one right for one entity at a time

```bash
php run.php --do=group_grant --group=2 --right=read --entity=mypackage\MyObject
```

#### Test eQual consistency

```bash
php run.php --do=test_package-consistency
```

#### Test your package consistency

```bash
php run.php --do=test_package-consistency --package=mypackage
```

#### Initiate eQual's core in DB

(this step is mandatory for every new database)

```bash
php run.php --do=init_package --package=core
```

#### Initiate your package in DB

```bash
php run.php --do=init_package --package=mypackage
```

#### Run package testing

```bash
php run.php --do=test_package --package=mypackage
```



## REST API

### GET :

Path example :  *../packages/mypackage/data/myobject.php*

**HTTP :**

```http
[eQ]?get=mypackage_myobject
```

**PHP :**

```php
run('get', 'mypackage_myobject')
```

**CLI :**

```bash
php run.php --get=model_collection --entity=mypackage\MyObject
```

### DO :

Path example :  *../packages/mypackage/actions/myobject/action.php*

**HTTP :**

```http
[eQ]?do=mypackage_myobject_action
```

**PHP :**

```php
run('do', 'mypackage_myobject_action', [/* parameters */])
```

**CLI :**

- Create = model_create
- Update = model_update
- Delete = model_delete

```bash
php run.php --do=model_update --entity=mypackage\MyObject --fields=['id'=>1,'name'=>'example']
```

