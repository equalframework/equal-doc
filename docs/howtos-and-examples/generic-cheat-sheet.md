# Generic cheat-sheet

This section presents common questions along with some relevant examples.



## eQual Apps

### Console

```
http://equal.local/console.php
```

### Workbench

```
http://equal.local?show=workbench
```



## CLI commands

Should be located at the root of eQual (where *run.php* is)

#### Grant DB rights

Available rights: "create", "read", "update", "delete", "manage"

You can grant one right for one entity at a time:

```bash
./equal.run --do=group_grant --group=default --right=update --entity="core\User"
```

#### Test package consistency

This controller runs some consistency checks and works with any package:

```bash
./equal.run --do=test_package-consistency --package=core
```

#### Initiate eQual core in DB

(this step is mandatory for every new installation)

```bash
./equal.run --do=init_package --package=core
```

#### Initiate your package in DB

```bash
./equal.run --do=init_package --package=mypackage
```

#### Run package test unit

```bash
./equal.run --do=test_package --package=mypackage
```



## REST API

### GET :

Related Path :  `/packages/mypackage/data/my-controller.php`

**HTTP :**

[http://equal.local?get=mypackage_my-controller](http://equal.local?get=mypackage_my-controller)

**PHP :**

```php
run('get', 'mypackage_MyClass')
```

**CLI :**

```bash
./equal.run --get=model_collection --entity="mypackage\MyClass"
```

### DO :

Related path :  `/packages/mypackage/actions/subdir/my-action.php`

**HTTP :**

[http://equal.local?do=mypackage_subdir_my-action](http://equal.local?do=mypackage_subdir_my-action)


**PHP :**

```php
run('do', 'mypackage_myobject_action', [/* parameters */])
```

**CLI :**

```bash
./equal.run --do=model_update --entity=mypackage\MyObject --fields=[ids]=1 --fields=[name]=example
```



## Howtos 

### How to create a new object?
`	$ids = update('core\User', array(0));`

> note: new object is not fully created unless we actually update it (until then, it  is considered as a draft)
> So, previous call will generate a new object which id may be found in the returned array
> You need to modify at least one field in order for the object to be available fo further use

```php
<?php
// for instance
update('core\User', $ids, array('firstname'=>'Bart'));
$bart_id = $ids[0];

//You may also create several objects at once:
$ids = update('core\User', array(0, 0, 0), array('firstname'=>'test'));

//And then change other fields:
update('core\User', $ids, array('lastname'=>'othertest');

//And finaly browse them:
print_r(browse('core\User', $ids));
```


### How to check if a given object does exist?
```php
<?php
// count the number of items returned by the search method
if(count(search($object_class, array(array(array('id', '=', $object_id)))))) {...}
```


### How to browse all objects of a given class?
```php
<?php
// note: ensure the specified class does actually exist
$res = browse($object_class, search($object_class));
```


### How to add a clause to every condition?
```php
<?php
// example: add the (deleted = 1) clause to every condition
for($i = 0, $j = count($domain); $i < $j; ++$i)
	$domain[$i] = array_merge($domain[$i], array(array('deleted', '=', '1')));
```


### How to obtain output (json/html) from another script ?
```php
<?php
// There are 2 possibilities :

// either use a HTTP request
load_class('utils/HttpRequest');
$request = new HttpRequest(FClib::get_url(true, false).'?get=core_objects_list&object_class=School%5CTeacher&rp=20&page=1&sortname=id&sortorder=asc&domain%5B0%5D%5B0%5D%5B%5D=courses_ids&domain%5B0%5D%5B0%5D%5B%5D=contains&domain%5B0%5D%5B0%5D%5B2%5D%5B%5D=1&fields%5B%5D=id&fields%5B%5D=firstname&fields%5B%5D=lastname');
$json_data = $request->get();

// or use PHP output buffering (to prevent scope collision, remember to emebed such code into a function)
function get_include_contents($filename) {
	ob_start();	
	include($filename); // assuming  parameters required by the script being called are present in the current URL 
	return ob_get_clean();
}
$result = get_include_contents('packages/core/data/objects/list.php');
```

### How to sort the result of the browse method (without calling search method)?
```php
<?php
// $order is an array containing fields names on which we want the result set sorted 
// $result is an array returned by a call to the browse method

foreach($order as $ofield) {
	uasort($result, function ($a, $b) use($ofield){
		if ($a[$ofield] == $b[$ofield]) return 0;
		return ($a[$ofield] < $b[$ofield]) ? -1 : 1;
	});
}
```




### How to request fields from all sub-objects at once?
```php
<?php
$pages_values = $orm->read('icway\Page', $pages_ids, array('url_resolver_id'), $lang);			
$url_ids = array_map(function($a){return $a['url_resolver_id'];}, $pages_values);
$url_values = $orm->read('core\UrlResolver', $url_ids, ['human_readable_url']);
```
