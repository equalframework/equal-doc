# ORM

eQual comes with an object-relational mapper (ORM) that greatly eases the interactions with the database by handling the
data conversions, classes inheritance and objects relations.

The ORM service is dedicated to low-level operations on entities (Object-Relational Mapping).  It handles all tasks that relate to objects search
and manipulations and offers an abstract layer for DBMS queries.

It primarily allows for searching, retrieving, and modifying lists of objects (Model).
Operations performed by the ORM are not subject to constraints related to application logic consistency, user permissions, event handling, field calculation, or entity relationship consistency.

All checks (permissions, workflow, data validation, etc.) are exclusively handled via Collections.

Controllers mostly require high-level manipulations (including data conversion, validation and permission checks) and
therefore use [Collections](collections.md).

However, direct use of the ORM in controllers (or entity event handlers) is permitted (as is the use of the DbManipulator service), but it should be done with caution as it poses a potential security risk. Additionally, controllers that inject the ORM service generate a warning during package integrity checks.

Furthermore, there are no logs at this level and no user concept (if fields do not contain a creator/modifier, it is assumed to be the super-user).




The ORM implements methods allowing to :

- **create**, **update** or **delete** one or more objects (based on the ID field)
- **retrieve** a single entity or a list of entities (both based on the ID field)
- **retrieve** a list of IDs of entities that match some criteria



## Object Definition

### Models

In the object-relational mapping (ORM) system, object definitions are structured using classes that inherit from the `equal\orm\Model`. This base class provides methods and properties that are common to all objects within the system.

#### Fields Definition

Each model class must implement at least one mandatory method: `getColumns()`. This method is essential as it defines the model fields.

The `getColumns()` returns an associative array, mapping field names to their definitions. This method allows for the specification of each field's characteristics, such as type, default values, constraints, and any other relevant attributes.

Below is a basic implementation of the `getColumns()` method in PHP:

```php
<?php
[...]
    
public static function getColumns() {
    // Returns an empty array by default
    return [];
}
```

#### Default Values

The `default` property of a field allows automatically assigning a default value when creating a new object. The framework supports scalar values, closures, and references to class methods for defining default values.

##### Scalar Values

Scalar values include numbers, strings, and results from PHP function calls that are evaluated when the class is parsed. In this case, there is no special handling, and all created instances will have the value (or the result of the function) as assigned during the class declaration. This assignment of default values occurs when the ORM first retrieves the class definition.

For example:

```php
<?php 
[...]
    
'day' => [
    'type'    => 'date',
    'default' => time()
]
```

This will generate instances with the same timestamp for all subsequent calls within the same thread.

##### Closure

Using a closure allows for dynamic generation of default values, where a new value can be determined each time a new instance is created. However, this method is limited to basic functionalities and does not allow for the retrieval of contextual information (like services, current user, etc.).

Example of using a closure:

```php
<?php 
[...]
    
'day' => [
    'type'    => 'date',
    'default' => function () { return time(); }
]
```

This configuration will generate instances with a different timestamp for each creation of a new instance.

##### Class Methods

Default values can also be defined by referencing a class method. This method can dynamically compute the default value based on additional context such as the current user.

For instance:

```php
<?php 
[...]
    
'user_id' => [
    'type'           => 'many2one',
    'foreign_object' => 'core\User',
    'default'        => 'defaultUserId'
]

public static function defaultUserId($self, $auth) {
    return $auth->userId();
}
```

In this example, the default value for the `user_id` field is dynamically obtained from the `defaultUserId` method, which utilizes the current authentication service to fetch the user ID.

##### Settings

Default values for fields can be dynamically defined using a Setting value.
This mechanism allows eQual to retrieve default values directly from the settings stored in the database.
To enable this functionality, configure the field's `default` value as *`defaultFromSetting`*.

If the corresponding Setting is not found in the database, the `setting_default` value will act as a fallback.

Example of using a setting:

```php
<?php
[...]

'printer_type' => [
    'type'            => 'string',
    'selection'       => [
        'pos-80',
        'iso-a4'
    ],
    'description'     => 'Printer format to be used for Point Of Sale tickets.',
    'default'         => 'defaultFromSetting',
    'setting_default' => 'iso-a4'
]

```

For eQual to be able to find the Setting, it must be part of the *default* `SettingSection` and follow a naming convention for the `Setting` code field.

In the table above are the Setting values for the field `printer_type` of the class `identity\CenterOffice`:

| Field   | Value                      |
|---------|----------------------------|
| package | identity                   |
| section | default                    |
| code    | center_office.printer_type |

The code is composed of:
  - the namespace (without the package) in snake case and dotted instead of slashed (e.g. `core\alert\MessageModel` becomes `alert.message_model`)
  - the field name prefixed by a dot

In that case, if the Setting `identity.default.center_office.printer_type` does not exist, `iso-a4` will be used as default.

This approach provides greater flexibility in assigning default values to fields, allowing adjustments to be made dynamically via database settings without requiring code changes.
This eliminates the need for redeployment or manual code modifications when default values need to be updated, streamlining maintenance and enhancing adaptability.

## Classes inheritance 


Classes can extend other classes, potentially from different packages, to add new fields or customize behavior.

When a class inherits from another, objects instantiated from this class will contain not only the fields defined in the class itself but also all the fields defined in its ancestor classes. There can be multiple levels of inheritance.

All model classes inherit from the class `equal\orm\Model`.

### Management of Relationships

The ORM automatically manages relationships between entities, with dynamic support for overrides, avoiding the need to manually redefine relational fields.

#### Dynamic Entity Creation

When **eQual** encounters a request for an entity that does not yet exist, but whose namespace corresponds to a parent entity, the framework dynamically generates an empty entity that extends this parent entity. This ensures that relationships between entities remain consistent, even without explicitly overriding the fields. The framework follows a standard naming convention by automatically subclassing entities under the schema `my\package\name\space\Class`.

##### Direct Relationships

In a direct relationship between two entities, as in the example below:

```php
A {
  b: rel_B
}

B {
  a: rel_A
}

my\A extends A {
  
}

my\B extends B {
  
}
```

When an entity **my\A** requests the property **b**, the framework automatically returns an object of class **my\B**.

##### Indirect Relationships

In the case of indirect relationships, where an intermediate entity links two other entities, as in the following example:

```php
A {
  c: rel_C
}

B {
}

C {
  b: rel_B
}

my\B extends B {
  other: any
}

my\C extends C {
}

my\A extends A {
}
```

When the relationship **c.b** is requested from the entity **my\A**, the ORM will return an object of class **my\B**, allowing access to the additional field `other` defined in class **my\B**.

#### Namespace Handling in Relationships

When processing relational fields, the ORM automatically checks the namespace of the related entities. If part of the namespace does not directly match the package of the processed entity but refers to an existing package, the framework prefixes the current processing package to the namespace. This ensures that custom entities are used when necessary.

#### Resolution of Parent Entities

If a custom entity does not exist in a given namespace, the ORM falls back to the parent entity. This ensures the consistency of the relational schema and prevents inconsistencies when extended entities are involved.

#### Advantages

- **Transparent entity overriding**: It is not necessary to manually override entities to maintain relationship consistency. 
- **Relational consistency**: Dynamically generated entities retain correct relationships with their parent entities, ensuring access to all properties and methods, even in complex relationships.
- **Simplicity in view overriding**: Views can be overridden without requiring entity rewriting, facilitating changes in user interfaces and data relationships.

#### Constraints to Observe

- **Naming conventions**: Entity overrides must follow the framework's naming convention, such as `package\name\space\Class` and `my\package\name\space\Class`.
- **Namespace collisions**: There can be no namespace collisions; if the second part of a class's namespace matches a package name, then this class **must** be inherited from a parent class within that package.

### Object Storage Using ORM

To store objects, the ORM utilizes a dedicated table in the database following the active record pattern. By convention, the name of the table for a given class is derived from the name of the first ancestor class that extends `equal\orm\Model`, with the full namespace converted to snake case. For example, objects of the class `realestate\RentalUnit` are stored in the "realestate_rentalunit" table.

However, it is also possible to have inheritance that is distinct from the table assignment in the database.

For example, the classes `sale\customer\Customer` and `identity\Contact` both inherit from the class `identity\Partner`. However, they are distinct objects which are preferable not to mix. In such cases, it is possible to manually define the table to be used for a class via the `getTable()` method.

### Impact of Inheritance on Translations

Inheritance affects translation files as well. All fields are eligible for providing a translation. If some fields are common between a class and its ancestor, they can be overridden. If not, the translations from the first ancestor declaring a translation will be utilized.

If a JSON file is absent for a given class, the system returns the first available JSON file from the class ancestors. For example, if no translation file is defined for `lodging\realestate\RentalUnit`, then the translation file for `realestate\RentalUnit` will be returned.

### Impact of Inheritance on Views

The inheritance system influences how views are managed in software applications. When a view is related to a class that inherits from another, the view can leverage all the properties and methods available from the ancestor classes. This allows for a more flexible and powerful user interface design, accommodating extended functionality as defined by the inheritance chain.



## ObjectManager methods

Objects manipulations are made on selections of objects, described as an array of identifiers.

All methods can either return an array (in case of success), or an integer (in case of error, the integer is an error
code).

###   create

#### Description

```php
<?php
/**
 * Creates a new instance of given class and, if given, assigns values to targeted fields.
 *
 * @param  string       $entity       Class of the object to create.
 * @param  array        $fields       Associative array mapping each field to its assigned value.
 * @param  string       $lang         Language in which to store multilang fields.
 * @param  boolean      $use_draft    If set to false, disables the re-use of outdated drafts.
 * @return integer      Identfier of the newly created object or, in case of error, the code of the error that was raised.
 */
function create($entity, $fields, $lang=null, $use_draft=true)
```

### search

Retrieve identifiers of objects matching given criterias set (domain).

#### Description
```php
<?php
/**
 * Searches for the objects that comply with the domain (series of criteria).
 *
 * @param   string     $class       Class of the objects to search for.
 * @param   array      $domain      Domain (disjunction of conjunctions) defining the criteria the objects have to match.
 * @param   array      $sort        Associative array mapping fields and orders on which result have to be sorted.
 * @param   integer    $start       The offset at which to start the segment of the list of matching objects.
 * @param   string     $limit       The maximum number of results/identifiers to return.
 *
 * @return  integer|array   Returns an array of matching objects ids.
 */
function search($class, $domain=null, $sort=['id' => 'asc'], $start='0', $limit='0', $lang=null)
```

### read

Fetches specified field values for the selected objects.

#### Description

```php
<?php
/**
 * Reads a collection of objects from a given class, based on a list of identfiers.
 *
 * @param   string     $class       Class of the objects to retrieve.
 * @param   mixed      $ids         Identifier(s) of the object(s) to retrieve (accepted types: array, integer, string).
 * @param   mixed      $fields      Name(s) of the field(s) to retrieve (accepted types: array, string).
 * @param   string     $lang        Language under which return fields values (only relevant for multilang fields).
 * @return  int|array  Resulting associative array mapping ids with objects, or error identifier.
 */
function read($entity, $ids, $fields, $lang=null)
```

#### Parameters

| **Parameter** | **Description**                                              |
| ------------- | ------------------------------------------------------------ |
| class         | Class of the objects we want to retrieve.                    |
| ids           | List of identifiers of the objects we want to retrieve.      |
| fields        | Array holding the names of the fields we want to retrieve .  |
| lang          | Language under which return fields values (only relevant for multilang fields). |

#### Returned value

Returns an associative array containing, for every object id, a sub array mapping each field to its value.  
Returns an integer (error code) if an error occurred.

### update

Sets new values for one or more object instances.

!!! note "multilang fields"
    While saving in a specific language, no test is done to check that specified fields are defined as multilang (it means that saving non-multilang fields in a non-default language will result in a loss of data).

#### Description

```php
<?php
/** 
 * Updates specified fields of seleced objects and stores changes into database.
 *
 * @param   string    $class        Class of the objects to write.
 * @param   mixed     $ids          Identifier(s) of the object(s) to update (accepted types: array, integer, numeric string).
 * @param   mixed     $fields       Array mapping fields names with the value (PHP) to which they must be set.
 * @param   string    $lang         Language under which fields have to be stored (only relevant for multilang fields).
 * @return  int|array Returns an array of updated ids, or an error identifier in case an error occured.
*/
function update($class, $ids, $fields, $lang=null)
```

#### Parameters

| **Parameter** | **Description**                                    |
| ------------- | ------------------------------------------------------------ |
| object_class  | Class of the objects we want to update.                      |
| ids           | Ids of the objects to update.                                |
| fields        | Array mapping fields names with their new values .           |
| lang          | Language to wich apply the changes (affects only multilang fields). |
| session_id          | Identifier of the session holding user data (by default, the current session). |

#### Returned value

Returns an array containing ids of newly created objects (if any).  
Returns an integer (error code) if an error occurred.

### delete

Deletes a series of object.
A flag allows to chose to permanently delete them, or to put them it in the "trash bin" (in the latter case, the object is not removed from the store, but marked as 'deleted').

#### description

```php
<?php
/**
 * Deletes an object permanently or put it in the "trash bin" (i.e. setting the 'deleted' flag to 1).
 *
 * @param   string  $class          Class of the object to delete.
 * @param   array   $ids            Array of ids of the objects to delete.
 * @param   boolean $permanent      Flag for soft deleted (marked as deleted) or hard deletion (removed from DB).
 *
 * @return  integer|array   Returns a list of ids of deleted objects, or an error identifier in case an error occured.
 */
function delete($entity, $ids, $permanent=false)
```

#### Parameters

* **class** : class of the objects to delete
* **ids** : ids of the objects to delete
* **permanent** : flag for marking deletion as soft-deletion (default) or hard-deletion. In the latter case, the record
  is removed from DB.

#### Returned value

Returns an associative array containing ids of the objects actually deleted.  
Returns an integer (error code) if an error occurred.


### search

Search for objects matching the domain criteria.

#### Description

```php
<?php
mixed search( string $class, array $domain=null, string $order='id', string $sort='asc', string $start='0', string $limit='0', string $lang=DEFAULT_LANG)
```

#### Parameters

| **Parameter** | **Description**                                              |
| ------------- | ------------------------------------------------------------ |
| class         | Class of the objects we want to look for.                    |
| domain        | Search criteria that objects have to match.                  |
| order         | Field on which the resulting list must be sorted .           |
| sort          | Sorting order.                                               |
| start         | Position in the global resulting list from which we want the ids. |
| limit         | Amount of ids to return.                                     |
| lang          | Language under which search applies (only relevant for multilang fields). |

#### Returned value

Returns an array of objects ids.  
Returns an integer (error code) if an error occurred.

### validate

Checks whether the values of a given object fields are consistent with the related model definition.

#### Description

```php
<?php
mixed validate(string $class, int[] $ids, array[] $values, boolean $check_unique=false, boolean $check_required=false)
```

#### Parameters

| **Parameter** | **Description**                                       |
| ------------- | ----------------------------------------------------- |
| class         | Class of the object we want to validate.              |
| values        | Associative array containing fields and their values. |

#### Returned value

Returns an associative array containing invalid fields with their associated error_message_id (thus an empty array means
no invalid fields).  
Returns an integer (error code) if an error occurred. 
