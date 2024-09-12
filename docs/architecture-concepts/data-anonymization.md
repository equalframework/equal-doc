# Data anonymization

This documentation provides a high-level guide on how to configure and use the data anonymization to replace sensitive data in your system. The guide covers the structure of a data anonymization configuration file and explains the key components involved.

## Init anonymize

The data anonymization action's name is `core_init_anonymize`, it anonymizes objects for a specific package using json configuration files located in `{package}/init/anonymize/`.

Action params:

- **package**: The name of the package to anonymize.
- **config_file**: If given, only the specified config file will be considered.

## Configuration File

The configuration file is a JSON array where each object specifies a model to be anonymized, including the fields and relationships to be processed.

```json
[
    {
        "name": "core\\alert\\MessageModel",
        "fields": ["label", "description"],
        "relations": {
            "messages_ids": {
                "fields": ["label", "description"]
            }
        }
    }
]
```

### Structure

Here's a breakdown of the key components in the anonymize action configuration file:

- **name**: Specifies the fully qualified name of the model class to be seeded. *(required)*
- **fields**: Contains fields that need to be anonymized.
- **relations**: Defines relationships to other models and how to handle them.

## Model anonymize

The model anonymize action's name is `core_model_anonymize`, it anonymizes existing objects of a specific entity for a given domain. This action is used by the init anonymize action to anonymize the objects.

A Model field can be anonymized automatically (without having to specify it in the `fields` option) by setting its sensitive option to true. Below we can see that the ``content`` field will always be anonymized.

```php
public static function getColumns() {
	return [
		'user_id' => [  
		    'type'              => 'many2one',
		    'foreign_object'    => 'core\User',
		    'description'       => 'User recipient of the message, if any'
		],
		'content' => [
			'type'              => 'string',
		    'description'       => 'Content of the message.',
		    'sensitive'         => true
		]
	];
}
```
