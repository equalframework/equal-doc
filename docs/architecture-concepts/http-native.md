eQual complies with HTTP methods and is ready to be used in a RESTful API context.





## URL mechanism 

URL mechanism is quite simple. The idea is to ask eQual to perform some operation based on parameters provided in the URL.

Here is how URL must be built: 

First part is the **regular URL** (for instance ''http://www.mywebsite.com/'') with the name of the script to call (the only entry-point of eQual is `index.php`).

Second part consists of the parameters:  

* The main parameter tells what kind of operation must be performed. 
    * `key` must be one of the following : 
        * **GET** some data: ''?get=...''
        * **DO** something: ''?do=...''
        * **SHOW** an App: ''?show=...''
    * `value` specifies the name of the **package** to be invoked (must be the name of a subfolder of the 'packages' directory, for instance 'core'), as well as the **script** to be called(a package may have several scripts - stored in the subfolders 'action', 'data', or 'apps')
* In addition, some specific parameters required be the script can also be present



Here is an example of such URL :  
``` 
http://www.mywebsite.com/?show=core_manage&package=blog
```

In this example, the main entry point (index.php) will try to execute the following script : `packages/core/apps/manage.php`  



In addition, the main config file (`config/config.inc.php`) allows to define a default package, and a default app can be defined for every package.  


```
<?php
define('DEFAULT_PACKAGE', 'equal');
```

In turn, each package can define a default App in its own config file (i.e. `packages/equal/config.inc.php`)
```
namespace config;
define('DEFAULT_APP', 'workbench');

```

This allows to make the root entry point  (`http://www.mywebsite.com/`) redirect to a webapp (in this exemaple:  `http://www.mywebsite.com/index.php?show=equal_workbench'`)

