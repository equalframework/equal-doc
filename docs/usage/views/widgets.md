# Widgets rendering

Widgets offer the possibility of assigning a widget type, allowing for multiple variants for the same field type.

## Types
The type of the field can be overloaded by the widget.

Supported widget types :  

* date
* string
* text
* link
* file

Example with a `moment` field defined as datetime, which should be displayed as a simple date:
```json
{
    "type": "field",
    "value": "moment",
    "width": "33%",
    "widget": {
        "type": "date"
    }
}
```

Other values are possible for specific field types:

**boolean:**
* toggle (default)
* checkbox

**many2one:**
* select (default: inline selection with typeahead, with the possibility of new context for creation or advanced selection)

**one2many:**
* list (default)
* multiselect (multi-select input view)

**many2many:**
* list (default)
* multiselect (multi-select input view)

## Usages

The widget variant can also be assigned using the `usage` property.

Most official content-types and also:

* 'color'
* 'icon'
* 'url'

```json
{
    "type": "field",
    "value": "description",
    "width": "50%",
    "widget": {
        "usage": "text/plain"
    }
}
```

Example with an `issue_date` field defined as datetime, which should be displayed with a specific format (here short):
```json
{
    "type": "field",
    "value": "issue_date",
    "width": "15%",
    "widget": {
        "usage": "datetime/short"
    }
}
```



## Styling

You can define simple visual customizations directly in widget definitions using style-related properties. These properties are **flat**, **coherent**, and **easy to memorize**, and they allow styling without custom CSS.

### Supported style properties

| Property           | Description                             | Example              |
| ------------------ | --------------------------------------- | -------------------- |
| `text-color`       | Text color (named or hex)               | `"red"`, `"#333"`    |
| `text-weight`      | Font weight                             | `"bold"`, `"500"`    |
| `text-align`       | Horizontal alignment                    | `"left"`, `"center"` |
| `text-decoration`  | Text decoration                         | `"underline"`        |
| `background-color` | Background color                        | `"lightgray"`        |
| `border-color`     | Border color                            | `"blue"`             |
| `border-radius`    | Border corner radius (CSS syntax)       | `"4px"`, `"0.25rem"` |
| `padding`          | Inner spacing                           | `"8px"`              |
| `margin`           | Outer spacing                           | `"0 auto"`           |
| `icon`             | Icon to display (by name or emoji)      | `"info"`, `"⚠️"`     |
| `icon-position`    | Icon placement relative to text         | `"left"`, `"right"`  |
| `icon-color`       | Icon color (falls back to `text-color`) | `"orange"`           |

### Example

```json
{
  "widget": "label",
  "text": "Important notice",
  "text-color": "#b30000",
  "text-weight": "bold",
  "background-color": "#fff3cd",
  "border-color": "#f5c6cb",
  "icon": "⚠️",
  "icon-position": "left"
}
```
