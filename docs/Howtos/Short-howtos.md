# Short howtos

This section aims to answer common questions through easy to understand examples

note: ```[eQ]``` is alway refering to your absolute root path to eQual



## eQual functionalities

### Show console

```http
[eQ]/console.php
```

### Show workbench

```http
[eQ]?show=qinoa_workbench
```



## GET

Refers to :  *../packages/mypackage/data/myobject.php*

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
php run.php --get=model_collection --entity=mypackage\Myobject
```



## DO

Refers to :  *../packages/mypackage/actions/myobject/action.php*

**HTTP :**

```http
[eQ]?do=mypackage_myobject_action
```

**PHP :**

```php
run('do', 'mypackage_myobject_action', [/* parameters */])
```

**CLI :**

Specify and replace model_ACTION with the desired action :

- Create = model_create
- Update = model_update
- Delete = model_delete

```bash
php run.php --do=model_ACTION --entity=mypackage\Myobject --fields=['fieldname'=>'fieldvalue']
```



## CLI commands

Should be located at the root of eQual (where *run.php* is)

#### Test eQual consistency

```bash
php run.php --do=public:qinoa_test_package-consistency
```

#### Test your package consistency

```bash
php run.php --do=public:qinoa_test_package-consistency --package=mypackage
```

#### Initiate eQual's core in DB

(this step is mandatory for every new database)

```bash
php run.php --do=public:qinoa_init_package --package=core
```

**Alternative :**

```bash
php run.php --do=init_package --package=core
```

#### Initiate your package in DB

```bash
php run.php --do=public:qinoa_init_package --package=mypackage
```

#### Run package testing

```bash
php run.php --do=test_package --package=mypackage
```
