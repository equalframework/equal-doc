# Model Import

eQual offers a simple and flexible way to import data from JSON/CSV files.
To achieve this, you need to create a configuration using **`core\import\EntityMapping`**.
This provides the necessary setup to map the import of specific entities into the system.

---

## Classes

### EntityMapping

An _EntityMapping_ is associated with an entity and defines how to import data from a JSON/CSV file.
It includes a type and one or more _ColumnMappings_ that describe the mapping rules.

To manage these configurations, navigate to: **Settings > Configuration > Import > Entity Mappings**

#### Mapping Types

There are two types of _EntityMapping_:

1. **`Index`**

    - Suitable for CSV files without column names.
    - The mapping is based on the **index** of the columns (e.g., `0`, `1`, `2`, ...).

2. **`Name`**

    - Suitable for JSON and CSV files with column names.
    - The mapping is based on the **name** of the fields/columns.

### ColumnMapping

A _ColumnMapping_ defines how to map a field/column from the import file to a corresponding entity field.
It can contain one or multiple _DataTransformer_ that allows to modify the data before it is used to create the new entity.

Its structure depends on the type of mapping:

#### Index Mapping
- **`origin_index`**: The index of the column in the CSV file to retrieve data from.
- **`target_name`**: The name of the entity field where the data will be stored.

#### Name Mapping
- **`origin_name`**: The name of the column/field in the JSON/CSV file to retrieve data from.
- **`target_name`**: The name of the entity field where the data will be stored.

### DataTransformer

A _DataTransformer_ allows you to modify the data from the import file before saving it to the entity.
This can be used for operations like sanitization, formatting, or computation.

Multiple _DataTransformer_ can be used, the **`order`** field will define the order in which they are executed.

#### Example: Phone Number Sanitization
Using the **phone** transformer with a configured prefix of `32` (Belgium):

- `062568555` → `+3262568555`
- `62568555` → `+3262568555`
- `062 56.85.55` → `+3262568555`

#### Transformer Types

  - **`value`**  
    Sets a predefined value.
  - **`cast`**  
    Casts the value to a specific type: `string`, `integer`, `float`, or `boolean`.
  - **`round`**  
    Rounds the value to the nearest integer.
  - **`multiply`**  
    Multiplies the value by a specified number.
  - **`divide`**  
    Divides the value by a specified number.
  - **`field-contains`**  
    Sets `true` if the value contains a specific string.
  - **`field-does-not-contain`**  
    Sets `true` if the value does not contain a specific string.
  - **`map-value`**  
    Replaces a value with another configured value.
  - **`replace`**  
    Substitutes a word/character with another specified word/character.
  - **`trim`**  
    Removes leading and trailing spaces.
  - **`phone`**  
    Sanitizes a phone number and adds a configured prefix.

### ERD

<center><img src="/_assets/img/erd_import.png" /></center>

---

## Actions

### Import CSV file

The action controller `core_model_import-csv-file` allows to import data from a CSV file.

#### Action params

| **Param**                   | **Description**                                                            | **Default** |
|-----------------------------|----------------------------------------------------------------------------|:-----------:|
| **entity_mapping_id**       | The id of the entity mapping to use to adapt given data to eQual entities. |             |
| **csv_separator_character** | The character used to separate columns in csv file.                        |      ,      |
| **csv_enclosure_character** | The character used to enclose in csv file.                                 |      "      |
| **csv_escape_character**    | The character used to escape in csv file.                                  |      \      |
| **data**                    | Payload of the file (raw file data).                                       |             |

#### Example type _name_

Identity.csv:
```csv
name,lastname,age,address,phone_number
John,Doe,45,"Rue jaune 45, 1254, Paris",05 44 74 39 19
Bill,Matros,45,"Rue noir 658, 2447, Nantes",564748947
Louise,Dix,11,"Avenue verte 124, 4456, Bordeaux",+33145612122
```

EntityMapping:
```json
{
  "id": 1,
  "entity": "identity\\Identity",
  "mapping_type": "name",
  "column_mappings_ids": [
    { "id": 1, "origin_name": "name", "target_name": "firstname", "data_transformers_ids": [] },
    { "id": 2, "origin_name": "lastname", "target_name": "lastname", "data_transformers_ids": [] },
    { "id": 3, "origin_name": "address", "target_name": "address_street", "data_transformers_ids": [] },
    {
      "id": 4,
      "origin_name": "phone_number",
      "target_name": "phone",
      "data_transformers_ids": [
        {
          "transformer_type": "phone",
          "order": 1,
          "phone_prefix": "33"
        }
      ]
    }
  ]
}
```

Command:
```shell
./equal.run --do=core_model_import-csv-file --entity_mapping_id=1 --data="$(base64 ./Identity.csv)"
```

Result:
```json
[
  { "firstname": "John", "lastname": "Doe", "address_street": "Rue jaune 45, 1254, Paris", "phone": "+33544743919" },
  { "firstname": "Bill", "lastname": "Matros", "address_street": "Rue noir 658, 2447, Nantes", "phone": "+33564748947" },
  { "firstname": "Louise", "lastname": "Dix", "address_street": "Avenue verte 124, 4456, Bordeaux", "phone": "+33145612122" }
]
```

#### Example type _index_

If the CSV file to import does not contain a first line with the columns names, then use an index _EntityMapping_.

Identity.csv:
```csv
John,Doe,45,"Rue jaune 45, 1254, Paris",05 44 74 39 19
Bill,Matros,45,"Rue noir 658, 2447, Nantes",564748947
Louise,Dix,11,"Avenue verte 124, 4456, Bordeaux",+33145612122
```

EntityMapping:
```json
{
  "id": 2,
  "entity": "identity\\Identity",
  "mapping_type": "index",
  "column_mappings_ids": [
    { "id": 1, "origin_index": 0, "target_name": "firstname", "data_transformers_ids": [] },
    { "id": 2, "origin_index": 1, "target_name": "lastname", "data_transformers_ids": [] },
    { "id": 3, "origin_index": 3, "target_name": "address_street", "data_transformers_ids": [] },
    {
      "id": 4,
      "origin_index": 4,
      "target_name": "phone",
      "data_transformers_ids": [
        {
          "transformer_type": "phone",
          "order": 1,
          "phone_prefix": "33"
        }
      ]
    }
  ]
}
```

Command:
```shell
./equal.run --do=core_model_import-csv-file --entity_mapping_id=2 --data="$(base64 ./Identity.csv)"
```

Result:
```json
[
  { "firstname": "John", "lastname": "Doe", "address_street": "Rue jaune 45, 1254, Paris", "phone": "+33544743919" },
  { "firstname": "Bill", "lastname": "Matros", "address_street": "Rue noir 658, 2447, Nantes", "phone": "+33564748947" },
  { "firstname": "Louise", "lastname": "Dix", "address_street": "Avenue verte 124, 4456, Bordeaux", "phone": "+33145612122" }
]
```

### Import JSON file

The action controller `core_model_import-json-file` allows to import data from a JSON file.

**Action params:**

| **Param**                   | **Description**                                                            | **Default** |
|-----------------------------|----------------------------------------------------------------------------|:-----------:|
| **entity_mapping_id**       | The id of the entity mapping to use to adapt given data to eQual entities. |             |
| **data**                    | Payload of the file (raw file data).                                       |             |

The _EntityMapping_ must be of type _name_ to import a json file.

#### Example type _name_

Identity.json:
```json
[
  { "name": "John", "lastname": "Doe", "age": 45, "address": "Rue jaune 45, 1254, Paris", "phone_number": "05 44 74 39 19" },
  { "name": "Bill", "lastname": "Matros", "age": 45, "address": "Rue noir 658, 2447, Nantes", "phone_number": "564748947" },
  { "name": "Louise", "lastname": "Dix", "age": 11, "address": "Avenue verte 124, 4456, Bordeaux", "phone_number": "+33145612122" }
]

```

EntityMapping:
```json
{
  "id": 1,
  "entity": "identity\\Identity",
  "mapping_type": "name",
  "column_mappings_ids": [
    { "id": 1, "origin_name": "name", "target_name": "firstname", "data_transformers_ids": [] },
    { "id": 2, "origin_name": "lastname", "target_name": "lastname", "data_transformers_ids": [] },
    { "id": 3, "origin_name": "address", "target_name": "address_street", "data_transformers_ids": [] },
    {
      "id": 4,
      "origin_name": "phone_number",
      "target_name": "phone",
      "data_transformers_ids": [
        {
          "transformer_type": "phone",
          "order": 1,
          "phone_prefix": "33"
        }
      ]
    }
  ]
}
```

Command:
```shell
./equal.run --do=core_model_import-csv-file --entity_mapping_id=1 --data="$(base64 ./Identity.json)"
```

Result:
```json
[
  { "firstname": "John", "lastname": "Doe", "address_street": "Rue jaune 45, 1254, Paris", "phone": "+33544743919" },
  { "firstname": "Bill", "lastname": "Matros", "address_street": "Rue noir 658, 2447, Nantes", "phone": "+33564748947" },
  { "firstname": "Louise", "lastname": "Dix", "address_street": "Avenue verte 124, 4456, Bordeaux", "phone": "+33145612122" }
]
```
