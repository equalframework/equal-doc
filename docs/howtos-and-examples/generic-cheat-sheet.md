# Generic cheat-sheet

This section aims to answer common questions through easy to understand examples

```[eQ]``` is always referring to your absolute root path to eQual



## eQual functionalities

### Console

```
[eQ]/console.php
```

### Workbench

```
[eQ]?show=workbench
```



## CLI commands

Should be located at the root of eQual (where *run.php* is)

#### Grant DB rights

Avalaible rights: "create", "read", "update", "delete", "manage"

You can grant only one right for one entity at a time

```bash
php run.php --do=group_grant --group=2 --right=read --entity=mypackage\MyObject
```

#### Test package consistency

It works with any package, even "core"

```bash
php run.php --do=test_package-consistency --package=mypackage
```

#### Initiate eQual core in DB

(this step is mandatory for every new database)

```bash
php run.php --do=init_package --package=core
```

#### Initiate your package in DB

```bash
php run.php --do=init_package --package=mypackage
```

#### Run package testing

```bash
php run.php --do=test_package --package=mypackage
```



## REST API

### GET :

Path example :  *../packages/mypackage/data/myobject.php*

**HTTP :**

```http
[eQ]?get=mypackage_myobject
```

**PHP :**

```php
run('get', 'mypackage_myobject')
```

**CLI :**

```bash
php run.php --get=model_collection --entity=mypackage\MyObject
```

### DO :

Path example :  *../packages/mypackage/actions/myobject/action.php*

**HTTP :**

```http
[eQ]?do=mypackage_myobject_action
```

**PHP :**

```php
run('do', 'mypackage_myobject_action', [/* parameters */])
```

**CLI :**

- Create = model_create
- Update = model_update
- Delete = model_delete

```bash
php run.php --do=model_update --entity=mypackage\MyObject --fields=['id'=>1,'name'=>'example']
```



## Howtos 

### How to create a new object?
`	$ids = update('core\User', array(0));`

> note: new object is not fully created unless we actually update it (until then, it  is considered as a draft)
> So, previous call will generate a new object which id may be found in the returned array
> You need to modify at least one field in order for the object to be available fo further use

```php
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
// count the number of items returned by the search method
if(count(search($object_class, array(array(array('id', '=', $object_id)))))) {...}
```


### How to browse all objects of a given class?
```php
// note: ensure the specified class does actually exist
$res = browse($object_class, search($object_class));
```


### How to add a clause to every condition?
```php
// example: add the (deleted = 1) clause to every condition
for($i = 0, $j = count($domain); $i < $j; ++$i)
	$domain[$i] = array_merge($domain[$i], array(array('deleted', '=', '1')));
```


### How to obtain output (json/html) from another script ? ====
```php
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
// $order is an array containing fields names on which we want the result set sorted 
// $result is an array returned by a call to the browse method

foreach($order as $ofield) {
	uasort($result, function ($a, $b) use($ofield){
		if ($a[$ofield] == $b[$ofield]) return 0;
		return ($a[$ofield] < $b[$ofield]) ? -1 : 1;
	});
}
```


### How to retrieve the current language?
```php
The current session language is stored in $_SESSION['LANG']
```


### How to override DEFAULT_LANG as default language for a specific application?
```php
// set default language
isset($_SESSION['myapp_lang']) or $_SESSION['myapp_lang'] = 'fr';

// fetch the lang parameter (you can add other params here)
$params = get_params(array('lang'=>null));

// if lang param was not in the URL, use previously chosen or default
if(is_null($params['lang'])) $params['lang'] = $_SESSION['LANG'] = $_SESSION['myapp_lang'];
// otherwise, remember the chosen language
else $_SESSION['myapp_lang'] = $params['lang'];

// from here, use $params['lang'] to retrieve current lang
```

### How to request fields from all sub-objects at once?
```php
$pages_values = &browse('icway\Page', $pages_ids, array('url_resolver_id'), $lang);			
$url_ids = array_map(function($a){return $a['url_resolver_id'];}, $pages_values);
$url_values = &browse('core\UrlResolver', $url_ids, array('human_readable_url'));
```
