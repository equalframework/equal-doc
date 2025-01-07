# Application Parameters

The **eQual framework** allows defining a series of parameters used by controllers that can be modified by administrators through the "Settings" application. These parameters are modeled using the `Setting` entity, which provides flexibility and a robust mechanism for configuration.

## Configuration Parameters

Each parameter is represented by a `Setting` entity. A `Setting` can have one or more associated `SettingValue` objects. The values may include:
- A general value applicable to all users.
- A user-specific value identified via the `user_id` field.

**Example of a `Setting` object**

```json
{
    "id": 5,
    "code": "numbers.decimal_precision",
    "title": "Number of decimal digits",
    "package": "core",
    "form_control": "select",
    "section_id": 1,
    "description": "Number of decimal digits",
    "help": "Number of decimal digits to store for fields of type 'float'.",
    "type": "integer"
}
```

## Auto-Generated Interface

The framework automatically generates an interface to manage `Setting` entities. This ensures:

- Consistency in data types.
- Proper controls for editing (e.g., select, text, etc.).
- Clear separation between parameters and their values.

## Interdependencies

Parameters can include relational fields or computed fields based on references. These relationships can sometimes require certain entities to be created before others. To address this:

- The initialization order is enforced by naming files in the `init/data` directory with numeric prefixes (e.g., `01_file.json`, `02_file.json`).

### Special Case: `SettingValue`

For `SettingValue` entities, additional considerations are made:

- Each `SettingValue` is always linked to a specific `Setting`.
- Direct use of `setting_id` risks collision between packages, making maintenance complex.

To avoid these issues:

- During initialization, the framework uses the `name` field set in `SettingValue` objects to find the corresponding `setting_id`.
- A warning is generated if a `setting_id` is manually specified.
- If the `Setting` cannot be resolved, an error is raised.

#### Automatic Adjustments

- Tests and adaptations are performed during the initialization process using files in the `init/data` directory.
- The system ensures consistency by identifying missing or misconfigured settings and resolving issues automatically where possible.

This approach provides a structured and dynamic way to manage application settings while avoiding conflicts and ensuring system integrity.
