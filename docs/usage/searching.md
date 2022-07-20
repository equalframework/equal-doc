# Searching

Searching can be performed either through a Collection or using the ORM service. 

Both methods use a `domain` argument to search amongst existing objects.


## Domains

A Domain is a descriptor of a series of conditions to use for filtering the objects.

Domains are widely used in controllers and views. In both cases the logic is the same.



That method uses a `domain` argument for filtering the objects.



Example:

```php
use core\User;

User::search( [
				['login', 'like', '%john%'],
				['validated', '=', 'true']				
			] );
```

### Domain syntax

The domain syntax is : 

``` 
[ [ [operand, operator, value], {...} ], {...} ]
```



A domain is a list of one or more clauses, each clause consisting of one or more conditions.

In other words, a domain is a list of disjunctions (OR) of conjunctions (AND). 

The minimal domain is a single clause with a single condition.

* accepted operators are : '=', '<', '>',' <=', '>=', '<>', 'like' (case-sensitive), 'ilike' (case-insensitive), 'in', 'contains'

* example : array( array( array('title', 'like', '%foo%'), array('id', 'in', array(1,2,18)) ) )

* Other example : http://equal.local/?get=model_search&entity=core\User&domain=[[[id,=,1],[firstname,=,Thomas]]]

  

There are a few other parameters : order, sort, start & limit. 



### ORM search

In classes and controllers, searching is handled by the ORM (ObjectManager class) and can be invoked by calling the `ObjectManager::search()` method either directly or through a collection. 




### Collection search

Example:

```php
use core\User;

User::search( [
				['login', 'like', '%john%'],
				['validated', '=', 'true']				
			] );
```
