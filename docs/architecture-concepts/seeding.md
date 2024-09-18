# Seeding

eQual comes with a handy automatic data population mechanism called seeding. 

Seeding allows for the automatic generation of objects. This feature is especially useful during development, as it enables developers and testers to work with consistent datasets. In addition, seeding ensures that generated values are consistent with the field descriptors, as defined in the related `getColumns()` method.


## Init seed

The action controller `core_init_seed` allows to automatically generate objects for a specific package according to the schemas (JSON files) present in `{package}/init/seed/`.

**Action params:**

| **Param** | **Description**                                                           |
|-------------------|---------------------------------------------------------------------------|
| **package**       | The name of the package to seed.                                           |
| **config_file**   | If given, only the specified config file will be considered.               |




### Seeding schema

A seeding schema hold a JSON array where each object represents a seeding operation for a specific data model. The file specifies which models to seed, the quantity of records to create, the fields to set, and the relationships between different models.

**Example:**

```json
[
	{
		"name": "core\\alert\\MessageModel",
		"qty": 1,
		"fields": {
			"name": "Example model"
		},
		"relations": {
			"messages_ids": {
				"mode": "create",
				"qty": [5, 10],
				"fields": {
					"severity": "notice"
				}
			}
		}
	}
]
```



Here's a breakdown of the key components in the seed action configuration file:


| **Attribute**         | **Description**                                                                                                                                                                                   |
|-----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **name**              | Specifies the fully qualified name of the model class to be seeded. *(required)*                                                                                                                  |
| **lang**              | Defines the language for any localized fields. If not specified, defaults to *DEFAULT_LANG*.                                                                                                      |
| **qty**               | Can be either an integer or an array holding 2 integers ([min, max]). A single integer indicates the exact number of records to create for the specified model. In case of an array, it specifies a range of number of records to create (which is picked randomly between min and max). If not specified, defaults to *1*. |
| **fields**            | Contains static values to be set on the model fields.                                                                                                                                              |
| **set_object_data**    | Maps fields from the created object to be used later in relationships domain.                                                                                                                     |
| **relations**         | Defines relationships to other models and how to handle them (see below).                                                                                                                          |



#### Relation descriptor


Descriptor for relational fields are similar to the entity descriptor, but hold additional attributes : 

| **Attribute** | **Description**                                              |
| ------------- | ------------------------------------------------------------ |
| **mode**      | Defines how to handle related records.                       |
|               | **create**: Creates new record(s).                           |
|               | **use-existing**: Uses existing record(s), based on the given domain. If no existing record meets the criteria, fallback on **create**. |
|               | **use-existing-or-create**: Attempts to use existing record(s) or create new if none exist. |
| **domain**    | Filters for selecting existing records.                      |



!!! note ''set_object_data"
    At any level, data can be arbitrary append to a global object reference, and can then be used in subsequent domains.




## Model generate

The action controller `core_model_generate` allows to generate objects for a specific entity with both given and random data.

This action is used by the `core_init_seed` controller to generate the objects.



**Example:**

```bash
$ ./equal.run --do=model_generate --entity='core\User' --qty=5
```



**Action params:**

| **Param**           | **Description**                                              |
| ------------------- | ------------------------------------------------------------ |
| **entity**          | Specifies the fully qualified name of the model class to be seeded. *(required)* |
| **qty**             | Can be either an integer or an array holding 2 integers ([min, max]). A single integer indicates the exact number of records to create for the specified model. In case of an array, it specifies a range of number of records to create (which is picked randomly between min and max). If not specified, defaults to *1*. |
| **fields**          | Associative array mapping fields to their related values.    |
| **set_object_data** | Maps fields from the created object to be used later in relationships domain. |
| **relations**       | Relational fields descriptor, telling how to handle the object relations (many2one, one2many and many2many). |



!!! note "generation attribute"
    In the entity schema, described with `getColumns()` method, the `generation` attribute allows to specifies how the field's values should be generated. This attribute must refer to a static method of the targeted entity (with no arguments).
