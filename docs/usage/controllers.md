## Introduction

Controllers are everywhere and refer to every action involving data manipulation.

They mainly play the role of data funnels and dispatchers, but they are also required when it comes to security: making sure every input has the permission to continue before it's being processed



## AccessController.php

Speaking of security, eQual has a built-in "control tower" called AccessController.class.php (in */lib/qinoa/access/*), granting or not the permission to perform CRUD actions depending on a few parameters. This service is called by default.

You can also overwrite AccessController to your own needs (and your own risks), this is particularly useful to establish future-proof settings, aswell as an alternative to core_permission (see [Cheat Sheet > Grant DB rights](../howtos-and-examples/generic-cheat-sheet.md)).

In the next section we'll see how to proceed

### Overwriting AccessController

In **/lib**, create a folder by the name of your project, you want a directory exactly like this: **/lib/myapp/access/AccessController.class.php**

Then, in the **config.inc.php** file of your package (located at /public/packages/myapp/config.inc.php), add this line:

```php
register('access', 'myapp\access\AccessController');
```

Finally, open you newly created AccessController.class.php and copy paste this :

```php
<?php
namespace myapp\access; // change 'myapp' with actual name
use qinoa\organic\Service;
use qinoa\services\Container;

class AccessController extends \qinoa\access\AccessController {
    
  // rewrite functions here to override their default behavior
    
  // here is a non-exhaustive example with filter:
  filter($operation, $object_class='*', $object_fields=[], $object_ids=[]){
    $user_id = $this->container->get('auth')->userId();
    // grant READ rights over 'User' class when an user is authenticated
    if($object_class == 'myapp\User') {
      if($operation == QN_R_READ) {
        if($user_id > 0) {
          return $object_ids;
        }    
      }
    }
  }
    
}
```

You're ready!

It might be confusing so don't hesitate to look back at */lib/qinoa/access/AccessController.class.php* for inspiration

