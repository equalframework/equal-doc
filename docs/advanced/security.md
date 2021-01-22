===== 6. Rights management =====

The 'Permission' class is dedicated to the rights management: for each object class (including the 'Permission' class itself), rights can be assigned to each existing group.

==== Users ====
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





==== Groups ====


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


==== ACL ====
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


==== Permission algorithm ====
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


==== Default rights ====
In addition, all users receive the default permissions, defined in the configuration file (see //DEFAULT_RIGHTS// constant in /config.inc.php).