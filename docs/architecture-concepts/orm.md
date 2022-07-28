# ORM

An ObjectManager service is dedicated to Object-Relational Mapping. It handles all tasks that relate to Objects manipulations and offers an abstract layer for DBMS queries.

The ORM can be used in order to : 

- create, update, delete one or more objects (based on the ID field)
- retrieve a single entity or a list of entities (both based on the ID field)
- retrieve a list of IDs of entities that match some criteria



ObjectManager methods are for low-level manipulations (no data conversion nor validation, and no user permission check at this level).

They are mostly used in methods of classes definition.

Controllers mostly require high-level manipulations (including data conversion, validation and permission checks) and therefore use Collections return by class autoloader. 



## ObjectManager methods

### read
Fetches specified field values for the selected objects.

#### Description
```php
<?php
mixed read( string $object_class [, int[] $ids=null, string[] $fields=null, string $lang=DEFAULT_LANG] )
```

#### Parameters
  * **object_class** : class of the objects we want to retrieve
  * **ids** : list of identifiers of the objects we want to retrieve
  * **fields** : array holding the names of the fields we want to retrieve 
  * **lang** : language under which return fields values (only relevant for multilang fields)
  * **session_id** : identifier of the session holding user data (by default, the current session)

#### Returned value
Returns an associative array containing, for every object id, a sub array mapping each field to its value.\\ 
Returns an integer (error code) if an error occurred.



### update 
Sets new values for one or more Object instances .

!!! note "multilang fields"
	while saving in a specific language, no test is done to check that specified fields are defined as multilang (it means that saving non-multilang fields in a non-default language will result in a loss of data).

#### Description

```php
<?php
mixed update( string $object_class, int[] $ids [, array[] $values=null, string $lang=DEFAULT_LANG, boolean $create=false] )
```

#### Parameters

  * **object_class** : class of the objects we want to update
  * **ids** : ids of the objects to update
  * **values** : array maping fields names with their new values
  * **lang** : language to wich apply the changes (affects only multilang fields)
  * **session_id** : identifier of the session holding user data (by default, the current session)

#### Returned value
Returns an array containing ids of newly created objects (if any).  
Returns an integer (error code) if an error occurred.

### delete
Deletes an object permanently or puts it in the "trash bin" (i.e. setting the 'deleted' flag to 1).

#### description
```php
<?php
mixed remove( string $object_class, array $ids [, boolean $permanent=false] )
```

#### Parameters

  * **object_class** : class of the objects we want to delete
  * **ids** : ids of the objects to remove
  * **permanent** : flag indicating if deletion is permanent (record removed from DB) or not ('deleted' field set to 1)
  * **session_id** : identifier of the session holding user data (by default, the current session)

#### Returned value 
Returns an associative array containing ids of the objects actually deleted.  
Returns an integer (error code) if an error occurred.



### search

Search for objects matching the domain criteria.

#### Description

```php
<?php
mixed search( string $object_class [, array $domain=null, string $order='id', string $sort='asc', string $start='0', string $limit='0', string $lang=DEFAULT_LANG] )
```

#### Parameters

  * **object_class** : class of the objects we want to look for
  * **domain** : search criteria that objects have to match
  * **order** : field on which the resulting list must be sorted
  * **sort** : sorting order
  * **start** : position in the global resulting list from which we want the ids
  * **limit** : amount of ids to return
  * **lang** : language under which search applies (only relevant for multilang fields) 
  * **session_id** : identifier of the session holding user data (by default, the current session)

#### Returned value

Returns an array of objects ids.  
Returns an integer (error code) if an error occurred.



### validate 

Checks whether the values of given object fields are valid or not.

#### Description

```php
<?php
array validate(string $class, int[] $ids, array[] $values, boolean $check_unique=false, boolean $check_required=false)
```

#### Parameters

  * **object_class** : class of the object we want to validate
  * **values** : associative array containing fields and their values

#### Returned value

Returns an associative array containing invalid fields with their associated error_message_id (thus an empty array means no invalid fields).  
Returns an integer (error code) if an error occurred. 



### 