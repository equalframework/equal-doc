# ORM

eQual comes with an object-relational mapper (ORM) that greatly eases the interactions with the database by handling the
data conversions, classes inheritance and objects relations.

The ObjectManager service is dedicated to Object-Relational Mapping. It handles all tasks that relate to objects search
and manipulations and offers an abstract layer for DBMS queries.

The ORM can be used in order to :

- **create**, **update** or **delete** one or more objects (based on the ID field)
- **retrieve** a single entity or a list of entities (both based on the ID field)
- **retrieve** a list of IDs of entities that match some criteria

ObjectManager methods are for low-level manipulations (no data conversion nor validation, and no user permission check
at this level). These are mostly used in methods of classes definition.

Controllers mostly require high-level manipulations (including data conversion, validation and permission checks) and
therefore use Collections returned by class autoloader.

## Object Definition

### Object Definition in ORM

In the object-relational mapping (ORM) system, object definitions are structured using classes that inherit from the `equal\orm\Model`. This base class provides methods and properties that are common to all objects within the system.

#### Field Definitions with `getColumns()`

Each model class must implement at least one mandatory method: `getColumns()`. This method is essential as it defines the model fields.

The `getColumns()` returns an associative array, mapping field names to their definitions. This method allows for the specification of each field's characteristics, such as type, default values, constraints, and any other relevant attributes.

Below is a basic implementation of the `getColumns()` method in PHP:

```php
<?php
public static function getColumns() {
    // Returns an empty array by default
    return [];
}
```


## Classes inheritance 


Classes can extend other classes, potentially from different packages, to add new fields or customize behavior.

When a class inherits from another, objects instantiated from this class will contain not only the fields defined in the class itself but also all the fields defined in its ancestor classes. There can be multiple levels of inheritance.

All model classes inherit from the class `equal\orm\Model`.

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

### read

Fetches specified field values for the selected objects.

#### Description

```php
<?php
mixed read( string $class [, int[] $ids=null, string[] $fields=null, 
string $lang=DEFAULT_LANG] )
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
    While saving in a specific language, no test is done to check that specified fields are defined as multilang (it means
that saving non-multilang fields in a non-default language will result in a loss of data).

#### Description

```php
<?php
mixed update( string $object_class, int[] $ids [, array[] $values=null, 
string $lang=DEFAULT_LANG, boolean $create=false] )
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

Deletes an object permanently or puts it in the "trash bin" (i.e. setting the 'deleted' flag to 1).

#### description

```php
<?php
mixed remove( string $class, array $ids [, boolean $permanent=false] )
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
mixed search( string $class [, array $domain=null, string $order='id', string 
$sort='asc', string $start='0', string $limit='0', string $lang=DEFAULT_LANG] )
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
mixed validate(string $class, int[] $ids, array[] $values, 
boolean $check_unique=false, boolean $check_required=false)
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
