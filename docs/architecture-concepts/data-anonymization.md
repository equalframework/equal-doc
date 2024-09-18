# Data anonymization


Data anonymization is a process that transforms personal data to make it non-identifiable. This means modifying the data so that the individuals or entities it refers to can no longer be identified, either directly or indirectly.

This process is crucial for compliance with modern regulations (GDPR, CCPA, LGPD, PIPEDA, PDPA, ...) and ensures the protection of individuals' privacy, while allowing the use of data for analytical or commercial purposes without violating privacy rules.

In eQual, data anonymization is irreversible and primarily based on randomization: data values are modified randomly, using datasets selected based on each field's descriptor, leveraging its type, usage (when available), and, if applicable, its name.

This approach offers several benefits:

1. **Data security**: Companies that anonymize data significantly reduce the risks of data leaks or malicious exploitation, as the information can no longer be attributed to specific individuals.
2. **Data reuse**: Anonymization allows the reuse of data without breaching data protection rules, which is particularly useful in sectors like healthcare, where sensitive data can be used for statistical or epidemiological studies.
3. **Customer trust**: Users are more likely to share their data with companies that commit to anonymizing and protecting it, strengthening trust and improving the company's reputation.
4. **Analytical flexibility**: Anonymized data can be used for market studies, behavioral analysis, and other research while respecting individual rights, opening new opportunities for companies to leverage their data without legal constraints.



eQual allows both the anonymization of specific objects of a given entity, filtered using a domain; or all the objects of a given package.
In the latter case, a schema can be used to specify the strategies for anonymizing the data.



## Entity schema

At the level of field descriptors, as defined in `getColumns()`, certain specific attributes define the behavior of the anonymization:

* **sensitive**: When set to `true`, the field is always anonymized (even if not listed among the fields).

* **generation**: Specifies how the field's values should be generated when it is anonymized. Must refer to a static method of the targeted entity (without parameters).

A Model field can be anonymized automatically (without having to specify it in the `fields` option) by setting its sensitive option to true. 


```php
public static function getColumns() {
	return [
        'content' => [
			'type'              => 'string',
		    'description'       => 'Content of the message.',
		    'sensitive'         => true
		]
	];
}
```



## Anonymization schema

An anonymization schema is a JSON array where each object specifies a model to be anonymized, including the fields and relationships to be processed.

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




| **Attribute** | **Description**                                              |
| ------------- | ------------------------------------------------------------ |
| **name**      | Specifies the fully qualified name of the model class to be seeded. *(required)* |
| **fields**    | Contains fields that need to be anonymized.                  |
| **relations** | Defines relationships to other models and how to handle them. |






## Init anonymize

The action controller `core_init_anonymize` allows to anonymize objects for a specific package.

It uses json configuration files as anonymization schemas.

When present, those schemas are located in `{package}/init/anonymize/`.

**Example : **

```bash
$ ./equal.run --do=init_anonymize --package=core
```



**Action params:**

| **Param**   | **Description**                                                           |
|-----------------|---------------------------------------------------------------------------|
| **package**     | The name of the package to anonymize.                                     |
| **config_file** | If given, only the specified config file will be considered.              |





## Model anonymize

The action controller `core_model_anonymize` allows to anonymize existing objects of a specific entity for a given domain. This action is used by the init anonymize action to anonymize the objects.

**Example : **

```bash
$ ./equal.run --do=model_anonymize --entity='core\User' --fields=[firstname,lastname]
```



**Action params:**

| **Param**   | **Description**                                                           |
|-----------------|---------------------------------------------------------------------------|
| **entity** | The name of the package to anonymize.                                     |
| **fields** | If given, only the specified config file will be considered.              |
| **relations** | Descriptor for handling the object relations (many2one and one2many). |
| **lang** | Specific language for multilang field. |
| **domain** | Criteria that results have to match (series of conjunctions). |

