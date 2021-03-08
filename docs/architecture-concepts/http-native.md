eQual complies with HTTP methods and is ready to be used in a RESTful API context.





## URL mechanism 

URL mechanism is quite simple. The idea is to ask eQual to perform some operation based on parameters provided in the URL.

Here is how URL must be built: 

First part is the **regular URL** (for instance ''http://www.mywebsite.com/'') with the name of the script to call (the only entry-point of eQual is `index.php`).

Second part consists of the parameters: 
* The main parameter tells what kind of operation must be performed. 
    * `key` must be one of the following : 
      * GET some data: ''?get=...''
      * DO something: ''?do=...''
      * SHOW an App: ''?show=...''
    * `value` specifies the name of the **package** to be invoked (must be the name of a subfolder of the 'packages' directory, for instance 'core'), as well as the **script** to be called(a package may have several scripts - stored in the subfolders 'action', 'data', or 'apps')
* In addition, some specific parameters required be the script can also be present



Here is an example of such URL :  
http://www.mywebsite.com/?show=core_manage&package=blog''  

In this example, the main entry point (index.php) will try to execute the following script : ''packages/core/apps/manage.php''.  



In addition, the main config file (`config/config.inc.php`) allows to define a default package, and a default app can be defined for every package.  

In other words, this allows you to make ''http://www.mywebsite.com/index.php'' redirect to your webapp
or ''http://www.mywebsite.com/index.php?show=mypackage'' redirect to ''http://www.mywebsite.com/index.php?show=mypackage_myapp''