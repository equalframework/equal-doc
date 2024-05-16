# Authorization

## AccessController

Access Control is a crucial topic, and eQual provides several mechanisms to authorize user interactions performed on entities by supporting a wide range of Access Control strategies:

* ACL (Access Control Lists)
* ABAC (Attribute-Based Access Control)
* RBAC (Role-Based Access Control)
* PBAC (Policy-Based Access Control)

!!! info "Other policy systems"
    An interesting comparison of some existing policy systems can be found on [openpolicyagent.org](https://www.openpolicyagent.org/docs/latest/comparison-to-other-systems/)

### Lexicon

There are four notions involved in authorizations:

* **ACLs**: Used to set permissions on CRUD operations (create, read, update, delete), which may be granted or revoked at the entity level for groups or users.
* **Policies**: Used to define entity-specific conditions that depend on both the current user and the state of an object (via `isCompliant()`).
* **Roles**: Arbitrary roles (identified by name, e.g., 'owner', 'admin', 'editor', 'viewer') can be assigned to a user on a given object. Roles can be hierarchical (e.g., an 'owner' is an 'admin', and an 'admin' is an 'editor'). The roles are defined at the entity level (each entity can enumerate assignable roles).
* **Actions**: One or more policies and/or roles can be attached to an action.

#### Groups vs Roles

A group is a collection of users with a given set of permissions assigned to the group (and, transitively, to the users). 

A role is a collection of granted actions (relating both to permissions and specific conditions/policies), and a user inherits those grants when they act under that role. Groups are based on identity, whereas roles are meant to demarcate activity that can be performed by a user or an application.

### Access Control Logic Recap

Access Control involves several components:

```
Access Control
├── Model (at field level, using `access`, `readonly`, and `visible` attributes)
├── CRUD operations
│    ├── CRWD rights (ACL) (via `hasRight()`)
│    └── (through `can[...]()` methods), Policies (via `isCompliant()`), Roles (via `hasRole()`)
└── Actions
     ├── Roles (via `hasRole()`) : restrict action to certain roles (RBAC)
     └── Policies (via `isCompliant()`) : restrict action to certain policies (complex rules ABAC, PBAC)
          └── Roles (via `hasRole()`) 
```

Note: Besides Access Control, other constraints might apply when requesting a transition on an object from one state to another (if a workflow is assigned to the related entity).

### Collections

* filterByUser
* filterByACL
* filterByRole
* filterByPolicy

### Policies

Policies are methods defined at the entity level, describing the authorization logic that applies to the related entity for specific actions.

### Roles

**Objets core_role**

Fields: `user_id`, `object_class`, `object_id`, `role`

Roles allow deducing rights and defining constraints like Separation of Duty (SoD).

```php
public static function getRoles() {
    return [
        "owner" => [
            "description" => "",
            "rights" => R_READ | R_WRITE | R_DELETE | R_MANAGE
        ],
        "admin" => [
            "description" => "",
            "implied_by" => ['owner'],
            "rights" => R_READ | R_WRITE | R_DELETE 
        ],
        "editor" => [
            "description" => "",
            "implied_by" => ['admin'],
            "rights" => R_READ | R_WRITE
        ],
        "viewer" => [
            "description" => "",
            "implied_by" => ['editor'],
            "rights" => R_READ
        ]
    ];
}

public static function getRoles() {
    return [
        "payment-creator" => [
            "description" => "",
            "excluded_by" => ['payment-approver']
        ],
        "payment-approver" => [
            "description" => "",
            "excluded_by" => ['payment-creator']
        ]
    ];
}
```

Role assignment is done using `Assignment` objects.

```sql
Assignment
    object_class
    object_id
    role
    user_id
```

SQL queries for assignments are direct (no table inheritance). Beforehand, all the roles that involve the requested assignment must be listed.

Example:
```php
$map_roles = [];
$map_roles[$role] = true;
$descriptor = $roles[$role];
while(isset($descriptor['implied_by'])) {
    foreach((array) $descriptor['implied_by'] as $r) {
        $map_roles[$r] = true;
    }
    $descriptor = $descriptor['implied_by'];
}

$roles = array_keys($map_roles);
```

Note: Inheritance can be set up for roles by using specific policies (possibly associated with 'view', 'edit', 'delete' actions). Example: 'folders' objects containing 'documents'.

### ACL

ACLs are defined using a dedicated class `Permission`.

Roles are assigned using a dedicated class `Assignment`.

**Permission Fields:**  
* object_class
* object_id
* rights
* group_id
* user_id

Permissions can be inherited from one of the groups a user belongs to. Additionally, if a right is granted to a user on a given entity, it is also granted on all entities that inherit from that entity.

### AccessController Methods

```php
AccessController::hasRole($user_id, $role, $object_class, $object_id) // used at the object level in policy handlers or `can[...]()` methods
AccessController::hasRight($user_id, $operation_mask, $object_class, $object_ids) // used at the collection level for CRUD operations
AccessController::canPerform($user_id, $action, $collection) // check if the user can perform an action based on associated policies
AccessController::isCompliant($user_id, $policy, $collection) // check policy compliance at the collection level
```

### Collection Methods

* `Collection::has([$right_or_role])` // Rights are integers, roles are strings
* `Collection::complies([$policy])`
* `Collection::can([$action])` -> `$access->canPerform`
* `Collection::do($action)`

### Policies

Policies are represented as an associative array mapping policy names with policy handlers. Policy handlers return an associative array mapping error IDs with their descriptions. If the returned array is empty, the action is allowed.

```php
public static function getPolicies() {
    return [
        'updatable' => [
            'description' => "",
            'function' => 'policyUpdatable'
        ],
        'subscribable' => [ 
            'description' => '',
            'function' => 'policySubscribable'
        ],
        'publishable' => [
            'description' => "Policy defining the rules for an object to be publishable.",
            'function' => 'policyPublishable'
        ]
    ];
}

public static function policySubscribable($self, $user_id) {
    $result = [];
    $user = User::id($user_id)->read(['points'])->first();
    foreach($self as $id => $object) {
        if (!$access->hasRole($user_id, 'viewer', self::getType(), $id)) {
            $result[$id] = ['missing_role' => "user doesn't have the viewer role"];
            continue;
        }
        if ($user['points'] < $object['points']) {
            $result[$id] = ['missing_points' => "user doesn't have enough points"];
            continue;
        }
        if (!$object['is_subscribable']) {
            $result[$id] = ['not_subscribable' => "object is not subscribable"];
            continue;
        }
    }
    return $result;
}
```

Policies can be defined to check if a given user can perform an operation (action or transition). The check can either be based on:

* The status or the value of some fields (ABAC)
* The roles of a specific user (RBAC)
* Indirect relationships (at user or object level)
* Or a combination of these.

The use of policies can be done directly by using the AccessController `isCompliant()` method, or indirectly by using entity actions or transitions (workflow).

### Direct call to AccessController

Example: `report_do-publish.php`

```php
if(!Report::ids($params['ids'])->can('publish')) {
    throw new Exception('cannot_publish', QN_ERROR_NOT_ALLOWED);
}
```

* It is possible to define one or multiple policies on a workflow transition (similar to the domain). Such policies are conditions that need to be fulfilled for the transition.
  * The 'domain' property pertains to the state of the object.
  * The 'policies' property pertains to conditions involving the user.

* Other services can be injected into ORM calls (e.g., `$auth`, `$access`).

The `canupdate` and `candelete` methods allow arbitrary conditions on CRUD operations. For complex mechanisms, it is advisable to use a workflow and define the update possibilities field by field.

### Indirect call to AccessController with actions defined in `getActions()`

Actions are methods that contain a series of arbitrary actions (independent of the status) and are limited based on the current user. An action without policy can be called by any user, but the rights on the involved objects still apply.

By using this mechanism, you can organize and name your actions, making them

 more identifiable and easier to work with. Additionally, you can enforce specific constraints based on user profiles, group memberships, or other user attributes, ensuring that the appropriate permissions are in place before executing the actions.

To use the actions defined in `getActions()`, follow these steps:

* Implement the `getActions()` method, which should return an object containing the defined actions. Each key should represent a specific action to be performed.
* Within the action methods, include the desired logic and operations that need to be executed. These actions can be independent of the current status.
* Ensure that the appropriate user-based constraints and permissions are applied within the action methods, considering the associated policies.

**Example of `getActions()` Implementation:**

```php
public static function getActions() {
    return [
        'publish' => [
            'description' => "",
            'policies' => ['publishable'], // Policies to comply with to perform the action
            'function' => '' // Name of the method to call to perform the action
        ]
    ];
}
```

**Calling a Specific Action:**

ORM:
```php
$orm->do(Report::getType(), $ids, 'publish');
```

Collection:
```php
Report::ids($ids)->do('publish');
```

In the ORM, all policies are verified via `$ac->can()`. If the action is authorized, the associated method is applied to the collection.

### Field Access

eQual handles access permissions on a per-object basis.

If a user is granted some rights on an object, they own those rights on all fields of the object.

If certain information involves distinct usage profiles, it might be necessary to consider splitting the object class into smaller entities with distinct rights for each entity.

* Fields can have specific behavior based on their descriptor (`readonly`, `required`, `visible`), which can be overridden based on the object's status.
* Actions involving operations on certain fields can be conditioned by policies.
* CRUD operations are performed by executing `can[...]()` methods, which, if defined, allow filtering operations (and potentially rejecting them) based on specific criteria, potentially related to the fields involved in the operation.

### `policies` attribute

The `policies` attribute is similar to the "visibility" attribute (which affects only the UI). It holds a series of policy names. If one of the policies is not validated for the current user, access to the related field is denied.

### `access` attribute

The `access` attribute defines if the field is accessible for the current user.

```json
access {
    groups [ids or names],
    users [ids or logins],
    roles,
    visibility [
        public: no restriction (default),
        protected: accessible to authenticated users only,
        private: accessible to root user only (system or CLI) - not to be revealed to users (ex: login)
    ]
}
```

!!! ,ote "ACL at package initialization"
    For classes implying some initial ACL and rights based on users and groups, it is recommended to include related JSON files in the `./init` folder of the package for importing those ACL at package init.

## Collections Handling

#### Create

If the entity has a `getRoles()` method: 

* Creation is not supported (roles relate to existing objects).
* Use actions (e.g., a 'create' action conditioned by policies, which adds the 'owner' role for the user to the created object).

Otherwise:

* Check if the user has the right `R_CREATE` on the entity (via their groups and/or permissions on parent entities).
* If not, creation fails.

#### Search

If the entity has a `getRoles()` method: 

* Identify roles with `R_READ` permission (according to the entity's roles). 
* Search for objects where the user has one of these roles (via `core_assignment`), and add a condition to the domain (`id in []`).	

Otherwise: 

* Check if the user has the `R_READ` permission on the entity (via their groups and/or rights on parent entities: `getUserRights(user_id, class)`).
* If yes, apply the search with the domain.
* If not, list objects where the user has the `R_READ` permission (direct), and add a condition to the domain (`id in []`).

#### Read, Update, Delete

If the entity has a `getRoles()` method (not inherited):

* Identify roles with the `R_READ` permission (according to the entity's roles).
* For each object in the collection, verify if the user has one of these roles.
* If not, the operation fails (all-or-nothing: the user must have the required rights on all objects in the collection, or the operation is canceled).

Otherwise: 

* Check if the user has the `R_READ` permission on the entity (via their groups and/or rights on parent entities).
* If yes, the operation is allowed.
* If not, verify for each object if the user has the `R_READ` permission.
* The smallest permission for the collection can be retrieved via `getUserRights()`.