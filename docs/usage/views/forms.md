# Forms


## routes
Form views may hold a **routes** element, specific to the current view, and  that contains a series of descriptors that are used to display button s on the right pane.

??? example "Example of routes definition"
    ``` json title="Booking.form.json" linenums="1"
    "routes": [
        {
            "id": "item.booking.file",
            "label": "Booking form",
            "description": "",
            "icon": "library_books",
            "route": "/booking/object.id",
            "context": {
                "entity": "lodging\\sale\\booking\\Booking",
                "view": "form.default",
                "domain": ["id", "=", "object.id"],
                "reset": true
            }
        },
        {
            "id": "item.booking.edit",
            "label": "Booked services",
            "description": "",
            "icon": "room_service",
            "route": "/booking/object.id/services"
        },
        {
            "id": "item.booking.contract",
            "label": "Send contract",
            "description": "",
            "icon": "drive_file_rename_outline",
            "route": "/booking/object.id/contract",
            "visible": [["has_contract", "=", true], ["status", "=", "confirmed"]]
        }
    ]
    ```

```json
    "routes": [
        {
            "id": "print.pdf",
            "label": "Print tender",
            "description": "",
            "icon": "print",
            "route": "/?get=renover_tender-pdf&id=object.id",
            "target": "_blank",
            "absolute": true
        }
    ]
```


## layout

Form views hold a **layout** element that contains all the information needed to display a single object in the view.

At the root of their layout descriptor, form views have a single array of groups descriptors.
When rendered, a form view have all its groups shown one below another.


| **PROPERTY** | **TYPE** |**DESCRIPTION**                                              |
| ------------ | ------------------------------------------------------------ | ----- |
| Groups | list of [Group](#group) | Groups of Section used to split the page horizontally |


### groups

**Groups** are horizontal part of a form view, containing sections.

| **PROPERTY** | **TYPE** |**DESCRIPTION**                                              |
| ------------ | ------------------------------------------------------------ | ----- |
| id | `string` | unique, used for translations.
| label | `string` | (optional) name used when no translation is available.
| sections |  list of [Section](#section) | list of section, represented as tabs |



### sections

**Section** contain a part of the layout presented in rows and columns.

If a group has more than 1 section, its sections are presented tabs.


| **PROPERTY** | **TYPE** |**DESCRIPTION**                                              |
| ------------ | ------------------------------------------------------------ | ----- |
| id | `string` | unique id, used for translations.
| label | `string` | (optional) name used when no translation is available.
| rows |  list of [Row](#row) |  |
|visible | `array`>`domain` | Domain that use the context to allow display of the section or not |



### rows

| **PROPERTY** | **TYPE** |**DESCRIPTION**                                              |
| ------------ | ------------------------------------------------------------ | ----- |
| id | `string` | unique id, used for translations.
| label | `string` | (optional) name used when no translation is available.
| rows |  list of [Column](#column) |  |
|visible | `array`>`domain` | Domain that use the context to allow display of the section or not |



### columns

| **PROPERTY** | **TYPE** |**DESCRIPTION**                                              |
| ------------ | ------------------------------------------------------------ | ----- |
| id | `string` | unique id, used for translations.
| label | `string` | (optional) name used when no translation is available.
| rows |  list of [Item](#item) |  |
|visible | `array`>`domain` | Domain that use the context to allow display of the section or not |
| width  | `integer`>`number/natural:100` | width of the column |



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



### Widget

Widget are used to set properties that depends on the type of view.


| **PROPERTY** | **TYPE** |**DESCRIPTION**                                              |
| ------------ | ------------------------------------------------------------ | ----- |
| link | `boolean` | Flag telling if the content of the item a link. |
| heading | `boolean`| Emphasizes the item (large version of the widget). |
| type | `string` | edit the type of display of the item, depends on the type of the field. |
| usage | `string` | override the usage of the field to display it. |

There are some additional properties depending on the type of field the item relates to:

**Associative fields (one2many, many2many)**


| **PROPERTY** | **TYPE** |**DESCRIPTION**                                              |
| ------------ | ------------------------------------------------------------ | ----- |
| header | [Header](#header) | *Associative Field Only*.  Override the header of the view to display the relation. |
| domain | `array`>`domain` | *Associative Field Only*.  Override the domain of the view to display the relation. |
| view | `string` | *Associative Field Only*. Provide the id of the view to use to display the relation. |

**one2many**

| **PROPERTY** | **TYPE** |**DESCRIPTION**                                              |
| ------------ | ------------------------------------------------------------ | ----- |
| autoselect | `boolean` | *One2Many Field Only*. Item is automatically selected when list contains only 1 (default = true). |



For certain fields, the header has an impact on the layout of the corresponding widget in a form view.

For instance, for many2one fields, header information is used to display (or not) the buttons for creating a new item or opening the selected item in a new context.

By modifying the header, it is therefore possible to deactivate the display of these buttons.

```json
"widget": {
    "header": {
        "actions": {
            "ACTION.CREATE": false,
            "ACTION.OPEN": false
        }
    }
}
```



??? tip "Using keyboard inputs"
    Navigation between input widgets is possible via the keyboard, primarily using the <tab> key. When opening dropdown selection boxes, the <up> and <down> keys allow navigation among the options.
    In form views, while in edit mode, it's also possible to use the combination <ctrl+s> to save the current form.
    In the case of a split button with multiple save actions (save and continue, save and close, ...), it's the first save action that is executed.
    During a keydown event, the keyboard status is captured by the visible Frame and relayed to the active context, which in turn relays it to the view (via a call to `keyboardAction()`).
