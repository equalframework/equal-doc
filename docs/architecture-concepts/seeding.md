# Seeding

This documentation provides a comprehensive guide on how to use and configure a seeder for populating your database with initial or test data. This guide covers the structure of a seeder configuration file and explains the different components involved.

## Action

The seeding action's name is `core_init_seed`, it generates objects for a specific package using json configuration files located in `{package}/init/seed/`.

Action params:
- **`package`**: The name of the package to seed.
- **`config_file`**: If given, only the specified config file will be considered.

## Configuration File

A seed configuration file is a JSON array where each object represents a seeding operation for a specific data model. The file specifies which models to seed, the quantity of records to create, the fields to set, and the relationships between different models. The file must be in in a package

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

### Structure

Here's a breakdown of the key components in the seed action configuration file:

1. **`name*`**: Specifies the fully qualified name of the model class to be seeded.
2. **`lang`**: Defines the language for any localized fields. If not specified, defaults to *DEFAULT_LANG*.
3. **`qty`**: Indicates the number of records to create for the specified model. If not specified, defaults to *1*.
4. **`random_qty`**: Specifies a range for the number of records to create.
5. **`fields`**: Contains static values to be set on the model fields.
6. **`set_object_data`**: Maps fields from the created object to be used later in relationships domain.
7. **`relations`**: Defines relationships to other models and how to handle them.

*\* required*

#### Relations

- **`mode`**: Defines how to handle related records.
    - **`create`**: Creates new record(s).
    - **`use-existing`**: Uses existing record(s), based on the given domain, if no existing record meet the criteria, fallback on **create**.
    - **`use-existing-or-create`**: Attempts to use existing record(s) or create new if none exists. This mode defaults to a 50/50 chance of either action, but if no existing records meet the criteria, fallback on **create**.
- **`qty`**: Number of related records to create. Defaults to `1` if not specified.
- **`random_qty`**: Specifies a range for the number of related records to create if applicable.
- **`fields`**: Default values for related records.
- **`set_object_data`**: Maps fields from related records.
- **`domain`**: Filters for selecting existing records.

*\* required*
