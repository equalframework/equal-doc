# Data Model, Fields, Usages, Constraints, and Data Adaptation

The eQual framework uses a sophisticated structure for type and value adaptation to manage data consistently and flexibly. 

Values are associated with specific types and usages, with constraints and adaptation mechanisms to ensure data integrity and validity throughout their lifecycle.

## Entity Definition

Entities are defined by inheriting from a base class called `Model`. This base class structures the data into various fields (`Field`).

## Fields and Usages

Each field is associated with a type and a usage:
- **Types**: The types handled by the ORM are limited to scalar and relational types such as `string`, `boolean`, `integer`, `float`, `date`, `datetime`, `time`, `binary`, `array`, `many2one`, `one2many`, `many2many`.
- **Content-Types**: Each type is linked to a specific content-type, as shown in the provided equivalence (e.g., `boolean` is associated with `number/boolean`).
- **Usages**: A usage is an extension of the content-type that includes additional information and constraints, such as size or specific standards (e.g., `number/integer:9` for integers with a maximum size of 9 digits).


### Types equivalence with Content-Types:

- `boolean`   : `number/boolean`
- `integer`   : `number/integer`
- `float`     : `number/real`
- `string`    : `text/plain`
- `date`      : `date/plain`
- `datetime`  : `date/datetime`
- `time`      : `time/plain`
- `binary`    : `binary/plain`
- `many2one`  : `number/natural`
- `one2many`  : `array`
- `many2many` : `array`
- `array`     : `array`

### Type equivalence with Usages:

- `boolean`   : `number/boolean`
- `integer`   : `number/integer:9`
- `float`     : `number/real:10.2`
- `string`    : `text/plain:255`
- `date`      : `date/plain`
- `datetime`  : `date/time`
- `time`      : `time/plain`
- `binary`    : `binary/plain:64000000`
- `many2one`  : `number/integer:9`
- `one2many`  : `array`
- `many2many` : `array`
- `array`     : `array`

### Constraints and Data Validation

The `Field` class provides a method `getConstraints()` to retrieve constraints specific to the type and usage of the field:
- **Field `getConstraints` Method**: This method calls the `getConstraints` method of the usage associated with the field to obtain the constraints.
- **Value Validation**: When values are associated with a usage, their validity is checked via the usage's `getConstraints` method.

## Data Adaptation

Data adaptation is performed via a `Service Data Adapter Provider`, which allows converting values from one format to another based on their usage:
- **Supported Formats**: Specific supported formats include `application/json`, `application/sql`, and `text/plain`.
- **Data Adapter**: The `Data Adapter` uses the **`adaptIn()` and `adaptOut()` ** methods to convert values. For example, a date timestamp can be converted into different output formats (JSON, SQL, text).

## Type Adaptation

The `Field` class has a method `getContentType` to find the content-type associated with the field's type. This content-type is then used to adapt the type from one format to another via:
- **`castTypeIn()` and `castTypeOut()` ** methods: These methods allow converting data types according to the target format, ensuring that the data is correctly interpreted based on the context of its use.

For instance a field with a `text/plain:255` Usage would be stored in MySQL as `varchar(255)`, while a field with a `text/plain:255` Usage would be stored using a `mediumtext` or a `longtext`.			

## Adaptation Example

Consider the example of converting a date timestamp (1717960331) into different formats:
- **application/json**: `2024-06-09T19:12:11.000Z`
- **application/sql**: `2024-06-09 21:12:11`
- **text/plain**: `Sunday, June 09, 2024 9:12 PM` or `Dimanche 09 Juin 2024 21h12` (depending on the locale)



This demonstrates how values can be adapted according to their usages and target formats, ensuring flexible and consistent data management in the Equal framework.



## Key takeaways

* The objects manipulated in controllers and classes contain values using PHP types.
* Collections can be exported by recursively converting the values during export (`get()`):
  `adapter->adaptOut($value, $f->getUsage())`

Non-exhaustive list of supported Usages :

```
text/plain
text/html
amount/money:4
amount/money:4
amount/percent
amount/rate
numeric/hexadecimal:1
numeric/integer
numeric/integer:3
image/jpeg
email
phone
password
password/nist
date/month
date/year
date/year:4
country/iso-3166:2
language/iso-639
uri/url
uri/urn.iban
uri/urn.ean
```