## Introduction

Controllers are everywhere and refer to every action involving data manipulation.

They mainly play the role of data funnels and dispatchers.

But they're also involved in rights management: making sure the user performing a request has the required permissions



## DB manipulation

### CRUD syntax

To get an overview on CREATE, READ, UPDATE, DELETE commands, see [*Building a REST API*](../howtos-and-examples/rest-api.md)

The following examples show the **minimal** syntax required to operate CRUD commands in a database

> Note: an additional "language" parameter is used for fields with multilang enabled. See [i18n](i18n.md) for more information

CREATE :

```php
MyObject::create($FIELDS);
```

READ :

```php
MyObject::search();
```

UPDATE :

```php
MyObject::ids($ID)
    	->update($FIELDS);
```

DELETE :

```php
MyObject::ids($ID)
    	->delete(true);
```

### Using limit & offset parameters

Minimal example for a **READ** request :

```php
MyObject::search( [], ["start" => $params['offset'], "limit" => $params['limit']] );
```

Alternative :

```php
MyObject::search()
		->from($params['offset'])
		->limit($params['limit']);
```