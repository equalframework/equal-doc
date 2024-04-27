# Rights management



## Users 
The structure is defined inside the `core\User` class (`packages/core/classes/User.class.php`).

Each User object holds a list of groups to which it belongs.

!!! Note
    All users belong to the group "users", which is the default group (see `DEFAULT_GROUP_ID` in `/eq.lib.php`).

```php
<?php
public static function getColumns() {
	return [
		'firstname'		=> ['type' => 'string'],        
		'lastname'		=> ['type' => 'string'],
		'login'			=> ['type' => 'string', 'label' => 'Username'],
		'password'		=> ['type' => 'string', 'label' => 'Password'],
		'language'		=> ['type' => 'string'],
		'groups_ids'	=> ['type' => 'many2many', 
						  'foreign_object'	=> 'core\Group', 
						  'foreign_field'	=> 'users_ids', 
						  'rel_table'		=> 'core_rel_group_user', 
						  'rel_foreign_key'	=> 'group_id', 
						  'rel_local_key'	=> 'user_id']
	];
}
```



## Groups

The structure is defined inside the `core\Group` class (`packages/core/classes/Group.class.php`).

There, you will find informations about the users inside the group, and also the rights attached to these groups. 

```php
<?php
public static function getColumns() {
	return [
		'name'			=> ['type' => 'string'],
        
		'users_ids'		=> ['type' => 'many2many', 
						  'foreign_object'	=> 'core\User', 
						  'foreign_field'	=> 'groups_ids', 
						  'rel_table'		=> 'core_rel_group_user', 
						  'rel_foreign_key'	=> 'user_id', 
						  'rel_local_key'	=> 'group_id'],
                            
		'permissions_ids'	=> ['type' => 'one2many', 
						'foreign_object'	=> 'core\Permission', 
						'foreign_field'		=> 'group_id']
	];
}
```



## ACL 

Rights assignment is carried out using ACLs that associate a permission with a group or a user.

There are different levels of permission (`CREATE`, `READ`, `WRITE`, `DELETE`, `MANAGE`) that can combine via a binary mask.

At the configuration level, a composite binary mask is assigned to each user (individually or by group).
During an access attempt with a specific permission level, the user's binary permission mask on the targeted object is retrieved and compared to the required level.

The most permissive authorization always takes precedence.


The `Permission` class is used to model the ACLs (Access Control Lists).

Located at `packages/core/classes/Permission.class.php`, the `Permission` class is dedicated to rights management: for each object class (including the 'Permission' class itself), rights can be assigned to each existing group.

A user can be a member of several groups, and a group can have multiple members, forming a many-to-many relationship.

```php
<?php
public static function getColumns() {
	return [
		'class_name'	=> ['type' => 'string'],
		'group_id'		=> [
						'type'				=> 'many2one', 
						'foreign_object'	=> 'core\Group', 
						'foreign_field'		=> 'permissions_ids'
					],
		'rights'		=> ['type' => 'integer']
	];
}
```



The field 'rights' of the `Permission` class is a binary mask (logical OR) of the rights given to the related group.
If a user belongs to several groups, the permission set will result in the most permissive combination of the rights from all its groups.

Rights values that can be assigned are defined in the file `/eq.lib.php` :
```php
<?php
    
define('R_CREATE',	1);	
define('R_READ',	2);	
define('R_WRITE',	4);	
define('R_DELETE',	8); 	
define('R_MANAGE',	16); 	// autorisation to manage the rights 
```



!!! note "Default rights"
    All users receive the default permissions, defined in the configuration file (`/config.json`) through setting `DEFAULT_RIGHTS`.

!!! note "User's own object"
    By convention, a User is always granted `READ` and `WRITE` his own object.



## AccessController

The `AccessController` service is in charge of retrieving the permissions granted to a given group or user. This service is called by default and you usually don't have to think about it.


### ACL logic (Permissions or Rights)

Objective: Determine if a user has certain rights on a class or on given objects.

Basic Logic:

* ACLs grant rights (corollary: retrieving rights or ACLs is the same)
* We seek the minimum common right for all given objects granted to a specific user
* If ACLs are not eligible, we ignore them (they do not grant rights)
    (eligible = they apply to the user or a group to which the user belongs)

When it comes to finding applicable rights, there are two possible situations.

1. Providing a class or a wildcard without object identifiers: Retrieve corresponding ACLs

2. Providing a class and a list of object identifiers (collection): Retrieve specific ACLs for the provided objects

    

#### 1.a Explicit Class (`getUserRightsOnClass()`)

Rules for classes: This type of ACL always indicates a null object_id.

1) Look for ACLs for this class
2) Augment with rights from wildcards on the class namespace (and subsequent wildcards)
3) If none are found, use rights on the parent class (recursion)

If a right is given for a class but there is a wildcard with more rights, which one takes precedence.

If nothing is found, default rights are used.

Example:

1. Find ACLs for this class
    `lodging\identity\Identity`	=> class_name like `lodging\identity\Identity`

2. Find wildcards
    `lodging\identity\Identity`	=> `lodging\identity\*`, `lodging\*` 

3. Find all parent classes
    `lodging\identity\Identity`	=> class_name like `identity\Identity`

    

#### 1.b Wildcards (`getUserRightsOnWildcard()`)

Wildcards apply to rules on classes: This type of ACL always indicates a null object_id.

1) Check if there is an ACL entry exactly matching the given wildcard
2) Augment with subsequent wildcard rights

(No search is performed for parent classes)



#### 2. Retrieve specific object rights (`getUserRightsOnObjects()`)

Object_ids are inseparable from the class: This type of ACL always indicates an explicit class.
(When a right is granted to an object, it is implicitly granted to parent classes [sharing a `getTable()`])








??? tip "Overriding AccessController"

    As all eQual services, the AccesController service can be overridden by a custom service to match any specific logic.
    
    **Example:**  
    
    1) Under `/lib` folder, create a folder by the name of your lib, and create a file for your Service (e.g. `/lib/mylib/access/AccessController.class.php`)  
    
    ```php
    <?php
    namespace mylib\access; 
    
    class AccessController extends \equal\access\AccessController {
        
        // rewrite functions here to override their default behavior
        public function hasRight($user_id, $operation, $object_class='*', $objects_ids=[]) { {
    	    // [...]
        }	
        		
        // [...]
    }
    ```
    
    2) When creating a controller that must use the specific logic, the service can be injected this way: 
     
    ```
    <?php
    list($params, $providers) = eQual::announce([
        'description'   => 'Returns a list of entities according to given domain (filter), start offset, limit and order.',
        'params'        => [
        ],
        'constants'     => ['DEFAULT_LANG'],
        'response'      => [
            'content-type'  => 'application/json',
            'charset'       => 'utf-8',
            'accept-origin' => '*'
        ],
        'providers'     => [ 'context', 'orm', 'adapt' => 'mylib\access\AccessController']
    ]);
    
    /**
     * @var \equal\php\Context               $context
     * @var \equal\orm\ObjectManager         $orm
     * @var \equal\data\DataAdapterProvider  $dap
     */
    list($context, $orm, $dap) = [ $providers['context'], $providers['orm'], $providers['adapt'] ];
    `
    // [...]
    ```