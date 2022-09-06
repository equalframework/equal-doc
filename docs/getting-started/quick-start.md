# Quick Start

You've just installed eQual. And now what ?

## Init your environment

First, let's configure it according to your environment.

A default configuration file is already present and can be modified, but it is a good practice to leave it untouched, especially if you're using git or any other versioning system, so that the configuration details of your environment remain confidential (not part of the repository).

```bash
$ cp config/default.inc.php config/config.inc.php
```
Then edit `config.inc.php` and update the values of the DBMS access. 

Constants role is unambiguous : 

```php
<?php 
define('DB_HOST',       '127.0.0.1');
define('DB_PORT',       '3306');   
define('DB_USER',       'root');
define('DB_PASSWORD',   'test');
define('DB_NAME',       'equal');
```
!!! note "About DB creation"
    * You can choose any name for the database: if it does not exist yet, you'll be able to create it using the command line;

######         * In case creation fails, make sure the DBMS server is actually running on the specified host and port.

You can now test your installation by calling the `test_db-connectivity` test tool :

| **PATH**        | `core\actions\test\db-connectivity.php`                      |
| --------------- | ------------------------------------------------------------ |
| **URL**         | `?do=test_db-connectivity`                                   |
| **CLI**         | `$ ./equal.run --do=test_db-connectivity`                    |
| **DESCRIPTION** | Tests connectivity to the DBMS server (check if we're able to establish a TCP/IP connection). |

If no error message is returned (the command ends with a `0` exit code), you can create your database by calling the `init_db` tool :

|**PATH**|`core\actions\init\db.php`|
|-|-|
|**URL**|`?do=init_db`|
|**CLI**|`$ ./equal.run --do=init_db`|
|**DESCRIPTION**|Creates a database using the details provided in config file. This controllers calls db-connectivity and if connection can be established with the host, it requests the creation of the database, if it does not exist yet.|

eQual holds a native `core` package that holds a few classes and operations. All packages depend on the ORM layer, which is responsible of storing the objects into the database. So, in order to start using a package that defines object classes, you have to initialize it. 

This can be done using the `init_package` tool :

|**PATH**|`core\actions\init\package.php`|
| --------------- | ------------------------------------------------------------ |
|**URL**|`?do=init_package&package=core`|
|**CLI**|`$ ./equal.run --do=init_package --package=core`|
|**DESCRIPTION**|Initialise database for given package. If no package is given, initialize core package.|




## Create your first package

This section covers the first steps to setup a backend service or API using eQual.

### 1. Create a new package

In `/packages`, create a **new folder**. For this example, we'll name it *"mypackage"*.
In `/packages/mypackage`, create 2 new folders named `classes` and `data`.

Directory should look like this :  

```
/packages
    /mypackage
        /classes
        /data
```


### 2. Define some custom classes

In `/packages/mypackage/classes/`, add a new **.class.php** file for each class we want to use. 
In this example, we define the class **Task** for a todo-list app :

`/packages/mypackage/classes/Task.class.php`

```php
<?php  
namespace mypackage;
use equal\orm\Model; // this is a built-in object handler
    
class Task extends Model {
    public static function getColumns() {
        return [
            'title'     => ['type' => 'string'],
            'content'   => ['type' => 'text']
       		];
    }
}
```

*Note: ID key generation is handled by eQual*

But, what if we want to establish a relation between two classes, like nesting the **User** of `User.class.php` as a parameter?

That's where the types **many2many**, **one2many**, and **many2one** come in handy (*see [Understanding DBMS relationships](https://afteracademy.com/blog/what-are-the-different-types-of-relationships-in-dbms)*).

```php
<?php
// Task
// ...
      return [
        // ...
        'user_id'	=> ['type' => 'many2one', 'foreign_object' => 'mypackage\User']
      ];
```

We also need to do the opposite in `User.class.php` :

```php
<?php
// User
// ...
      return [
        // ...
        'task_id'	=> ['type' => 'one2many', 'foreign_object' => 'mypackage\Task']
      ];
```



### 3. Initialize the new package

For this step, we use mySQL with a dedicated database (See [Configuration](configuration.md)).

First we **initiate the core component**; this is required every time we want to use eQual in a new DB.

Open your CLI at the root of eQual's folder and use this :

```bash
$ ./equal.run --do=init_package --package=core
```

Then we do the same for our package. It will automatically create **one table per associated class**.

```bash
$ ./equal.run --do=init_package --package=mypackage
```

Now the database should have the following tables:  

- `core_user`  

* `core_group`  
* `core_rel_group_user`  
* `core_log`  
* `core_permission`  
* `core_translation`  
* `core_version`  
* `mypackage_task`  
* `mypackage_user`  

#### Troubleshooting

If none of the above is working. Try this :

|**PATH**|`core\actions\test\package-consistency.php`|
| --------------- | ------------------------------------------------------------ |
|**URL**|`?do=test_package-consistency&package=core`|
|**CLI**|`$ ./equal.run --do=test_package-consistency --package=core`|
|**DESCRIPTION**|Consistency checks between DB and class as well as syntax validation for classes (PHP), views and translation files (JSON).|

It will tell you if something is wrong or missing. You can ignore any error related to view or translation (they are optional but will display a warning nonetheless).

You can run the same command with your package's name instead of "core", see if the problem lies in here.

|**PATH**|`core\actions\test\package-consistency.php`|
| --------------- | ------------------------------------------------------------ |
|**URL**|`?do=test_package-consistency&package=myPackage`|
|**CLI**|`$ ./equal.run --do=test_package-consistency --package=myPackage`|
|**DESCRIPTION**|Consistency checks between DB and class as well as syntax validation for classes (PHP), views and translation files (JSON).|



### 4. Grant CRUD permissions

By default you don't have any rights to read, write or delete in the DB. This is for security reasons.

If you want to bypass this you have 2 options :

- Use the CLI to grant the permissions

- Debug mode (for testing purpose)

### CLI commands

When you make a CLI command, eQual automatically bypass all limitations. It makes it the only reliable way to give and retain rights within your project.

The rights available are **create**, **read**, **update**, **delete**, **manage**.

For instance, we'll continue with our todolist example and grant the permission to **read** for the group of objects **Task** :

```bash
$ ./equal.run --do=group_grant --group=default --right=read --entity=mypackage\Task
```

**You can only grant one right at a time**, it means we'll need to repeat this command for every permission we want to give (i.e. create, read, write, delete, manage).

If you want to target **all the classes** of a package, you can specify with ``` --entity=mypackage\* ```.

### Debug mode

Alternatively, you can grant all access through all the project. This should only be used for testing purpose.

In the `/config` folder, open the **`config.inc.php`** file. If you don't have one, create one by copying it from `default.inc.php`.

There is a parameter called "DEFAULT_RIGHTS" you can customize :

```php
<?php
// [...]

	// replace this
    define('DEFAULT_RIGHTS', 0);

	// by this
    define('DEFAULT_RIGHTS', QN_R_CREATE | QN_R_READ | QN_R_DELETE | QN_R_WRITE);

// [...]
```

Uncomment the commented part, comment the uncommented part, then save.

Note: eQual uses binary masks for granting rights : 

- 0 means **no rights** 
- 1 is **create** 
- 2 is **read** 
- 4 is **write/update**
- 8 is **delete**
- 16 is **manage**



## 5. Change default view

In `/config`, open *config.inc.php* or *default.inc.php* (unrecommended) and adapt this :

```php
define('default_package', 'mypackage');
```

In `/packages/mypackage`, create a *config.inc.php* file and add these lines :

```php
<?php
namespace config;

define('DEFAULT_APP', 'landing');	
// it refers to packages/mypackage/apps/landing.php
```

And we're done, eQual will now use the landing controller when requesting mypackage.

To test it, navigate to: [http://equal.local/?show=mypackage](http://equal.local/?show=mypackage)

### More learning ressources

See [*Usage*](../usage/directory-structure.md) and [*Howtos*](../howtos-and-examples/generic-cheat-sheet.md)

