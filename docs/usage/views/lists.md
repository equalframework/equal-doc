# Lists


Lists views hold a **layout**  element that contains all the information needed to display the a list of objects that must be presented  in the view.

At the root of their layout descriptor, list views have a single array of Items descriptors. Each Item descriptor defines the structure of a row in the table, including the fields or attributes of the object to be displayed.

When rendered, a list view consists of a table with each of its columns showing the value for the corresponding object's attributes as defined in the Item descriptors. Users can interact with the table, such as sorting rows based on specific column values or filtering based on certain criteria.

| **PROPERTY** | **TYPE** |**DESCRIPTION**                                              |
| ------------ | ------------------------------------------------------------ | ----- |
| Items | list of [Item](#item) | List of the column to display |



### Items


**Items** are a descriptor of the way a **field** or a **label** of a **Model** is displayed in a view.

#### Structure summary

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

**Structure Summary**



| **PROPERTY** | **TYPE** |**DESCRIPTION**                                              |
| ------------ | ------------------------------------------------------------ | ----- |
| link | `boolean` | is the content of the item a link ? |
| sortable | `boolean`| can the user sort the list by this item ? |
| type | `string` | edit the type of display of the item, depends on the type of the field |
| domain | `array`>`domain` | |
| usage | `string` | override the usage of the field to display it|



