# Lists

## header

The **header** section allows to override the default behavior of the view.

Here are the properties are specific to views.

(for the common properties of the Views header, see [views common strcucture](./common-structure)).

| **PROPERTY** | **TYPE** | **DESCRIPTION**                                              |
| ------------ | ---- | ------------------------------------------------------------ |
| selection | [Selection](#selection) | Descriptor for the actions that can be applied when one or more items are selected in the list. |
| actions |                         | Map allowing to disable specific actions for the view. |
| advanced_search | `boolean` or descriptor |  |




#### selection
The `selection` property allows to customize the list of bulk actions that are available when one or more items are selected.

| **PROPERTY** | **TYPE** |**DESCRIPTION**                                              |
| ------------ | ------------------------------------------------------------ | ----- |
| **default**  | `boolean` | (optional) Flag telling if the default actions have to be present in the available action to apply on current selection. (defaults to true) |
| **actions**  | array of [Action](#action) |(optional) An array of action items that can be applied on current selection. |

**Examples :**

**a. Prevent selecting items within the list:**

```json
"header": {
    "selection": false
}
```

**b. Allow custom filters, disable quick search, and  allow only `ACTION.CLONE` and a custom action :**

```json
"header": {
    "filters": {
        "custom": true,
        "quicksearch": false
    },
    "selection": {
        "default" : false,
        "actions" : [
            {
                "id": "ACTION.CLONE"
            },
            {
                "id": "header.selection.actions.mark_ignored",
                "label": "Mark as ignored",
                "icon": "",
                "controller": "lodging_sale_booking_bankstatementline_bulk-ignore"
            }
        ]
    }
}
```



#### actions

**Examples:**
```json
"header": {
    "actions": {
        "ACTION.CREATE" : false
    }
}
```

#### advanced_search

In the case where the specified controller for data collection is not the default controller (`core_mode_collect`), the UI checks if a view is associated with this controller (that must extend `core_model_collect`). Since controllers can be handled as an entity. In that case, its parameters are considered as fields that can be assigned in an "advanced search" form.

However, in the header, it is always possible to explicitly tell if the advanced search must be shown or not, and if it should be open when view loads (default behavior is closed).

**Examples : **
```
    "advanced_search": false
```

```
    "advanced_search": {
        "show": true,
        "open": true
    }
```


## order

String holding the name(s) of the field to sort results on, separated with commas.
Example :

```json
"order": "sku,product_model_id"
```

## limit

Integer providing the maximum number of items that can be shown on each page of the list.
(Read more details about pagination.)

## controller

The optional **controller**  property specifies the controller that must be requested for fetching the Model collection that will feed the View (either a single object or a collection of objects).

The default values is `model_collect` (which is an alias for `core_model_collect`)

## filters

It is possible to pre-define "custom" filters, which will be displayed among the list of user-defined custom filters. These pre-defined filters provide preset filtering options that complement the user's own configurations.

Example :
```
    "filters": [
        {
            "id": "lang.french",
            "label": "français",
            "description": "Users with locale set to french.",
            "clause": ["language", "=", "fr"]
        }
    ],
```



!!! Note "clause or dommain"
    Both "clause" and "domain" are allowed to describe a filter



## group_by

A `group_by` array can be set to describe the way the objects have to be grouped.
Each item in the array is either a field name or the descriptor of an operation to perform on a specific field.

When objects are grouped on fields, they're sorted on the field when it is a string, or on the `name` field when it is an object. In the latter case, the field to be used for sorting can be modified using the `order`  property.

Example :

```json
"group_by": ["date"]
```

The group_by descriptors use the structure shown in the following example:

```json
"group_by":[ {
    "open": true,
    "colspan": 3,
    "field": "product_id",
    "operation": ["SUM", "object.qty"],
    "order": "name"
}]
```

Another example :

```json
"group_by": ["date", {"field": "product_id", "operation": ["SUM", "object.qty"], "open": false}]
```



**Recap**

| Property     | Type                | Description                                                  |
| ------------ | ------------------- | ------------------------------------------------------------ |
| `field`      | `string`            |  The field on which to group the data. Can be a scalar field (e.g., `date`) or a relation (e.g., `product_id`). |
| `operation`  | `array` or `string` |  An operation to perform on the grouped data. Accepts an operation array like `["SUM", "object.qty"]` or a string operator. |
| `order`      | `string`            |  Field used to sort the groups. By default, uses `"name"` for object fields, or the field value itself for scalars. |
| `open`       | `boolean`           |  Indicates whether the group should be expanded (`true`) or collapsed (`false`) by default in the UI. |
| `operations` | `object`            |  A mapping of additional operations to apply to specific fields inside this group. Useful for displaying multiple computed values with formatting. |




## operations

Associative array ([Operation](/architecture-concepts/operations) | (optional) make calculation on the whole fetcthed data do display it.

**Examples:**

```
"operations": {
  "total": {
    "total_paid": {
      "id": "operations.total.total_paid",
      "label": "Total received",
      "operation": "SUM",
      "usage": "amount/money:2"
    },
    "total_due": {
      "operation": "SUM",
      "usage": "amount/money:2"
    }
  }
}
```




## layout 
Lists views hold a **layout**  element that contains all the information needed to display the a list of objects that must be presented  in the view.

At the root of their layout descriptor, list views have a single array of Items descriptors. Each Item descriptor defines the structure of a row in the table, including the fields or attributes of the object to be displayed.

When rendered, a list view consists of a table with each of its columns showing the value for the corresponding object's attributes as defined in the Item descriptors. Users can interact with the table, such as sorting rows based on specific column values or filtering based on certain criteria.

| **PROPERTY** | **TYPE** |**DESCRIPTION**                                              |
| ------------ | ------------------------------------------------------------ | ----- |
| Items | list of [Item](#item) | List of the column to display |



### items


**Items** are a descriptor of the way a **field** or a **label** of a **Model** is displayed in a view.

| **PROPERTY** | **TYPE** |**DESCRIPTION**                                              |
| ------------ | ------------------------------------------------------------ | ----- |
| id | `string` | (optional) unique id, used for traductions (traductions override label and value) |
| label | `string` | (optional) override the value for the title value of the item |
| type | `string` | type of item (either `field` or `label`) |
| width |  `integer`>`number/natural:100`| width of the field (in percentage its parent) |
| readonly | `boolean` | tell if the item is editable in a creation or edtition context of the usage of the view |
| visible | `boolean` or `array`>`domain` | tell if the item should be displayed |
| widget | [Widget](#widget) | Give properties to the item, depends on the type of view |



#### Widget

The widget property can be used to set properties that depends on the type of view.

When a widget targets a view, the properties of the related schema can be overridden by assigning custom values.

**Properties common to all fields**


| **PROPERTY** | **TYPE** |**DESCRIPTION**                                              |
| ------------ | ------------------------------------------------------------ | ----- |
| link | `boolean` | If set to true, the value will be displayed as a link. |
| sortable | `boolean`| Can the user sort the list by this item ? |
| type | `string` | Edit the type of display of the item, depends on the type of the field |
| domain | `array`>`domain` | |
| usage | `string` | Override the usage of the field to display it|

**Properties specific to many2one and many2many fields**

Most of the properties can be forced within the widget

| PROPERTY   | **TYPE**        | **DESCRIPTION**        |
| ---------- | --------------- | ---------------------- |
| header     | `header object` | @see [Header](#header) |
| controller |                 |                        |
| start      |                 |                        |
| limit      |                 |                        |
| sort       |                 |                        |
| order      |                 |                        |
| group_by   |                 |                        |


**Examples:**


(For one2many relation)

**Default**

(no overrides in `widget`)

* View Mode
  - "create" button

* Edit Mode
  - "create" button
  - Selection allowed
      - Actions: "remove"



**Custom actions**

```json
"widget": {
    "header": {
        "selection": {
            "default": false,
            "actions": [
                {"id": "ACTION.EDIT_INLINE"},
                {"id": "ACTION.CLONE"}
            ]
        }
    }
}
```

* View Mode
  - "create" button

* Edit Mode
  - "create" button
  - Selection allowed
    - Actions: "edit inline", "duplicate"



##### No selection or actions

```json
"widget": {
    "header": {
        "selection": false,
        "actions": {
            "ACTION.CREATE": false
        }
    }
}
```

* View Mode
  - No action buttons

* Edit Mode
  - No action buttons
  - Selection / Actions
    - No selection allowed