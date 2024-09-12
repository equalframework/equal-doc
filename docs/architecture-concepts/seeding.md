# Seeding

This documentation provides a comprehensive guide on how to use and configure the seeding action for populating your database with demo data. This guide covers the structure of a seed configuration file and explains the different components involved.

## Init seed

The seeding action's name is `core_init_seed`, it generates objects for a specific package using json configuration files located in `{package}/init/seed/`.

Action params:

- **package**: The name of the package to seed.
- **config_file**: If given, only the specified config file will be considered.

### Configuration File

A seed configuration file is a JSON array where each object represents a seeding operation for a specific data model. The file specifies which models to seed, the quantity of records to create, the fields to set, and the relationships between different models.

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
				"random_qty": [5, 10],
				"fields": {
					"severity": "notice"
				}
			}
		}
	}
]
```

#### Structure

Here's a breakdown of the key components in the seed action configuration file:

- **name**: Specifies the fully qualified name of the model class to be seeded. *(required)*
- **lang**: Defines the language for any localized fields. If not specified, defaults to *DEFAULT_LANG*.
- **qty**: Indicates the number of records to create for the specified model. If not specified, defaults to *1*.
- **random_qty**: Specifies a range for the number of records to create.
- **fields**: Contains static values to be set on the model fields.
- **set_object_data**: Maps fields from the created object to be used later in relationships domain.
- **relations**: Defines relationships to other models and how to handle them.

#### Relations structure

- **mode**: Defines how to handle related records.
    - **create**: Creates new record(s).
    - **use-existing**: Uses existing record(s), based on the given domain, if no existing record meet the criteria, fallback on **create**.
    - **use-existing-or-create**: Attempts to use existing record(s) or create new if none exists. This mode defaults to a 50/50 chance of either action, but if no existing records meet the criteria, fallback on **create**.
- **qty**: Number of related records to create. If not specified, defaults to 1.
- **random_qty**: Specifies a range for the number of related records to create if applicable.
- **fields**: Default values for related records.
- **set_object_data**: Maps fields from related records.
- **domain**: Filters for selecting existing records.

## Model generate

The model generate action's name is `core_model_generate`, it generates objects for a specific entity with given and random data. This action is used by the seed action to generate the objects.
