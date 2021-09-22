To define the layout of the forms, the list of the fields and the possible interactions between them, we use a system of views similar to templates.

Each package has a folder named ‘views’ that contains, for each class, a series of views.  
These files are intended to describe how to present lists of objects (list) or single objects (form) under a given context (i.e. which fields to display and how to position them), and are written in JSON format.

**Generic filename format** is: `{class_name}.{view_type}.{view_name}.json`

* `class_name`: the class name of the entity the view relates to (e.g. view related to core\User are stored as `packages/core/views/User.form.default.json` and `packages/core/views/User.form.default.json`)
* `view_type`: Possible values are '*list*', '*form*' and '*card*'
* `view_name`: As a convention, classes should always have a 'default' view for types 'list' and 'view'.

A **view** relates to an entity and has a type and a name. The view itself requests the corresponding data from the server (template or translation) when loading the layout at which a domain can be specified.
Within a view, a layout defines the way in which the widgets are linked to the model. The view is synchronized with the model during modifications. 

Keep in mind that if the view's class extends another class, which will be called the parent, then it should also contain all the fields from this parent class except the computed ones and the ones that are already present in this child class.

A **Model** is a collection of objects of a given entity. This class keeps the full schema of the entity with the default values that are then updated by this model after it requests the corresponding data from the server.

A **Layout** is the layout associated with a given view. It is always linked to a Model.

A **Widget** is responsible for displaying the value of an object's field (in 'view' or 'edit' mode). It synchronizes its value with the Model to which it is associated via the Layout and the View that is using it.
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
* `description` (optional): is a small brief about the field.
* `onchange` (optional): calls a function to get it's value whenever a change exists.
* `selection`: represents all the options in a field of a class. It's like the list of possible options in a dropdown menu.
* `visible`: It specifies the conditions that must be met in order for the field to be relevant.
* `foreign_object`: is the path to the parent class of a field in another one.
* `foreign_field`: the name of the field that refers to the parent class.
* `required`: Marks a field as mandatory (storing an object without giving a value for that field will raise an error).


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
            ],

            'booking_types_ids' => [ 
                'type'              => 'many2many', 
                'foreign_object'    => 'sale\booking\BookingType', 
                'foreign_field'     => 'product_categories_ids', 
                'rel_table'         => 'sale_rel_productcategory_bookingtype', 
                'rel_local_key'     => 'productcategory_id',
                'rel_foreign_key'   => 'bookingtype_id',
                'description'       => 'List of booking types assigned to the category.'
            ]            
        ];
    }
}
```



## Menus

Menus are defined by App and are injected into the side bars (navigation drawer).

Below is an example of a Booking Menu drawer, as shown by the ```name``` property. The ```layout``` is how the items are going to be displayed. Then we have the ```items``` property that contains all the fields with their additional properties like `id` which will also be used to translate this field, `description`, `icon` related to the field label, `type` is either parent or entry which will contain one or more children that you can view once clicked on this parent. Then we have the `children` related too the parent containing all the fields already mentioned, as well as additional ones like the `context` that can itself hold multiple fields like `entity` with is the class name of the that this child belongs to, also a `view` field which represents the view type like "form.default" and "list.default", a `purpose` field which as its name show is the purpose of this child like "create" to create new data. `order` and `sort` fields can also be present, and are usually added for list views to respectively order the list by "id" for example and sort it either in "desc" (descending order) or "asc" (ascending order). In the example shown below, one parent menu item is present named "New Booking" and it contains two children, "New Booking" to create a new booking and "All Bookings" that displays the list of all the bookings ordered by id and sorted in descending order.

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
                    }
        		]
            }
        ]
    }
}
```

Some of the additional properties that can be added to a menu are: 
* `domain`: ""
* `sort`: ""
* `order`: '*desc*' or '*asc*'
* `limit`: ""



## Forms

Forms are the view and edit view for individual objects. It is possible to define as many views as desired, the only constraint is the definition of a default view. This view should contain all the fields present in its corresponding class, except for the fields that are of type computed. 
The most used properties of a form view are `name`, `description`, `layout`, `groups`, `sections`, `rows` and `columns` ,  which all just describe the view and design the layout for it by grouping it and assigning the rows and columns. Then for each item of there's a `type` which is usually a label, an `id`, `value` which is the name of the field present in the class, `label` to display what we want the name of the field to be, `width` which is how much the field is going to take from the page, and finally `widget` that can be set to true and shows the field in bigger font, which makes it the most important field of the view.

A property `actions` could also be added to the form which will contain a list of objects defining the actions that this model can make, for example: set something as option, confirm booking, check in/out and so on all of them and the related model for each of these actions is a .php file and is placed in an actions folder in its corresponding package. These actions have "id", "label" and "description" that are present in the other properties and in addition, they have a "controller" which will let the action work and is written likeso, `"controller": "lodging_booking_option"`, and finally a visible property that specifies when this action is visible to the user, written as follows: `"visible": ["status", "=", "quote"]` and this example indicates that the action is visible if the status is equal to quote. 

Below is how actions are displayed in a form view:

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





The name of form view is displayed like so: `packages/core/views/User.form.default.json`
A form is defined according to the following structure:

```json
{
    "name": "User",
    "description": "Simple form for displaying User",
    "layout": {
        "groups": [				// the groups are stacked vertically (there is always at least 1 group)
            {
                "label": "",			// name of the group
                "sections": [                   // if several sections, display with tabs (there is always 1 													section at least)
                    {
                        "label": "",		// name of the section that will be displayed as tab
						"visible": [],		// visibilty of the section
                        "rows": [		// rows are stacked vertically
                            {
                                "columns": [
                                    {
                                        "width": "50%",			// the width is adapted according to a flex 																grid logic (1/12)
                                        "align": "left",
                                        "items": [			// within a column, items are stacked by 																	default, or side-by-side if the specified width 															of the items allows
                                            {
                                                "type": "label",  // the labels can be either relative to a 																	control, or independent
												"id": "identifier",
                                                "value": "identifier",
                                                "width": "50%",		// the width is relative to that of the 																	column (100% by default)
                                                "align": "right"
                                            },
                                            {
                                                "type": "field",
                                                "id": "firstname",	// the identifier of a field is the name 																	of the associated field
                                                "width": "50%",
                                                "widget": {
													"header": true,  // the field is a title (scaled 1.5)
	                                            	"view": "" ,     // in the case of a widget using a 																			view: the view ID (the type of view 																		is implicit in the widget, but can 																			be forced e.g. `list.detailed`)
        	                                    	"domain": []    // in the case of a widget using a 																				domain
												},
												"visible": [ 
													["object.field", "=", "value"]		// the visibility of 																					a control can be 																							conditioned (by default 																					it is the rule of the 																				diagram that applies, if it is 																					defined) ...
													["user.field", "=", "value"],		
							   					]		// the names 'object' and 'user' are reserved and 																are associated with the context
                                            }
                                        ]
                                    }
                                ],
                            },
                        ]
                    }
                ]
            }
        ]
    }
}
```

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



## Lists

List views are the ones that displays the important fields that were saved in the form view. It contains the same properties mentioned in the ```Form View``` section, such as `name`, `description`, `layout` and many more. Some of the additional properties that are specific for the list views are `filters`, `pager` for displaying in navigation bar, `selection_actions` which allows the modification of an object in the list or exporting and printing it.
By clicking on one row in the list, it redirects you to the editable form related to the view. 

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
		"filter": "bool",			// display or not a filter zone
		"pager": "bool",			// display or not a navigation bar
		"selection_actions": [		// These actions allow either to modify the selected objects (archive, delete) with refresh of the 									list; either to execute an action with the selection as input (export, print, ...)
			{
				"label": "export",
				"action": "products_export"
			}
		],
		"items": [
			{
				"id": "firstname",		// the identifier of a field is the name of the associated field
				"type": "field",		// in a List layout, the item type is `field` by default (the columns are the fields)
				"label": "First Name",		// the label to use as the column name
				"width": "10%",		// the width of the column expressed in%. It is possible to enter 0% to be able to use in columns in the search area without displaying them.
				"widget": {			// allows you to define the cell display mode for each column
					"type": "",	 // a 'link' type allows you to open the targeted object in a new context
					"view": "",       // in the case of a widget using a view (the type of widget)
					"domain": ""      // in the case of a widget using a domain
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