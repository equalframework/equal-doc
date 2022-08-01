# CRUD manipulations

eQual implements Collections with special `Collection` objects that are instanciated based on a given model.

Collections give the ability to apply an action on multiple objects at a time while checking the permission of the current user (returned by the `Authentication` service).

To get an overview on CREATE, READ, UPDATE, DELETE commands, see [*Building a REST API*](../howtos-and-examples/rest-api.md)

The following examples show the **minimal** syntax required to operate CRUD commands in a database

> Note: an additional "language" parameter is used for fields with multilang enabled. See [i18n](i18n.md) for more information

CREATE :

```php
MyObject::create($values_map);
```

READ :

```php
MyObject::read($fields_array);
```

UPDATE :

```php
MyObject::ids($ids_array)
    	->update($values_map);
```

DELETE :

```php
MyObject::ids($ids)
    	->delete(true);
```

SEARCH:

```php
MyObject::search($search_domain);
```

**Example** :


```php
MyObject::search(['firstname', 'ilike', '%ro%'])
		->from($params['offset'])
		->limit($params['limit'])
    	->get();
```

