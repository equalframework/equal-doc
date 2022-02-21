To define the layout of the forms, the list of the fields and the possible interactions between them, we use a system of views similar to templates.

Each package has a folder named ‘views’ that contains, for each class, a series of views.  
These files are intended to describe how to present lists of objects (list) or single objects (form) under a given context (i.e. which fields to display and how to position them), and are written in JSON format.

**Generic filename format** is: `{class_name}.{view_type}.{view_name}.json`

* `class_name`: the class name of the entity the view relates to (e.g. default form view for  `core\User` is stored as `packages/core/views/User.form.default.json`)
* `view_type`: Possible values are '*list*', '*form*' and '*card*'
* `view_name`: As a convention, classes should always have a 'default' view for types 'list' and 'view'.

A **view** relates to an entity and has a type and a name. The view itself requests the corresponding data from the server (template or translation) when loading the layout at which a domain can be specified.
Within a view, a layout defines the way in which the widgets are linked to the model. The view is synchronized with the model during modifications. 

Keep in mind that if the view's class extends another class, which will be called the parent, then it should also contain all the fields from this parent class except the computed ones and the ones that are already present in this child class.

A **Model** is a collection of objects of a given entity. This class keeps the full schema of the entity with the default values that are then updated by this model after it requests the corresponding data from the server.

A **Layout** is the layout associated with a given view. It is always linked to a Model.

A **Widget** is responsible for displaying the value of an object's field (in 'view' or 'edit' mode). It synchronizes its value with the Model to which it is associated via the Layout and the View that is using it.

<em>widget</em> can have multiple field options such as: 

* ```view```: than represents the view to which this value points to.
* ```header```: is a Boolean. When it's true, the field is considered as header and will have bigger font size than the other fields.
* ```readonly```: is a Boolean. When it's true, the field can't be changed even if the update event is triggered.

When we have an x2many field the widgets: 

* allows to see the selected object (form)
* allows to select one or more other objects (list)



## Mapping between Model and View

A `class` contains a series of fields definition that relate to a specific entity (ex.: name, address, user_id,  ...)  

Each `field` definition may contain one or more of these properties: 

* `type` (mandatory): The existing types are: *alias*, *computed*, *many2one*, *many2many*, *one2many*, *integer*, *string*, *float*, *boolean*, *text*, *date* and *datetime*.
* `alias`: which represents another name that the field in known for. For example: field name is "name" so the alias would be "display_name".
* `function`: Mandatory when type is '*computed*'. It tells which method to call for computing the value of the field.
* `result_type`: Mandatory when type is '*computed*'. It specifies the type of the result of this specific field, which can be any of the mentioned typed in the `type` property.
* `store`: Applies when type is '*computed*'. It tells if the resulting value has to be stored in the DB or computed each time it is requested.
* ```default```: is the default value of the field.
* `description` (optional): is a small brief about the field.
* `onchange` (optional): calls a function to get it's value whenever a change exists.
* ```ondelete```: has 2 values, either <em>cascade</em> or <em>null</em>. 
    * <em>cascade</em> is used in the context of on delete action for the parent class, delete this field too. 
    * <em>null</em> is used to identify that when deleting the parent class, the current field shouldn't be delete with it.
* ```ondetach```: has one value which is <em>delete</em>, which is triggered when the update event for a field is happening. 
* `selection`: represents all the options in a field of a class. It's like the list of possible options in a dropdown menu.
* `visible`: It specifies the conditions that must be met in order for the field to be relevant.
* `foreign_object`: is the path to the parent class of a field in another one.
* `foreign_field`: the name of the field that refers to the parent class.
* `required`: Marks a field as mandatory (storing an object without giving a value for that field will raise an error).
* ```domain```: is a condition set for a field which implies for example that a field is equal, or in the range of, or different than another one. It is written like so: ```'domain' => ['relationship', '=', 'customer']```. This means that the specific field that has this domain must have a relationship type equal to customer.
* ```usage```: it specifies under which format the field is used. For example: 
    * markup/html
    * country/iso-3166:2 (for the country address)
    * amount/money (for the price)
    * phone, email, url
    * uri/urn:iban (for the account iban)
    * amount/percent (for the rate)
    * date/year:4 (for the year)
    * uri/urn:ean (for the ean code)
    * language/iso-639:2 (for the language abbreviation like fr, en, du...)


Below is an example of a class called Category having multiple fields for which we will then show how to write its ```Form View``` and ```List View```.

```php
<?php 
class Category extends Model {
    
    public static function getColumns() {
      return [
        'name' => [
          'type'              => 'string',
          'description'       => "Name of the category (for all variants).",
          'required'          => true
        ],

        'description' => [
          'type'              => 'string',
          'description'       => "A few details about category purpose and usage."
        ],
        
        'product_models_ids' => [ 
          'type'              => 'many2many', 
          'foreign_object'    => 'sale\catalog\ProductModel', 
          'foreign_field'     => 'categories_ids', 
          'rel_table'         => 'sale_product_rel_productmodel_category', 
          'rel_foreign_key'   => 'productmodel_id',
          'rel_local_key'     => 'category_id',
          'description'       => 'List of product models assigned to the category.'
        ]

      ];
    }
}
```






## Form views

**Forms** allow to view and edit individual objects. It is possible to define as many views as desired, and a given entity should always have default form view (`{entity}.form.default.json`). 

Forms views are JSON objects that describe the layout for viewing and editing a given entity, by grouping fields within rows and columns.

### Minimal example

Example.form.default.json
```json
{
  "name": "Example",
  "description": "Simple form for displaying Example objects",
  "actions": [],
  "layout": {
    "groups": [	
      {
        "label": "",
        "sections": [
          {
            "rows": [
              {
                "columns": [
                  {
                    "width": "50%",
                    "items": [
                      {
                          "type": "field",
                          "value": "id",
                          "width": "100%"
                      }
                    ]
                  }
                ]
              }
            ]
          }
        ]
      }
    ]
  }
}
```


### Structure



| property    | description                                                  |
| ----------- | ------------------------------------------------------------ |
| name        | The **name** property is mandatory and relates to the unique name assigned to the view. |
| description | Array of sections objects. A group must always have at least 1 section. |
|header_actions|This section allows to override the order of actions for buttons with multiple actions (split buttons) that are displayed in the header of the form.|
|actions||



#### header
This section allows to override the order of actions for buttons with multiple actions (split buttons) that are displayed in the header of the form.

Default order is as follow : 
```
    "header": {
        "actions_order": {
    	    "ACTIONS.CREATE": [],
	        "ACTIONS.SAVE": ["SAVE_AND_CLOSE", "SAVE_AND_VIEW", "SAVE_AND_CONTINUE"],
        	"ACTIONS.CANCEL": ["CANCEL_AND_CLOSE", "CANCEL_AND_VIEW"]        
        }
    },
```

#### actions
The `actions`  property  contains a list of objects defining the actions that this model can make, for example: set something as option, confirm booking, check in/out and so on all of them and the related model for each of these actions is a `.php` file and is placed in an actions folder in its corresponding package. These actions have "id", "label" and "description" that are present in the other properties and in addition, they have a "controller" which will let the action work and is written likeso, `"controller": "lodging_booking_option"`, and finally a visible property that specifies when this action is visible to the user, written as follows: `"visible": ["status", "=", "quote"]` and this example indicates that the action is visible if the status is equal to quote. 



| property    | description                                                  |
| ----------- | ------------------------------------------------------------ |
| id          | Identifier of the action for translation purpose (can be set in the i18n related file). |
| description | The description that is displayed to the user when (s)he clicks on the related button. |
| label       | Label assigned to the view.                                  |
| controller  | Controller to invoke when the user confirms the action.      |
| visible     | Domain (array) of conditions to meet in order to make the action button visible. |

Example:

```json
"actions": [
  {
    "id": "action.option",
    "label": "Set as Option",
    "description": "Rental units will be blocked but no funding will be claimed yet.",
    "controller": "lodging_booking_option",
    "visible": ["status", "=", "quote"]
  },
  {
    "id": "action.validate",
    "label": "Confirm Booking",
    "description": "Rental units will be blocked and the invoicing plan will be set up.",
    "controller": "lodging_booking_confirm",
    "visible": ["status", "in", ["quote", "option"]]
  },
  {
    "id": "action.checkin",
    "label": "Check In",
    "description": "The host has arrived: the rental units will be marked as occupied.",
    "controller": "lodging_booking_checkin",
    "visible": ["status", "=", "validated"]
  },
  {
    "id": "action.checkout",
    "label": "Check Out",
    "description": "The host is leaving: the rental units will be marked for cleaning.",
    "controller": "lodging_booking_checkout",
    "visible": ["status", "=", "checkedin"]
  },
  {
    "id": "action.invoice",
    "label": "Invoice",
    "description": "The host has left and room has been reviewed: emit the invoice.",
    "controller": "lodging_booking_invoice",
    "visible": ["status", "=", "checkedout"]
  }
]
```

#### layout


The name of form view is displayed like so: `packages/core/views/User.form.default.json`
A form is defined according to the following structure:

```json
{
  "name": "User",
  "description": "Simple form for displaying User",
  "layout": {
    "groups": [				
      {
        "label": "",
        "sections": [
          {
            "label": "",		// name of the section that will be displayed as tab
            "visible": [],	// visibilty of the section
            "rows": [		   // rows are stacked vertically
              {
                "columns": [
                  {
                    "width": "50%",			// the width is adapted according to a flex grid logic (1/12)
                    "align": "left",
                    "items": [			// within a column, items are stacked by default, or side-by-side if the specified width of the items allows
                      {
                          "type": "label",  // the labels can be either relative to a control, or independent
                          "id": "identifier",
                          "value": "identifier",
                          "width": "50%",		// the width is relative to that of the column (100% by default)
                          "align": "right"
                      },
                      {
                        "type": "field",
                        "id": "firstname",	// the identifier of a field is the name of the associated field
                        "width": "50%",
                        "widget": {
                          "header": true,  // the field is a title (scaled 1.5)
                          "view": "" ,     // in the case of a widget using a view: the view ID (the type of view is implicit in the widget, but can be forced e.g. `list.detailed`)
                          "domain": []    // in the case of a widget using a domain
                        },
                        "visible": [ 
                          ["object.field", "=", "value"]		// the visibility of a control can be conditioned (by default it is the rule of the diagram that applies, if it is defined) ...
                          ["user.field", "=", "value"],		
                        ]		// the names 'object' and 'user' are reserved and are associated with the context
                      } 
                    ]
                  }
                ]
              }
            ]
          }
        ]
      }
    ]
  }
}
```

#### layout.groups

The groups are stacked vertically. A layout must always have at least 1 group.

|property|description|
|--|--|
|label|name of the group|
|sections|Array of sections objects. A group must always have at least 1 section.|



#### group.sections

A group must always have at least 1 section.

When several sections are present, each section is displayed under a tabs.

|property|description|
|--|--|
|label|(optional) Label (en) of the section. The label of a section is only displayed when there are several sections.|
|id|(optional) the id is used to override the label in translation files|
|rows|Array of rows objects. A section must always have at least 1 row.|

#### section.rows

|property|description|
|--|--|
|columns|An array of columns objects that should be displayed within the row.|


#### row.columns

|property|description|
|--|--|
|width|Width of the column (in %). Ex.: "25%"|
|items|An array of items that should be displayer within the column.|



#### column.items

Each item of there's a `type` which is usually a label, an `id`, `value` which is the name of the field present in the class, `label` to display what we want the name of the field to be, `width` which is how much the field is going to take from the page, and finally `widget` that can be set to true and shows the field in bigger font, which makes it the most important field of the view.

|property|description|
|--|--|
|label|(optional)|
|type||
|value||
|width|width relative to parent column, in %|
|visible|(optional) either a boolean (true, false) or a domain (ex. ["is_complete", "=", true] )|
|domain|["type", "<>", "I"]|
|widget|(optional) additional settings to apply on the widget that holds the fields|

#### item.widget
|property|description|
|--|--|
|header|(optional) if set to true, the widget is emphasized|
|readonly|(optional) if set to true, the value cannot be modified (mark as disabled in edit mode)|
|action_select|(optional) force the display (true) of the 'select' button for M2M and O2M fields|
|action_create|(optional) force the display (true) of the 'create' button for M2M and O2M fields|
|view|(optional) ID of the view to use for subobjects. For forms, default is "form.default", and for lists, default is "list.default". (ex.: "form.create")|



### Real life example

A real example of a form view is shown below, which is the Category form of a package having multiple `sections` (tabs) each having a label(Categories, Product Models and Booking Types) and an id(`section.categories_id`, `section.product_models`, `section.booking_types`) to be able to be translated in using the "i18n". The field called `name` has a `widget` property with an attribute `header` set to true which makes it have a bigger font as mentioned earlier and the most important field of this view.

The view's name is `Category.form.default.json` and is as follows:

```json
{
  "name": "Category",
  "description": "Categories are not related to Families and allow a different way of grouping Products.",
  "layout": {
    "groups": [
      {
        "sections": [
          {
            "label": "Categories",
            "id": "section.categories_id",
            "rows": [
              {
                "columns": [
                  {
                    "width": "50%",
                    "items": [
                      {
                        "type": "field",
                        "value": "name",
                        "width": "100%",
                        "widget": {
                            "header": true
                        }
                      },
                      {
                        "type": "field",
                        "value": "description",
                        "width": "100%"
                      }
                    ]
                  }
                ]
              }
            ]
          },
          {
            "label": "Product Models",
            "id": "section.product_models",
            "rows": [
              {
                "columns": [
                  {
                    "width": "100%",
                    "items": [
                      {
                        "type": "field",
                        "value": "product_models_ids",
                        "width": "100%"
                      }
                    ]
                  }
                ]
              }
            ]
          },
          {
            "label": "Booking Types",
            "id": "section.booking_types",
            "rows": [
              {
                "columns": [
                  {
                    "width": "100%",
                    "items": [
                      {
                          "type": "field",
                          "value": "booking_types_ids",
                          "width": "100%"
                      }
                    ]
                  }
                ]
              }
            ]
          }
        ]
      }
    ]
  }
}
```



## List views

List views are used to display collections of items. It contains the same properties mentioned in the ```Form View``` section, such as `name`, `description`, `layout` and a few additional ones. Some of the additional properties that are specific for the list views are `filters`, `pager` for displaying in navigation bar, `selection_actions` which allows the modification of an object in the list or exporting and printing it. ```sortable``` property could also be added to the list view, which enables the action of sorting the list when it has the value "true" since it's of type Boolean.
By clicking on one row in the list, it redirects you to the editable form related to the view. 



### Minimal example

The name of file is displayed like so: `packages/core/views/User.list.default.json`
A list view is defined according to the following structure:

```json
{
  "name": "",
  "description": "",
  "domain": [],			// syntax can be either ["id", ">", "3"] or "['id', '>', '3']"
  "filters": [
    {
      "id": "lang.french",
      "label": "français",
      "description": "Users that prefers french",
      "clause": ["language", "=", "fr"] 
    }
  ],
  "layout": {
    "filter": "bool",
    "pager": "bool",
    "selection_actions": [
      {
        "label": "export",
        "action": "products_export"
      }
    ],
    "items": [
      {
        "id": "firstname",
        "type": "field",
        "label": "First Name",
        "width": "10%",
        "widget": {
          "type": "",
          "view": "",
          "domain": ""
        }
      }
    ]
  }
}
```

The list view of the Category form mentioned in the above section contains the `name` of the list which is Categories, a `description` and the main fields to be displayed for quick use like the "name" and the "description" of the categories.
The list view is named *Category.list.default.json* and has the following structure:

```json
{
  "name": "Categories",
  "description": "This view is intended for displaying the list of categories.",
  "layout": {
    "items": [
      {
        "type": "field",
        "value": "name",
        "width": "15%"
      },
      {
        "type": "field",
        "value": "description",
        "width": "25%"
      }
    ]
  }
}
```

### Structure

#### name

#### description

#### order
String holding the name(s) of the field to sort results on, separated with commas.
Example : 

```
    "order": "sku,product_model_id"
```
#### sort

String litteral ('*desc*' or '*asc*')

Example:
```
    "sort": "asc"
```

#### limit
integer (max size of result set)

Example : 

```
    "limit": 100
```

Bear in mind that the default controller (core_mode_collect), has a `max` constraint of `500` for this parameter.

#### domain

#### filters


#### actions

```actions``` displayed in a list is an array of objects that contain 3 main fields which are: the action ```id```, the ```view``` form to which it relates to and the ```description``` of this action. It is displayed like so: 

```json
"actions": [
        {
            "id": "ACTIONS.CREATE",
            "view": "form.create",
            "description": "overload form to use for booking creation"
        }
    ]
```

#### exports

Printing a document such as a contract can be done in the `list` view. Multiple fields will have to be added such as the `id` of the contract, the `label`, the `icon` of the printer also known as "print" is added. Also, a small `description`, a `controller` having the value "model_export-print" used to trigger the printing action, the `view` which corresponds to the specific view "print.default" and finally `visible` field should be displayed as well. 

All these fields are added inside of the <em>exports</em> section of list view, which is an array of objects that has the below structure:

```json
"exports": [
        {
            "id": "export.print.contract",
            "label": "Print contract",
            "icon": "print",
            "description": "Print contract related to the booking.",
            "controller": "model_export-print",
            "view": "print.default",
            "visible": ["status", "=", "quote"]
        }
    ]
```

The view value "print.default" points to the view having the format of .html and is used to design the contract that will be printed. This html file will contain the information about the customer that is booking as well as the company that is hosting them. 

#### layout



#### layout.items



#### operations

This property allows to apply a series of operations on one or more columns, for the displayed records set.

```
"operations": {
  "total" : {
	"price": {
		"type": "SUM",
		"usage": "amount/money:2"
	}
  }
}
```

Supported operations are : 'SUM', 'MIN', 'MAX', 'AVG', 'COUNT'


## Print views



## Menus

Menus are defined by App and are injected into the side bars (navigation drawer).

Below is an example of a Booking Menu drawer, as shown by the `name` property. The `layout` is how the items are going to be displayed. 

Then we have the `items` property that contains all the fields with their additional properties like `id` which will also be used to translate this field, `description`, `icon` related to the field label, `type` is either parent or entry which will contain one or more children that you can view once clicked on this parent. 

Then we have the `children` related too the parent containing all the fields already mentioned, as well as additional ones like the `context` that can itself hold multiple fields like `entity` with is the class name of the that this child belongs to, also a `view` field which represents the view type like "form.default" and "list.default", a `purpose` field which as its name show is the purpose of this child like "create" to create new data. `order` and `sort` fields can also be present, and are usually added for list views to respectively order the list by "id" for example and sort it either in "desc" (descending order) or "asc" (ascending order). 

In the example shown below, one parent menu item is present named "New Booking" and it contains two children, "New Booking" to create a new booking and "All Bookings" that displays the list of all the bookings ordered by id and sorted in descending order.

```json
{
  "name": "Booking menu",
  "layout": {
    "items": [
      {
        "id": "item.bookings",
        "label": "Bookings",
        "description": "",
        "icon": "menu_book",
        "type": "parent",
        "children": [
          {
            "id": "item.new_booking",
            "type": "entry",
            "label": "New booking",
            "description": "",
            "icon": "add",
            "context": {
              "entity": "lodging\\sale\\booking\\Booking",
              "view": "form.default",
              "purpose": "create"
            }
          },
          {
            "id": "item.all_booking",
            "type": "entry",
            "label": "All bookings",
            "description": "",
            "context": {
              "entity": "lodging\\sale\\booking\\Booking",
              "view": "list.default",
              "order": "id",
              "sort": "desc"
            }
          }
        ]
      }
    ]
  }
}
```

