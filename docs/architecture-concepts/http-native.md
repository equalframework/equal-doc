eQual complies with HTTP methods and ready to be used in a RESTful API context


To fine-tune this, developer can create as many custom controllers as necessary.

Controllers are re-usable and can be inter-dependant.





====== URL mechanism ======

URL mechanism is quite simple. The idea is to ask easyObject to perform some operation based on parameters placed in the URL.

Here is how URL must be built: 

First part is the **regular URL** (for instance ''http://www.mywebsite.com/'') with the name of the script to call (quite easy since the only entry-point of easyObject is ''index.php'' !)

Second part consists of the parameters: 
  - The main parametertells what kind of operation must be performed. 
    * //**key**// must be one of the following : 
                  * **get** some data: ''?get=...''
                  * **do** something: ''?do=...''
                  * **show** some html page: ''?show=...''
    * //**value**// specifies the name of the **package** to be invoked (must be the name of a subfolder of the 'packages' directory, for instance 'core'), as well as the **script** to be called(a package may have several scripts - stored in the subfolders 'action', 'data', or 'apps')
  - in addition, some specific parameters required be the script can also be present

An example of such URL could be : ''http://www.mywebsite.com/index.php?show=core_manage&package=blog''\\
In which case, the main entry point (index.php) will try to execute the following script : ''packages/core/apps/manage.php''.\\
\\
In addition, main config file (config.inc.php) allows to define a default package, and a default app can be defined for every package.
In other words, this allows you to make ''http://www.mywebsite.com/index.php'' redirect to your webapp
or ''http://www.mywebsite.com/index.php?show=mypackage'' redirect to ''http://www.mywebsite.com/index.php?show=mypackage_myapp''