# Rights management

The 'Permission' class is dedicated to the rights management: for each object class (including the 'Permission' class itself), rights can be assigned to each existing group.

## Users 
Every User object holds a list of groups to which it belongs.\ 
Note: a user belongs at least to one group (see //DEFAULT_GROUP_ID// in /fc.lib.php).

Structure is define in the core\User class (library/classes/objects/core/User.class.php)
```php
public static function getColumns() {
	return array(
		'firstname'		=> array('type' => 'string'),
		'lastname'		=> array('type' => 'string'),
		'login'			=> array('type' => 'string', 'label' => 'Username'),
		'password'		=> array('type' => 'string', 'label' => 'Password'),
		'language'		=> array('type' => 'string'),
		'groups_ids'		=> array('type' => 'many2many', 
						  'foreign_object'	=> 'core\Group', 
						  'foreign_field'	=> 'users_ids', 
						  'rel_table'		=> 'core_rel_group_user', 
						  'rel_foreign_key'	=> 'group_id', 
						  'rel_local_key'	=> 'user_id')
	);
}
```



## Groups


Structure is define in the core\Group class (library/classes/objects/core/Group.class.php)
```php
public static function getColumns() {
	return array(
		'name'			=> array('type' => 'string'),
		'users_ids'		=> array('type' => 'many2many', 
						  'foreign_object'	=> 'core\User', 
						  'foreign_field'	=> 'groups_ids', 
						  'rel_table'		=> 'core_rel_group_user', 
						  'rel_foreign_key'	=> 'user_id', 
						  'rel_local_key'	=> 'group_id'),
		'permissions_ids'	=> array('type' => 'one2many', 
						'foreign_object'	=> 'core\Permission', 
						'foreign_field'		=> 'group_id')
	);
}
```


## ACL 
core\Permission (library/classes/objects/core/Permission.class.php)
```php
public static function getColumns() {
	return array(
		'class_name'		=> array('type' => 'string'),
		'group_id'		=> array(
						'type'			=> 'many2one', 
						'foreign_object'	=> 'core\Group', 
						'foreign_field'		=> 'permissions_ids'
					),
		'rights'		=> array('type' => 'integer')
	);
}
```



## Default rights 
In addition, all users receive the default permissions, defined in the configuration file (see //DEFAULT_RIGHTS// constant in /config.inc.php).




## Permission management
The field 'rights' of the Permission class is a binary mask (logical OR) of the rights given to the related group. 
If a user belongs to several groups, the permission set will result in the most permissive combination of the rights from all its groups.

Rights values that can be assigned are defined in the file /fc.lib.php :
```php
	define('R_CREATE',	1);	
	define('R_READ',	2);	
	define('R_WRITE',	4);	
	define('R_DELETE',	8); 	
	define('R_MANAGE',	16); 	// autorisation to manage the rights 
```


### AccessController.php

This file is the built-in "control tower" of eQual, and is located in **/lib/qinoa/access/**. What it does is granting the permission (or not) to perform CRUD actions depending on a few set criteria. This service is called by default and you usually don't have to think about it

But if you need custom security rules, you can also **overwrite AccessController** (at your own risks). This is particularly useful to establish future-proof settings, aswell as an alternative to core_permission (see [Cheat Sheet > Grant DB rights](../howtos-and-examples/generic-cheat-sheet.md)).

In the following section we'll see how to proceed:

### Overriding AccessController

In **/lib**, create a folder by the name of your project, you want a directory similar to this: **/lib/myapp/access/AccessController.class.php**

Then, in the **config.inc.php** file of your package (located at /public/packages/myapp/config.inc.php), add this line:

```php
register('access', 'myapp\access\AccessController');
```

Finally, open your newly created AccessController.class.php and copy paste this :

```php
<?php
namespace myapp\access; // change 'myapp' with actual name
use qinoa\organic\Service;
use qinoa\services\Container;

class AccessController extends \qinoa\access\AccessController {
    
  // rewrite functions here to override their default behavior
    
  // here is a non-exhaustive example with filter:
  filter($operation, $object_class='*', $object_fields=[], $object_ids=[]){
    $user_id = $this->container->get('auth')->userId();
    // grant READ rights over 'User' class when an user is authenticated (0 = guest_user)
    if($object_class == 'myapp\User') {
      if($operation == QN_R_READ) {
        if($user_id > 0) {
          return $object_ids;
        }    
      }
    }
  }
    
}
```

You're ready to write your own rules!

It might be confusing so don't hesitate to look back at */lib/qinoa/access/AccessController.class.php* for inspiration

