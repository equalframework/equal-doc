# Quick Start

This section covers the first steps to manage a backend service or API, using eQual



## 1. Create your package

In **public/packages/**, create a **new folder**. For this example, we'll name it *"myapp"*.
In `mypackage`, create a bunch of new folders named `classes` and `data`
Directory should look like this :

```
/packages
    /mypackage
        /classes
        /data
```



## 2. Define custom classes

In `public/packages/mypackage/classes/`, we add a new **.class.php** file for each class we want to use. 
In this example, we define the class **Task** for a todo-list app :

*../packages/mypackage/classes/Task.class.php*

```php
<?php  
namespace mypackage;
use qinoa\orm\Model; // this is a built-in object handler
    
class Task extends Model {
    public static function getColumns() {
        return array(
            'title'     => ['type' => 'string'],
            'content'   => ['type' => 'text']
        );
    }
}
```

*Note: ID key generation is handled by eQual*

But, what if we want to establish a relation between two classes, like nesting the **User** of *User.class.php* as a parameter?
That's where the types **many2many**, **one2many**, and **many2one** come in handy (*see [Understanding DBMS relationships](https://afteracademy.com/blog/what-are-the-different-types-of-relationships-in-dbms)*)

```php
// Task
// ...
      return array(
        // ...
        'user_id'	=> ['type' => 'many2one', 'foreign_object' => 'myapp\User']
      );
```

We also need to do the opposite in *User.class.php* :

```php
// User
// ...
      return array(
        // ...
        'task_id'	=> ['type' => 'one2many', 'foreign_object' => 'myapp\Task']
      );
```



## 3. Initiate package in the database

For this step, we use mySQL with a dedicated database (See [Configuration](configuration.md))

First we **initiate the core component**; this is required every time we want to use eQual in a new DB

Open your CLI at the root of eQual's folder and use this :

```bash
php run.php --do=init_package --package=core
```

Then we do the same for our package. It will automatically create **one table per associated class**

```bash
php run.php --do=init_package --package=mypackage
```

Now the dabase "equal" should have the following tables: core_contact, core_group, core_log, core_permission, core_rel_group_user, core_translation, core_user, core_version, mypackage_task, mypackage_user

##### Troubleshooting

If none of the above is working. Try this :

```bash
php run.php --do=test_package-consistency --package=core
```

It will tell you if something is wrong or missing. You can ignore any error related to view or translation (they are optional but will display a warning nonetheless)

You can run the same command with your package's name instead of "core", see if the problem lies in here

```bash
php run.php --do=test_package-consistency --package=mypackage
```



## 4. Grant CRUD permissions

By default you don't have any rights to read, write or delete in the DB. This is for security reasons

If you want to bypass this you have 2 options :

- Use the CLI to grant the permissions

- Debug mode (for testing purpose)

### CLI commands

When you make a CLI command, eQual automatically bypass all limitations. It makes it the only reliable way to give and retain rights within your project

The rights available are **"create", "read", "update", "delete", "manage"**

For instance, we'll continue with our todolist example and grant the permission to **read** for the group of objects **Task** :

```bash
php run.php --do=group_grant --group=2 --right=read --entity=mypackage\Task
```

**You can only grant one right for one entity at a time**, it means we'll need to repeat this command for every permission we want to give, and for every object

The good thing is once it's done you don't ever need to redo it, unless you initiated the core package again in the DB (it wipes its data, including core_permissions)

### Debug mode

Alternatively, you can grant all access through all the project. This should only be used for testing purpose

In the **config** folder, open the config.inc.php file. If you don't have one open default.inc.php instead

There is a parameter called "DEFAULT_RIGHTS" you can tweak :

```php
// config.inc.php
<?php
// [...]

    // define('DEFAULT_RIGHTS', QN_R_CREATE | QN_R_READ | QN_R_DELETE | QN_R_WRITE);
    define('DEFAULT_RIGHTS', 0);

// [...]
```

Uncomment the commented part, comment the uncommented part, then save

It's using a binary mask and 0 means no rights. Create is 1, read is 2, write/update is 4, delete is 8, and manage is 16.

Alternatively, you can temporarily replace 0 with the addition of the binary values of the rights you want to give

Don't forget to **switch back** once you're done



## 5. Change default view

In **public/config/**, open *config.inc.php* or *default.inc.php* (unrecommended) and adapt this :

```php
define('default_package', 'mypackage');
```

In **public/packages/myapp/**, create a *config.inc.php* file and write this :

```php
<?php
namespace config;

define('DEFAULT_APP', 'landing');	// it refers to packages/mypackage/apps/landing.php
```

And we're done, eQual will now display landing.php as default landing page



### More learning ressources

See [*Usage*](../usage/directory-structure.md) and [*Howtos*](../howtos-and-examples/generic-cheat-sheet.md)