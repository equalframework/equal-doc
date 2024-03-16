# Packages

In order to ease development and collaboration, eQual organizes application logic into themed packages represented by
folders, located under the `/packages` directory.

These packages contain several components : models, views, controllers, and translation files. Models define data structure,
views describe user interface, controllers manage data flow, and translations support i18n features.

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

The manifest is a file containing informations about the package and its Apps:

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

| **PROPERTY**  | **ROLE**                                                     | **EXAMPLES**               |
| :-----------: | ------------------------------------------------------------ | -------------------------- |
|    `name`     | String holding the name of the package. <br />This string can be used as identifier and should match the name of the folder of the package, and should contain letters only. | `"myPackage"`              |
|   `version`   | The version of the package.                                  | `"1.0"`                    |
| `description` | Short string describing the package.                         | `"A very useful package."` |
|   `license`   | The license of the package.                                  | `"LGPL-3"`                 |
|   `authors`   | Array holding the name(s) of the author(s).                  | `["Michael Scott"]`        |
| `depends_on`  | List of packages that need to be initialized in order for the package to work. | `["core", "finance"]`      |
|    `apps`     | applications related to the package <br>You can add an App object if your package need some views. <br> The Apps `manifest.json` bellow explain it. | `[myApp]`                  |



!!!note The `apps`  property might either contain strings or descriptor objects. 
    The application `app` can indeed act as surrogate for creating custom Apps using all standard features without having to write additional Angular components. In such case, the descriptor is exepected to follow the structure defined below.



### README.md

A markdown file containing various relevant information about the package, its installation, configuration, and the features it offers.

## Apps

### manifest.json

| **PROPERTY**       | **ROLE**                                                     | **EXAMPLES**                                        |
| ------------------ | ------------------------------------------------------------ | --------------------------------------------------- |
| name               | The name of the application.                                 | `Settings`                                          |
| description        | A short description of the role and usage of the application. |                                                     |
| url                | Relative URL of the application.                             | `/settings`                                         |
| icon (optional)    | Identifier of the (material) icon for representing the application. | `settings`                                          |
| color (optional)   | Color assigned to the application (used for the button).     | `#FF9741`                                           |
| extends (optional) | String telling which application it extends from, if any.    | `app`                                               |
| access/groups      | List of groups the access to the application is restricted to. | `['users']`                                         |
| params             | Object to be relayed to the targeted App (if supported).     | `"params": { "menus" : { "left": "myApp.left" }  }` |
| show_in_apps       | Flag telling if the application must be listed in the dashboard Apps list. | default: ``false``                                  |
| tags (optional)    | List of labels for tagging of the application.               | `["equal", "core"]`                                 |