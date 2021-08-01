To define the layout of the forms, the list of the fields and the possible interactions between them, we use a system of views similar to templates.

Each package has a folder named ‘views’ that contains, for each class, two JQUERY views, one for the form and the other for the list.
These files are written in JSON format, and contain information about the fields and labels to display and their positioning, for the edition of the related class.

**Filename format** is: ''//view_name//.(list | form).//default//.json''
*
A **view** has an entity, a type (form, list, menu or kanban), a view name. The view itself requests the corresponding data from the server (template or translation) when loading the layout at which a domain can be specified.
Within a view, a layout defines the way in which the widgets are linked to the model. The view is synchronized with the model during modifications.

A **Model** is a collection of objects of a given entity. This class keeps the full schema of the entity with the default values that are then updated by this model after it requests the corresponding data from the server.

A **Layout** is the layout associated with a given view. It is always linked to a Model.

A **Widget** is responsible for displaying the value of an object's field (in 'view' or 'edit' mode). It synchornizes its value with the Model to which it is associated via the Layout and the View that is using it.
When we have an x2many field the widgets: 
	=> allows you to see the selected object (form)
	=> allows you to select one or more other objects (list)


===Examples===

**Class related to a view**
```php
class Option extends Model {
    public static function getColumns() {
        return [
            'name' => [
                'type'              => 'string',
                'description'       => 'Unique name of this option.'
            ],

            'description' => [
                'type'              => 'string',
                'description'       => "Short description of the option."
            ],
            
            'family_id' => [
                'type'              => 'many2one',
                'foreign_object'    => 'sale\catalog\Family',
                'description'       => "Product Family this option belongs to.",
                'required'          => true
            ]
        ];
    }
}
```

==== Menus ==== 

**Menu View**
Menus are defined by App and are injected into the side bars (navigation drawer).

```js
[
    {
        name: [],
        description: [],
        icon: [],
        type: [entry | parent],

        target: [view_name] 
        domain:[]
        sort: []
        order: ['desc', 'asc']
        limit:
      
    	children: [
    		// sub-items
        ]
	}
]
```


==== Forms ==== 

**Form View**
Forms are the view and edit view for individual objects. It is possible to define as many views as desired, the only constraint is the definition of a default view. This view should contain all the fields present in its corresponding class, except for the fields that are of type computed. 
The name of form view is displayed like so: `packages/core/views/User.form.default.json`
A form is defined according to the following structure:

```js
{
    "name": "User",
    "description": "Simple form for displaying User",
    "layout": {
        "groups": [				// the groups are stacked vertically (there is always at least 1 group)
            {
                "label": "",			// name of the group
                "sections": [                   // if several sections, display with tabs (there is always 1 section at least)
                    {
                        "label": "",		// name of the section that will be displayed as tab
						"visible": []		// visibilty of the section
                        "rows": [		// rows are stacked vertically
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
                                                "width": "50%"
                                                "widget": {
													"header": true  // the field is a title (scaled 1.5)
	                                            	"view": ""      // in the case of a widget using a view: the view ID (the type of view is implicit in the widget, but can be forced e.g. `list.detailed`)
        	                                    	"domain": []    // in the case of a widget using a domain
												},
												"visible": [ 
													['object.field', '=', 'value']		// the visibility of a control can be conditioned (by default it is the rule of the diagram that applies, if it is defined) ...
													['user.field', '=', 'value'],		
							   					]		// the names 'object' and 'user' are reserved and are associated with the context
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


==== Lists ==== 

**List View**
List views are the ones that displays the important fields that were saved in the form view. By clicking on one row in the list, it redirects you to the editable form related to the view.
The name of file is displayed like so: `packages/core/views/User.list.default.json`
A list is defined according to the following structure:

```js
{
    "name": '',
    "description": '',
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
		"filter": bool,			// display or not a filter zone
		"pager": bool,			// display or not a navigation bar
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