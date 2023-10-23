# Packages

In order to ease development and collaboration, the eQual framework's architecture organizes application logic into themed packages represented by folders, located under the `/packages` directory. 

These packages contain components like models, views, controllers, and translation files. Models define data structure, views describe user interface, controllers manage data flow, and translations support multilingual features. 


## Package structure
Each **package** is defined as follows :

```
package_name
├── classes
│   └── */*.class.php
├── actions
│   └── */*.php
├── apps
│   └── */*.php
├── data
│   └── */*.php
├── tests
│   └── */*.php
├── init
│   └── *.json
├── views
│   ├── 
│   └── *.json
├── i18n
│   └── *
│       └── *.json
├── manifest.json         
├── config.json
└── readme.md
```



| **FOLDER** | **ROLE** | **URI KEY** | **EXAMPLES**                                                                       |
|-|-|-|------------------------------------------------------------------------------------|
| `classes`  | model          |                  | `core\User.class.php`, `core\Group.class.php`, `core\Permission.class.php` |
| `actions`  | action handler (controller) | do       | core_manage, core_utils                                                            |
| `apps`     | applications related to the package |        | auth, apps                                                                         |
| `data`  | data provider | get       | core_objects_browse, core_user_lang                                                |
| `test`  | test units | do | `default.php`                                                                      |
| `init`  | initialize the package with data (**requires**:`import=true`) or routes | do | `core_Group.json`                                                                  |
| `views`  | templates |        | `User.form.default.json`, `User.list.default.json`                                 |
| `i18n`  | translations |        | `User.json`                                                                        |
| `assets` | static html |        | static content, javascripts, stylesheets, images                                   |



## Package descriptors

### manifest.json

The manifest is a file containing informations about the package and its apps:

```
{
    "name": "core",
    "description": "Foundations package holding the application logic of the elementary entities.",
    "version": "2.0",
    "authors": ["Cedric Francoys"],
    "license": "LGPL-3",
    "depends_on": [],
    "apps": [ "apps", "auth", "app", "settings" ],
    "tags": ["equal", "core"]
}
```


| **PROPERTY** | **ROLE** |  **EXAMPLES**  |
|-|-|---|
| `name` | String holding the name of the package. This string can be used as identifier and should match the name of the folder of the package, and should contain letters only. | `"myPackage"` |
| `description` | Short string describing the package. | `"A very useful package."` |
| `authors` | Array holding the name(s) of the author(s). | `["Michael Scott"]` |
| `version` | The version of the package. | `"1.0"` |
| `depends_on` | Packages that need to be initialized in order for the package to worl. | `["core", "finance"]` |
| `apps` | applications related to the package | `[myapps]` |



### Apps
#### manifest.json

| **PROPERTY** | **ROLE** |  **EXAMPLES**  |
|-|-|---|
| name** | name of the application | `APPS_APP_SETTINGS` |
| description | description of the application |  |
| url    | url of the application | `/auth` |
| icon (optional) | material icons representing the application | `settings` |
| color (optional) | color attributed to the application | `#FF9741` |
| access/groups | groups giving access to the application | `setting.default.user` |
