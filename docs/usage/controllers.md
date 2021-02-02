## Introduction

Controllers are everywhere and refer to every action involving data manipulation.

They mainly play the role of data funnels and dispatchers, but they are also required when it comes to security: making sure every input has the permission to continue before it's being processed



## AccessController.php

Speaking of security, eQual has a built-in "control tower" called AccessController.class.php (in */lib/qinoa/access/*), granting or not the permission to perform CRUD actions depending on a few parameters. This service is called by default.

You can also overwrite AccessController to your own needs (and your own risks), this is particularly useful to establish future-proof settings, aswell as an alternative to core_permission (see [Cheat Sheet > Grant DB rights](../howtos-and-examples/generic-cheat-sheet.md/#grant-db-rights)).

In the next section we'll see how to proceed

### Overwriting AccessController

In **/lib**, create a folder by the name of your project, you want a directory exactly like this: **/lib/myapp/access/AccessController.class.php**

Then, in the **config.inc.php** file of your package (located at /public/packages/myapp/config.inc.php), add this line:

```php
register('access', 'myapp\access\AccessController');
```

Finally, find the original AccessController.php in /lib/qinoa/access/ and copy paste its content in the new one

You're done! From there you can apply your own rules and filters within the functions

Some examples:

- isAllowed() is used to grant CRUD permissions
- filter() is a way to filter only the data an user has the rights for



