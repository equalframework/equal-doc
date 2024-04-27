# Collections

Collections are widely used throughout the framework: they ease manipulations of entities (objects) and allow to execute a same operation on several objects in a single call.

In eQual, a collection is a series of partial objects (the ORM loads only requested fields) on which one or more [CRUD operations](../usage/crud.md) can be applied.

Using collections allows method chaining which makes code in controllers shorter and easier to read.

## Collection architecture
```php
<?php
use core\User;

// Initiating a query to fetch users with specific fields and nested relations
$usersCollection = User::search()->read([
    'id', 'name', 'type_id', 
    'category_id' => ['id', 'name'], 
    'groups_ids'  => ['id', 'name']
]);

// Collection objects are iterable
foreach ($usersCollection as $id => $user) {
    // Process each user object here
}
```

### Structure of the collection

Each user is represented as an array with their data organized as follows:

```php
[
    1 => [
        'id'          => {integer},
        'name'        => {string},
        'type_id'     => {integer},
        'category_id' => {Object},    // many-to-one relation as an object
        'groups_ids'  => {Collection} // one-to-many relation as a sub collection
    ]
]
```





## Manipulating collections



### Methods to manipulate the collection

#### Instantiate a collection 

In order to provide collections, eQual uses magic method on Model classes with special (reserved) methods dedicated to instantiating new collections :

* `ids()` (or `id()` to instantiate a single object collection)
* `create()`
* `search()`

#### Export result as a map or an array

`get()` is used to retrieve an associative array where keys are identifiers and values are objects or sub-collections.
`get(true)` is used to manipulate the structure in terms of pure arrays.

Examples : 
```php
$getExample = $usersCollection->get();
$getArrayExample = $usersCollection->get(true);
```

When a collection is given as an array, it consists of a associative array mapping objects ids with their related (partial) value (which is also an array).

Example:

```php
<?php
[
    5 => [ 'id' => 5, 'name' => 'Huey' ],
    6 => [ 'id' => 6, 'name' => 'Dewey' ],
    7 => [ 'id' => 7, 'name' => 'Louie' ]
]
```


#### Accessing the first element in the collection
`first()` returns a single object with possible sub-collections.
`first(true)` returns a single array with possible sub-arrays.

**Examples:** 

```
$firstObject = $usersCollection->first();
$firstArray = $usersCollection->first(true);
```




### Direct use of ORM 

`ObjectManager` service can also be used for manipulating entities.

Methods from the `ObjectManager` can be considered as "system" calls while Collections offer an additional layer to make easier dealing with user permissions, objects consistency and data adaptation.

Methods defined in Model classes (i.e. classes that inherit from `equal\orm\Model`) are always invoked by the  `ObjectManager` service, and their signature holds a reference to the service itself. 

Methods all have a similar return signature :


||**INSTANCE**|**RETURN VALUE**|**ERROR/EXCEPTION**|
|--|--|--|--|
|**ObjectManager**|dependency injection|Array|In case of error, an error is returned (and a message is sent to the Reporter). ObjectManager methods don't raise Exceptions.|
|**Collections**|magic methods|Collection|Methods raises exceptions: the process is interrupted and the exception is converted to an HTTP error.|

**Example :** 

```php
<?php
use equal\orm\ObjectManager;

$orm = ObjectManager::getInstance();
$users = $orm->read('core\User', [5,6,7], ['id', 'name']);
```
is (almost) equivalent to : 

```php
<?php
use core\User;

$users = User::ids([5,6,7])->read(['id', 'name'])->get();
```



The first snippet results in calling directly the `read()` method of the `ObjectManager`, which will be directly translated into a SQL query to fetch the requested fields.  While the second snippet also checks the current user (i.e. the user from who's the request is originating) and make sure that user has enough rights to perform the operation.

In case of error, in the first example, `$users` will hold an error code, while in the second example, an Exception will be thrown and an HTTP response with an error status will be returned.

