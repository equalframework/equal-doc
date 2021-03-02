# Models definition



Each Model  is defined in a `.class.php` file , located in the `/packages/{package_name}/classes` file of the package it relates to (see [Directory Structure](directory-structure.md))



Every class inherits from a common ancestor: the  `Model` class declared in the `qinoa\orm` namespace and defined in `/lib/qinoa/orm/Model.class.php`

Using eQual API, a class is always referred to with the package name to which it belongs (packages are used as namespaces).

The syntax is : `package_name\class_name` (ex. :'school\Teacher').

A class consists of several fields, each of them having a name and a type, and a list of methods.\\ 
Some methods are system (their name is standard and used by the ORM), and others are specific to a class and defined by the user (see below).



##  Classes

Objects are grouped into classes that define the structure and methods of those objects. 

Classes definitions from a same package are placed into the folder //packages/[package_name]/classes//.

Here is an example of the class definition syntax :
```php
<?php
namespace school;
use qinoa\orm\Model;

class Student extends Model {
  public static function getColumns() {
    return [
      'firstname'  => ['type' => 'string'],
      'lastname'   => ['type' => 'string'],
      'birthdate'  => ['type' => 'date']
    ];
  }
}
```



## Consistency between a .class.php definition and database schema 

In parallel, a table must be defined in the database that has a structure matching the related class definition. The main constraint being that the types must be compatibles (ex.: a varchar(255) column in the database may represent a string, as well as a short_text or a text in the related class).

Consistency between type definition and related table structure in DB must be permanently maintained. 
This is left to the responsibility of the developer: no process neither checks nor fixes potential errors. However, some plugin might help you in this task (see Utility and stand-alone scripts).





## Fields types

### Basic fields

These fields values are directly stored in the associated SQL table and don't need to be processed.

Basic fields include **boolean**, **integer**, **float**, **string**, **text**, **array**, **date**, **time**, **datetime**, **file**, **binary**, **many2one**

Each of them correspond to a specific value :



#### boolean

Numeric value of Boolean type (**true** or **false**)

> We use the PHP built-in constant : true and false

#### integer

Signed numeric value (negative or positive)

> With PHP it depends on the platform (generally 32 bits signed), with SQL it depends on t he chosen type and size

#### float

Floating point numeric value (float or double)

#### string

Short string without formatting/carriage returns (ex: lastname, place, …)

#### text

Text potentially long and including formatting

> notes : with SQL, the types TEXT, BLOB or MEDIUMTEXT, MEDIUMBLOB are recommended 

#### binary

Any binary value (ex : a picture, a document, …)

> With SQL, the MEDIUMBLOB type is recommended



#### date, time & datetime


Internally dates, times and datetimes are handled as timestamps.

However, when stored to the DBMS, those types follow the standard SQL format:

* date: YYYY-mm-dd
* time: HH:mm:ss
* datetime: YYYY-mm-dd HH:mm:ss

#### many2one
Relational field used for fields holding a N-1 relation, that is to say a numeric value that represents the identifier of the pointed object.

N-1 relation, generating an integer to identify the related one2many object


	foreign_object -> select amongst other classes
		+ ability to create symetrical one (one2many)
```php
// todolist\Class
// Each task has only 1 user assigned at a time
'user_id' => [
    'type'           => 'many2one',
    'foreign_object' => 'todolist\User'
]
```



### Complex fields

Those fields require additional processing to be retrieved

Complex fields include , **one2many**, **many2many**, **function**



#### one2many

1-N relation, related to a many2one object

		foreign_object -> select amongst other classes
		foreign_field -> select amongst fields of other class
				+ ability to create a new one (many2many) : création temporaire dans un Sens ou un autre !
		rel_table -> check amongst existing rel tables ({package}_rel_local_foreign or rel_foreign_local)
				if no table yet -> create
		rel_local_key -> {local_class}_id
		rel_foreign_key -> {foreign_class}_id


```php
// todolist\User
// Each user can have many tasks
'tasks_ids'  => [
    'type'           => 'one2many',
    'foreign_object' => 'todolist\Task',
    'foreign_field'  => 'user_id'
]
```

#### many2many

M-N relation

	foreign_object -> select amongst other classes
		+ ability to create symetrical one (one2many)
```php
// school\Teacher
'courses_ids' => [
    'type' 			  => 'many2many', 
    'foreign_object'  => 'school\Course', 
    'foreign_field'	  => 'teachers_ids', 
    'rel_table'		  => 'school_rel_course_teacher', 
    'rel_foreign_key' => 'course_id', 
    'rel_local_key'	  => 'teacher_id'
]
```

#### function

	result_type : select ('boolean', 'integer', 'float', 'string', 'text', 'html' )
	create handler name auto
	store : boolean
Calls any mentioned function, it's mostly used to return a processed value

> Most of the time use of the store attribute requires that field(s) on which depends the computed value of has an onchange event, triggering the update of the calculated field (see example).

When trying to load a function field, 

  * if 'store' is not defined or set to false, it computes the value using the provided method each time the field value is requested
  * if 'store' is set to true and field is not is DB (NULL), it computes the value using the provided method
  * if 'store' is set to true and field is in DB, it uses the DB value

```php
// Example from core\Permission
// ...
    'rights' => [
        'type'		   => 'integer',
        'onchange'	   => 'core\Permission::onchangeRights'
    ],
    'rights_txt' => [
        'type'		   => 'function', 
        'store'		   => true, 
        'result_type'  => 'string', 
        'function'	   => 'core\Permission::getRightsTxt'
    ],
// ...


public static function onchangeRights($om, $ids, $lang) {
    $rights = Permission::getRightsTxt($om, $ids, $lang);
    foreach($ids as $oid) { 
        $om->write('core\Permission', $oid, ['rights_txt' => $rights[$oid]], $lang);
    }
}

public static function getRightsTxt($om, $ids, $lang) {
    $res = array();
    $values = $om->read('core\Permission', $ids, array('rights'), $lang);
    foreach($ids as $oid) {
        $rights_txt = array();
        $rights = $values[$oid]['rights'];
        if($rights & QN_R_CREATE)   $rights_txt[] = 'create';
        if($rights & QN_R_READ)     $rights_txt[] = 'read';
        if($rights & QN_R_WRITE)    $rights_txt[] = 'write';
        if($rights & QN_R_DELETE)   $rights_txt[] = 'delete';
        if($rights & QN_R_MANAGE)   $rights_txt[] = 'manage';
        $res[$oid] = implode(', ', $rights_txt);
    }
    return $res;
}
```





## Fields attributes

| Type         | Attribute   |Usage                    |
| - | -|-|
| ***Basic fields*** | type        | specify the field type   |
|              | [multilang] | boolean telling if current field can be translated (default value: false)  |
|              | [onchange] | string holding the name of the method to invoke when field is updated, with format : `package\Class::method` (note : this method will be called with PHP function `call_user_func()`) |
|              | [selection] | Value selected from a pre-defined list (*) |
| **many2one**    | foreign_object     | class toward which current field is pointing back |
| **one2many**    | foreign_object     | class toward which current field is pointing back |
|     | foreign_field     | name of the field of the pointed class that is pointing back toward the current class        |
|     | [foreign_key]     | field that serves as identifier for objects pointed by the relation (default value: 'id')        |
|     | [order]     | field on which pointed objects must be sorted        |
|     | [sort]     | direction fort sorting (possible values: 'asc', 'desc') |
| **many2many**    | foreign_object     | class toward which is pointing the current field        |
|     | foreign_field | name of the field of the pointed class that is pointing back toward the current class |
|     | rel_table | name of the SQL table dedicated to the m2m relation (recommended syntax: `package_rel_class1_class2`) |
|     | rel_local_key | name of the column in `rel_table` holding the identifier of the current object |
|     | rel_foreign_key | name of the column in de `rel_table` holding the identifier of the pointed object |
| **related**    | result_type  | type resulting from the indirect relation (i.e. type of the final pointed field) |
|     | foreign_object | class of the target field |
|     | path     | array holding the names of the cascade fields to consult in order to get to the final field (note : the first field must belong to the current class) |
| **function**    | result_type     | type of the value resulting from the invoked function |
|     | store     | boolean telling if the function result must be stored in database (in that case, the related table must contain a column for the field) |
|     | function  | string holding the name of the method to invoke, with format : `package\Class::method` (note : this method will be called with PHP function `call_user_func`) |
|              | [multilang] | boolean telling if field can be translated (default value: false). This applies only for stored fields.  |



(*) Example of a basic field using the `selection` attribute:

```php 
'fieldname' => [
    'type'      => 'string',
    'selection' => ['choice1', 'choice2','choice3']
]
```

### onchange method

When updating a field, if `onchange` attribute is defined, related method is executed
(method is in charge of updating fields that depends on current field)

### System fields
### Common methods 
### Custom methods