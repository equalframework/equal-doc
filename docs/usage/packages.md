# Packages

In order to ease development and collaboration, eQual organizes application logic into themed packages represented by
folders, located under the `/packages` directory.

These packages contain components like models, views, controllers, and translation files. Models define data structure,
views describe user interface, controllers manage data flow, and translations support multilingual features.

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

| **FOLDER** | **ROLE**                                        | **EXAMPLES**                                                               |
|:----------:|-------------------------------------------------|----------------------------------------------------------------------------|
| `classes`  | model                                           | `core\User.class.php`, `core\Group.class.php`, `core\Permission.class.php` |
| `actions`  | action handler (controller)                     | `core_manage`, `core_utils`                                                |
|   `apps`   | applications related to the package             | `auth`, `apps`                                                             |
|   `data`   | data provider                                   | `core_model_read`, `core_config_packages`                                  |
|   `test`   | test units                                      | `default.php`                                                              |
|   `init`   | initialize the package (entities, data, routes) | `core_Group.json`                                                          |
|  `views`   | templates                                       | `User.form.default.json`, `User.list.default.json`                         |
|   `i18n`   | translations                                    | `User.json`                                                                |
|  `assets`  | static content (resources)                      | js scripts, stylesheets, fonts, images                                     |

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

| **PROPERTY**  | **ROLE**                                                                                                                                                               | **EXAMPLES**               |
|:-------------:|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|
|    `name`     | String holding the name of the package. This string can be used as identifier and should match the name of the folder of the package, and should contain letters only. | `"myPackage"`              |
|   `version`   | The version of the package.                                                                                                                                            | `"1.0"`                    |
| `description` | Short string describing the package.                                                                                                                                   | `"A very useful package."` |
|   `license`   | The license of the package.                                                                                                                                            | `"LGPL-3"`                 |
|   `authors`   | Array holding the name(s) of the author(s).                                                                                                                            | `["Michael Scott"]`        |
| `depends_on`  | Packages that need to be initialized in order for the package to work.                                                                                                 | `["core", "finance"]`      |
|    `apps`     | applications related to the package <br>You can add an App object if your package need some views. <br> The Apps manifest.json bellow explain it.                      | `[myApp]`                  |

### readme.md

A markdown file containing various relevant information about the package, things to know about installation and
configuration, and the features it offers.

## Apps

### manifest.json

| **PROPERTY**       | **ROLE**                                                                            | **EXAMPLES**                                        |
|--------------------|-------------------------------------------------------------------------------------|-----------------------------------------------------|
| name               | name of the application                                                             | `APPS_APP_SETTINGS`                                 |
| description        | description of the application                                                      |                                                     |
| url                | url of the application                                                              | `/auth`                                             |
| icon (optional)    | material icons representing the application                                         | `settings`                                          |
| color (optional)   | color attributed to the application                                                 | `#FF9741`                                           |
| extends (optional) | tell which application it extend <br> In most case you probably have to use ``app`` | `app`                                               |
| access/groups      | groups giving access to the application                                             | `['users']`                                         |
| params/menus       | Object for inserting different layout menu                                          | `"params": { "menus" : { "left": "myApp.left" }  }` |
| show_in_apps       | show the application in the apps list                                               | default: ``false``                                  |
| tags (optional)    | tags of the application                                                             | `["equal", "core"]`                                 |