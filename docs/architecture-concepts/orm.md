# ORM

A simple set of system-related operations allows to handle most common tasks
- retrieve a single entity
- retrieve a collection of entities matching some criteria
- create, update, delete a given entity









### user_id 
Indicates the identifier of the current user.

#### Description
```php
int user_id( [ string $session_id=SESSION_ID ] )
```

#### Parameters
  * **session_id** : the identifier of the session we are looking for (by default, the current session)

#### Returned value
Returns the identifier of the user associated with the specified session_id.

### login
Tries to log a user in with the given credentials.

#### Description
```php
bool login( string $login, string $password [, string $session_id=SESSION_ID ] )
```

Note : even when not using https, we guarantee minimum privacy
  * Only MD5 values of the password are sent from client to server. So user's password stays unknown from the admin/app developper.
  * The value that is sent is always different (i.e. : MD5 value is only valid for current session). So, one cannot grab user's password by capturing http packet.

#### Parameters
  * **login** : login/username under which we want to identify
  * **password** : locked value of the password related to the specified login 
  * **session_id** : identifier of the session holding user data (by default, the current session)

#### Returned value
Returns TRUE if login process was successful.\\ 
Returns FALSE if something wrong occured (unknown username or wrong password).

## Object methods

### validate 
Checks whether the values of given object fields are valid or not.

#### Description
```php
<?php
bool validate( string $object_class, array $values )
```

#### Parameters
  * **object_class** : class of the object we want to validate
  * **values** : associative array containing fields and their values

#### Returned value
Returns an associative array containing invalid fields with their associated error_message_id (thus an empty array means no invalid fields).\\ 
Returns an integer (error code) if an error occured. 

### get
Retrieve the specified object.

#### Description 
```php
<?php
object &get( string $object_class, int $object_id [, $session_id=SESSION_ID ] )
```
Returns an instance of the specified class holding data associated with specified identifier.\\ 
Because handling the object instance required the class to be declared this method only works in PHP
(In order to use it in other languages, it would be necessary to declare the classes in each programming language and overload the setters and getters).

#### Parameters
  * **object_class** : class of the object we want to retrieve
  * **object_id** : identifier of the object we want to retrieve
  * **session_id** : identifier of the session holding user data (by default, the current session)

#### Returned value
Returns the instance of the specified object(if user has right on it).\\ 
Returns an integer (error code) if an error occured.

### browse
Fetches specified field values for the selected objects.

#### Description
```php
<?php
mixed &browse( string $object_class [, array $ids=null, array $fields=null, string $lang=DEFAULT_LANG, string $session_id=SESSION_ID ] )
```

#### Parameters
  * **object_class** : class of the objects we want to retrieve
  * **ids** : list of identifiers of the objects we want to retrieve
  * **fields** : array holding the names of the fields we want to retrieve 
  * **lang** : language under which return fields values (only relevant for multilang fields)
  * **session_id** : identifier of the session holding user data (by default, the current session)

###Returned value ==
Returns an associative array containing, for every object id, a sub array maping each field to its value.\\ 
Returns an integer (error code) if an error occured.

### search
Search for objects matching the domain criteria.

#### Description
```php
<?php
mixed search( string $object_class [, array $domain=null, string $order='id', string $sort='asc', string $start='0', string $limit='0', string $lang=DEFAULT_LANG, string $session_id=SESSION_ID ] )
```

#### Parameters
  * **object_class** : class of the objects we want to look for
  * **domain** : search criteria that objects have to match
  * **order** : field on which resulting list must be sorted
  * **sort** : sorting order
  * **start** : position in the global resulting list from which we want the ids
  * **limit** : amount of ids to return
  * **lang** : language under which search applies (only relevant for multilang fields) 
  * **session_id** : identifier of the session holding user data (by default, the current session)

#### Returned value
Returns an array of objects ids.\\ 
Returns an integer (error code) if an error occured.





### update 
Sets the values of one or more instance(s) or creates new object(s) (if specified $object_id is 0).
Note: while saving in a specific language, no test is done to check that specified fields are defined as multilang 
(it means that saving non-multilang fields in a non-default language will result in a loss of data)

#### Description

```php
mixed update( string $object_class, $ids [, array $values=null, string $lang=DEFAULT_LANG, string $session_id=SESSION_ID ] )
```

#### Parameters

  * **object_class** : class of the objects we want to update
  * **ids** : ids of the objects to update
  * **values** : array maping fields names with their new values
  * **lang** : language to wich apply the changes (affects only multilang fields)
  * **session_id** : identifier of the session holding user data (by default, the current session)

#### Returned value
Returns an array containing ids of newly created objects (if any).\\ 
Returns an integer (error code) if an error occured.

### remove 
Deletes an object permanently or puts it in the "trash bin" (ie setting the 'deleted' flag to 1).

#### description
```php
<?php
mixed remove( string $object_class, array $ids [, boolean $permanent=false, $session_id=SESSION_ID ] )
```

#### Parameters

  * **object_class** : class of the objects we want to delete
  * **ids** : ids of the objects to remove
  * **permanent** : flag indicating if deletion is permanent (record removed from DB) or not ('deleted' field set to 1)
  * **session_id** : identifier of the session holding user data (by default, the current session)

#### Returned value 
Returns an associative array containing ids of the objects actually deleted.\\ 
Returns an integer (error code) if an error occured.