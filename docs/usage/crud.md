# Manipulating entities

eQual implements Collections with special `Collection` objects that are instanciated based on a given model.

Collections give the ability to apply an action on multiple objects at a time while checking the permission of the current user (returned by the `Authentication` service).

To get an overview on CREATE, READ, UPDATE, DELETE commands, see [*Building a REST API*](../howtos-and-examples/rest-api.md).

The following examples show the minimal syntax required to operate CRUD manipulations:

!!! note "multilang fields"
	For fields whose value varies from one language to another (multilang), an additional `$lang` parameter is used. See [i18n](i18n.md) for more information.

## CRUD standard

### Create

```php
<?php
User::create($values_map);
```

### Read

```php
<?php
User::read($fields_array);
```

### Update

```php
<?php
User::ids($ids_array)
    ->update($values_map);
```

### Delete

```php
<?php
User::ids($ids)
    ->delete(true);
```

### Search

```php
<?php
User::search($search_domain);
```

Example :


```php
<?php
use core\User;

User::search(['firstname', 'ilike', '%sam%'])
        ->from(0)
        ->limit(5)
        ->get();
```



## Collect

In addition to classic CRUD operations, eQual offers a handy `collect()` method.
The purpose of that method is to perform a `search()` and a `read()` in a single call.



The collect operation is available using the Collections or using the standard controller `core_model_collect`.



### Querying sub-objects

```
// a) dot notation
$ ./equal.run --get=model_collect --entity=core\\User --fields=[id,name,groups_ids.name,groups_ids.description]

// b) array notation
$ ./equal.run --get=model_collect --entity=core\\User --fields="{id,name,groups_ids:{name,description}}" 
```

