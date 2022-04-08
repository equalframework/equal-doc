# Views

Views are intended to describe how to present the objects to end-users under a given context.
They are used as templates for the front-end, and are stored as JSON files within the `views` folder of their respective package.

Each of them represents a mode of visualization: form, list, kanban, chart, etc; and can be edited independently from the models they relate to. 

It is possible to define as many views (of different types, or variations with same type) as necessary. 
Each view is referenced by an ID, which is composed of its type and its name.

As a convention, a default view for `list` and `form` types should be defined for each entity.

**The generic filename format** is: `{class_name}.{view_type}.{view_name}.json`

* `class_name`: the class name of the entity the view relates to (e.g. default form view for  `core\User` is stored as `packages/core/views/User.form.default.json`)
* `view_type`: Possible values are '*list*', '*form*' and '*card*'
* `view_name`: As a convention, classes should always have a 'default' view for types 'list' and 'view'.



Here is a recap for the `core\User` entity :

| filename                          | entity    | view type | view name | view ID |
| --------------------------------- | --------- | --------- | --------- | --------- |
| `core\views\User.list.default.json` | core\User | list | default | list.default |
| `core\views\User.form.default.json` | core\User | form | default | form.default |





## Front-end logic

A **view** relates to an entity and has a type and a name. The view itself requests the corresponding data from the server (template or translation) when loading the layout at which a domain can be specified.
Within a view, a layout defines the way in which the widgets are linked to the model. The view is synchronized with the model during modifications. 

Keep in mind that if the view's class extends another class, which will be called the parent, then it should also contain all the fields from this parent class except the computed ones and the ones that are already present in this child class.

A **Model** is a collection of objects of a given entity. This class keeps the full schema of the entity with the default values that are then updated by this model after it requests the corresponding data from the server.

A **Layout** is the layout associated with a given view. It is always linked to a Model.

A **Widget** is responsible for displaying the value of an object's field (in 'view' or 'edit' mode). It synchronizes its value with the Model to which it is associated via the Layout and the View that is using it.

!!! note "About widget property"
	The <em>widget</em> property has various field options: 	
	* ```view```: to indicate which view template to use with the widget.  
	* ```header```: (boolean) to emphasise the widget. When set to true, the field is considered as header and is shown with a bigger font-size.  
	* ```readonly```: (boolean). When set to true, the field is displayed as read-only (can't be changed).  
	For one2many and many2many field, it is also possible: 	
	* to specify the order and limit the loaded lines shown as autocomplete  
	* to force using a non-default x2many widget



## Views commons
Some attributes are common to all types of views. Below is a list of the common attributes and their role.


### name

### description

### access

groups: array (list of groups the view is restricted to)

Example : 

```
    "access": ['root']
```

### actions <a id="view_commons_actions"></a>
The **actions**  property  contains a list of objects defining the actions that are attached to the view.

Each action item  relates to a button, show in the header, which, when clicked, will relay a request to a given controller. Once the action has been performed, the view is automatically refreshed.

| <u>Property</u> | <u>Description</u>                                           |
| --------------- | ------------------------------------------------------------ |
| **id**          | Identifier of the action for translation purpose (can be set in the i18n related file). |
| **description** | The description that is displayed to the user when (s)he clicks on the related button. |
| **label**       | Label assigned to the view.                                  |
| **controller**  | Controller to invoke when the user confirms the action. By default, the `id` of the current object is sent as a parameter. |
| **visible**     | (optional) Domain (array) of conditions to meet in order to make the action button visible. Example: `"visible": ["status", "=", "quote"]` |
| **confirm**     | (optional) If set to true, a confirmation dialog is displayed before relaying the request to the controller. |
| **params**      | (optional) Associative array mapping fields with their values. Values can be assigned by referencing a property of the current user (e.g. `user.login`) or current object (for form views). |

Example:

```json
"actions": [
  {
    "id": "action.option",
    "label": "Set as Option",
    "description": "Rental units will be blocked but no funding will be claimed yet.",
    "controller": "lodging_booking_option",
    "visible": ["status", "=", "quote"],
    "confirm": true,
    "params": {
        "id": "object.id"
    }
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



If target controller requires one or more parameter, the view will generate a dialog asking the user for the values to be assigned to each parameter.

If no parameter is required but the `confirm` property is set to `true`, then a confirmation dialog is displayed before performing the action.

Here below is a flow diagram that recaps the interactions between the controller and the `confirm` property.


![](https://files.yesbabylon.org/document/2196871ef46f435e9e899287fe4c1256)




### header <a id="view_commons_header"></a>

The **header** section allows to override the actions buttons shown in the left part of the View header.

It can be used to force action buttons visibility, to define the order of the actions for buttons having multiple actions ("split buttons"), and to override the configuration of the subsequent Views (for relational fields).

* For **forms**, default actions are : `ACTION.EDIT`, `ACTION.SAVE`, `ACTION.CREATE`, `ACTION.CANCEL`
* For **lists**, default actions are : `ACTION.SELECT`, `ACTION.CREATE`

Action items are either booleans or arrays items describing the order of the buttons and parameters for subsequent views. 

Items set to false mean that the action is not available for the View.



| <u>Action</u>    | <u>Description</u>    | <u>IDs</u>|
| ---- | ---- |---- |
| **ACTION.EDIT** | For forms in view mode, allows to edit the current object. ||
| **ACTION.SAVE** | For forms in edit mode, allows to save the current object. |`SAVE_AND_CLOSE`, `SAVE_AND_VIEW`, `SAVE_AND_CONTINUE`|
| **ACTION.CREATE** | For all views, allows to create a new current object. |`CREATE`, `ADD`|
| **ACTION.CANCEL** | For forms in edit mode, allows to cancel the changes made to the current object. |`CANCEL_AND_CLOSE`, `CANCEL_AND_VIEW`|
| **ACTION.SELECT** | For lists, allows to relay current selection to parent View. ||



**Usage example**: 

```
    "header": {
        "actions": {
    	    "ACTION.CREATE": [
                {
                    "view": "form.create",
                    "description": "Overload form to use for objects creation.",
                    "domain": ["parent_status", "=", "object.status"]
                }
            ],
			"ACTION.SELECT": false,
	        "ACTION.SAVE": [ {"id": "SAVE_AND_CONTINUE"}, {"id": "SAVE_AND_CLOSE"} ],
        	"ACTION.CANCEL": [ {"id": CANCEL_AND_VIEW"}]
        }
    }
```



## Form views

**Forms** allow to view and edit individual objects. It is possible to define as many views as desired, and a given entity should always have default form view (`{entity}.form.default.json`). 

Forms views are JSON objects that describe how to render a specific view related to a given entity.

### Minimal example

Example.form.default.json
```json
{
  "name": "Example",
  "description": "Simple form for displaying a basic objects",
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
                          "width": "50%"
                      },
                      {
                          "type": "field",
                          "value": "name",
                          "width": "50%"
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

| <u>Property</u> | <u>Description</u>                                           |
| ----------- | ------------------------------------------------------------ |
| **name**    | The **name** property is mandatory and relates to the unique name assigned to the view. |
| **description** | A **description** property allows to give a short hint about the way the view is intended to be used. |
|**header**|This section allows to override action buttons that are displayed in the header of the form.|
|**actions**|(optional) A list of actions associated to a view. If set, visible actions (see below) will be shown in the right-part of the header.|



#### header

The **header** property is common to all views. For details about its structure see <a href="#view_commons_header">views commons</a>.



#### actions

The **action** property is common to all views. For details about its structure see <a href="#view_commons_actions">views commons</a>.



#### layout

The layout part holds a nested structure that describes the way the (form) view has to be rendered (which fields, using which widgets) and how to place its elements, by grouping fields within rows and columns.



#### layout.groups

The groups are stacked vertically. A layout must always have at least 1 group.

|<u>Property</u>|<u>Description</u>|
|--|--|
|**label**|name of the group|
|**sections**|Array of sections objects. A group must always have at least 1 section.|



#### group.sections

A group must always have at least 1 section.

When several sections are present, each section is displayed under a tabs.

|<u>Property</u>|<u>Description</u>|
|--|--|
|**label**|(optional) Label (en) of the section. The label of a section is only displayed when there are several sections.|
|**id**|(optional) identifier for mapping the section in translation files|
|**visible**|(optional) a domain conditioning the visibility of the section and its tab (ex. `["status", "not in", ["quote", "option"]]`)|
|**rows**|Array of rows objects. A section must always have at least 1 row.|



#### section.rows

|<u>Property</u>|<u>Description</u>|
|--|--|
|**columns**|An array of columns objects that should be displayed within the row.|



#### row.columns

|<u>Property</u>|<u>Description</u>|
|--|--|
|**width**|Width of the column, as percentage of the width of the parent row (ex.: "25%").|
|**items**|An array of items that should be displayer within the column.|



#### column.items

Each column has a list of items, which are element describing which fields are to be rendered, how to render them (room within the column, widget override, ...), and under what conditions they must be displayed.

Each item is an object accepting the following properties : 

|<u>Property</u>|<u>Description</u>|
|--|--|
|**label**|(optional) Default label|
|**type**||
|**value**||
|**width**|width, in percentage of the parent column width.|
|**visible**|(optional) either a boolean (true, false) or a domain (ex. `["is_complete", "=", true]` )|
|**domain**|(optional) (ex. `["type", "<>", "I"]`)|
|**widget**|(optional) additional settings to apply on the widget that holds the fields|



#### item.widget

Within item`objects`, the widget property allows to refine the configuration of the widget (i.e. how the widget has to be rendered within the view).

|<u>Property</u>|<u>Description</u>|
|--|--|
|**heading**|(optional) if set to true, the widget is emphasized|
|**readonly**|(optional) if set to true, the value cannot be modified (marked as disabled in edit mode). If the readonly property is set to true in the schema, it cannot be overriden.|

Some additional properties apply only to specific field types. Here is the full list of the available options by type of field:

|field type|property||
|-|-|-|
|`many2many`, `one2many`|||
||**show_actions**|(optional) when set to false, header actions buttons are not displayed.|
|| **header**       |(optional) The widget can override the configuration that will be relayed to the subsequent View for M2M and O2M fields. For details about **header** structure see <a href="#view_commons_header">views commons</a>.|
||**view**|(optional) ID of the view to use for subobjects. For forms, default is "form.default", and for lists, default is "list.default" (ex.: "form.create").|
||**domain**||
|`many2one`|||
||**order**||
||**limit**||
||**domain**||

**Example:**

```
"widget": {
	"header": {
		"actions": {
			"ACTION.SELECT": true,
			"ACTION.CREATE": false        
		}
	}
}
```





!!! Note
	When an `usage` property is set in the schema of the entity, the widget is adapted accordingly. For example, when a field has its **type** set as `float` and its **usage** set to `amount/percent`, in view mode, it is displayed as an integer value followed by a '%' sign (e.g: 0.01 is converted to "'1%'').



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

The **filter** property allows to provide a series of predefined search filters   

```json
"filters": [
    {
        "id": "lang.french",
        "label": "French",
        "description": "French speaking people",
        "clause": ["language", "=", "fr"] 
    }
]
```

#### header
The **header** property is common to all views. For details about its structure see <a href="#view_commons_header">views commons</a>.



#### actions

The **action** property is common to all views. For details about its structure see <a href="#view_commons_actions">views commons</a>.



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

The view property points to an HTML file that will be parsed and filled with selected object values before being converted to PDF.  



#### layout

The layout part holds an structure that describes the way the (list) view has to be rendered (which fields, using which widgets) and how to order its elements, group them or apply operations on them.



#### layout.items

The list view consists of a table having a series of columns (items). Each column relates to a field, and is described by an item that specifies how the field is to be rendered, the behaviors attached to it (ordering, sorting, ...), and under what conditions it must be displayed.

Each item is an object accepting the following properties : 

| property | description                                                  |
| -------- | ------------------------------------------------------------ |
| label    | (optional) Default label                                     |
| type     | always 'field'                                               |
| value    | the name of the field                                        |
| width    | column width, in percentage of the list width.               |
| visible  | (optional) either a boolean (true, false) or a domain (ex. `["is_complete", "=", true]` ) |
| sortable | (optional) boolean to mark the column related to the field as sortable. |



#### operations

This property allows to apply a series of operations on one or more columns, for the displayed records set.

```
"operations": {
  "total" : {
    "operation": ["+", "object.price"],
	"usage": "amount/money:2"
  },
  "average" : {
    "operation": "AVG",
    "field": "price"
	"usage": "amount/money:2"
  }  
}
```

Operation Syntax : 

```
[ OPERATOR, FIELD ]
```
OR
```
[ OPERATOR, {FIELD | OPERATION}, {FIELD | OPERATION} ]
```

Supported shortcuts are : 'SUM', 'MIN', 'MAX', 'AVG', 'COUNT'



|Shortcut|Operation syntax|
|--|--|
|SUM|['+', object.field]|
|AVG|['/', ['+', object.field], ['#', object.field]]|
|COUNT|['#', object.field]|
|MIN|['min', object.field]|
|MAX|['max', object.field]|



## Print views



## Menu Views

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



## Routes



## Checks
