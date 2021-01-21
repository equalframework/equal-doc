# Object definition

Every class of object is defined by the **.class.php** file type, and is located in the **/classes** file of your project (see [Directory Structure](directory-structure.md))

See this example for class definition syntax :

```php
namespace myproject;

class Myobject {
 public static function getColumns() {
  return [
   'myfield'	  => ['type' => 'string'],
   'anotherfield' => ['type' => 'integer', 'text']
  ];
 }
}
```



## Fields types

### Basic fields

These fields values are directly stored in the associated SQL table and don't need to be processed

Basic fields include **boolean**, **integer**, **float**, **string**, **short_text**, **text**, **date**, **time**, **datetime**, **timestamp**, **selection**, **binary**

Each of them correspond to a specific value :



#### boolean

Numeric value of Boolean type (**true** or **false**)

#### integer

Signed numeric value (negative or positive)

#### float

Floating point numeric value (float or double)

#### string

Short string without formatting/carriage returns (ex: lastname, place, …)

#### short_text

String potentially containing carriage returns, without formatting (ex:  a short description, an address, …)

#### text

Text potentially long and including formatting

#### binary

Any binary value (ex : a picture, a document, …)

#### selection

Value selected from a pre-defined list

```php 
'fieldname' => ['type' => 'selection' => ['choice1', 'choice2','choice3']]
```

#### date

Date with format **YYYY-mm-dd**

#### time

Time with format **HH:mm:ss**

#### datetime

Date with format **YYYY-mm-dd HH:mm:ss**

#### timestamp

Date with format **YYYY-mm-dd**



### Complex fields

Those fields require addional processing to be retrieved

Complex fields include **many2one**, **one2many**, **many2many**, **related**, **function**



#### many2one

N-1 relation, generating an integer to identify the related one2many object

```php
// todolist\Class
// Each task has only 1 user assigned at a time
'user_id' => [
    'type' => 'many2one',
    'foreign_object' => 'todolist\User']
```

#### one2many

1-N relation, related to a many2one object

```php
// todolist\User
// Each user can have many tasks
'tasks_ids'  => [
    'type' => 'one2many',
    'foreign_object' => 'todolist\Task',
    'foreign_field' => 'user_id']
```

#### many2many

M-N relation

```php
// school\Teacher
'courses_ids' => [
    'type' 			  => 'many2many', 
    'foreign_object'  => 'school\Course', 
    'foreign_field'	  => 'teachers_ids', 
    'rel_table'		  => 'school_rel_course_teacher', 
    'rel_foreign_key' => 'course_id', 
    'rel_local_key'	  => 'teacher_id'
],
```

#### related

Indirect relation of type many2one or one2many. It's done by pointing to the field of another object

```php
// school\Lesson
'school_id' => [
    'type' 			 => 'related',
    'result_type' 	 => 'many2one', 
    'foreign_object' => 'school\School', 
    'path'			 => [ 'class_id','course_id','school_id' ]
]
```

#### function

Calls any mentioned function, it's mostly used to return a processed value

When trying to load a function field, 

  * if 'store' is not defined, it computes the value using the provided method
  * if 'store' is set to true and field is not is DB (NULL), it computes the value using the provided method
  * if 'store' is set to true and field is in DB, it uses the DB value

```php
// Example from core\Permission
// ...
    'rights' => [
        'type'		=> 'integer',
        'onchange'	=> 'core\Permission::onchange_Rights'
    ],
    'rights_txt' => [
        'type'			=> 'function', 
        'store'			=> true, 
        'result_type'	=> 'string', 
        'function'		=> 'core\Permission::getRightsTxt'
    ],
// ...

// ::onchange_Rights
public static function onchange_rights($om, $ids, $lang) {
    $rights = Permission::getRightsTxt($om, $ids, $lang);
    foreach($ids as $oid) $om->write('core\Permission', $oid, array('rights_txt' => $rights[$oid]), $lang);
}

// ::getRightsTxt
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

