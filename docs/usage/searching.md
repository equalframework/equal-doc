# Searching

Searching is handled by the ORM, which offers a `search()` method, and can be performed either through a Collection or using the ObjectManager service. 

The `search` method returns a list of objects identifiers of the targeted class.

* that list of identifiers can then be used for reading values of the filtered objects;
* additional parameters (order, sort, start, limit) allow to refine the search behavior.

In order to search amongst existing objects, the `search` method  uses a  `domain` argument describing the search criteria.

###### 

!!! note "About domains"
    To understand or learn more about domains, please refer to the  [`domain`](../architecture-concepts/domains.md)  section.

![searching.drawio](C:\Users\Jean\Documents\searching.drawio.png)



### ORM search

In classes, searching can be invoked by calling the ObjectManager service. 

#### signature

```
public function search($class, $domain=NULL, $sort=['id' => 'asc'], $start='0', $limit='0', $lang=DEFAULT_LANG)
```



#### usage

Example:

```php
$orm->search('core\User', ['login', '=', $login])
```



### Collection search

In controllers, searching can be invoked either by calling the ObjectManager service or through a collection. 

#### signature

```
public function search(array $domain=[], array $params=[], $lang=DEFAULT_LANG)
```



#### usage
Example:

```php
<?php
use core\User;

User::search( [
				['login', 'like', '%john%'],
				['validated', '=', 'true']				
			] );
```
