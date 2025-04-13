# Settings & envinfo

The **eQual** framework allows defining a series of parameters used by controllers that can be modified by administrators through the **Settings** application. These parameters are modeled using the `Setting` entity, which provides flexibility and a robust mechanism for contextual configuration.


## Configuration Parameters

Each parameter is represented by a `Setting` entity. A setting can have one or more associated `SettingValue` or `SettingSequence` objects. These values may include:

- A general value applicable to all users.
- A user-specific or context-specific value (e.g., via `user_id`, `organization_id`, etc.).
- A counter or sequential value (for sequences).

### Example of a Setting object
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

### Auto-Generated Interface
The framework automatically generates an interface to manage `Setting` entities. This ensures:

- Consistency in data types.
- Proper controls for editing (e.g., select, text, etc.).
- Clear separation between parameters and their values.

### Interdependencies and Initialization

Parameters can include relational fields or computed fields based on references. These relationships can sometimes require certain entities to be created before others. To address this:

- The initialization order is enforced by naming files in the `init/data` directory with numeric prefixes (e.g., `01_file.json`, `02_file.json`).

#### Special Case: SettingValue
- Each `SettingValue` is always linked to a specific `Setting`.
- Direct use of `setting_id` can risk collision between packages, making maintenance complex.
- To avoid this:
  - During initialization, the framework uses the `name` field set in `SettingValue` objects to find the corresponding `setting_id`.
  - A warning is generated if a `setting_id` is manually specified.
  - If the `Setting` cannot be resolved, an error is raised.

### Automatic Adjustments
- Tests and adaptations are performed during the initialization process using files in the `init/data` directory.
- The system ensures consistency by identifying missing or misconfigured settings and resolving issues automatically where possible.

---

## Core Concepts

A **Setting** represents a configurable parameter in the system, uniquely identified by a `package`, a `section`, and a `code`. Each setting must be linked to **at least one value or sequence** to be considered valid.

### SettingValues
**SettingValues** hold the actual value of a setting. These values can be **scoped** to specific contexts using a **selector** — a set of fields like `user_id`, `organization_id`, etc.

Examples:
- A setting can have a global default value (no selector).
- And specific overrides for a user or an organization.

### SettingSequences
**SettingSequences** are special settings that manage numeric counters (e.g., invoice numbers). They also support contextual scoping through selectors, allowing, for instance, separate counters per organization.

---

## Selector Hierarchy

The **selector** defines the *context* of a value or sequence:

- If **empty**, it targets the most generic (global) value.
- If **non-empty**, it must match a value with **exactly** those fields set (and all other discriminant fields must be `null`).
- Fields omitted in the selector are considered implicitly `null`.

This strict matching ensures **clarity and avoids ambiguity** when retrieving values.

The list of discriminant fields (called **context keys**) is configurable and defines the scoping logic (e.g., per user, org, etc.). You may override the `getSelectorKeys()` method in subclasses to adapt this behavior.

---

## Core Entities

### `Setting`
- Represents a unique configuration parameter, defined by its `package`, `section`, and `code`.
- A `Setting` must have **at least one** associated `SettingValue` or `SettingSequence`.

### `SettingValue`
- Stores the actual value for a `Setting`, with optional scoping using discriminant fields (e.g., `user_id`, `organization_id`).
- Can be multilingual, depending on the `Setting`'s `is_multilang` flag.

### `SettingSequence`
- Provides integer-based sequential values for a given `Setting`, used when auto-incrementing or unique numbering is required.
- Also supports contextual scoping like `SettingValue`.

---

## Common Method Patterns

The following methods are implemented both for `SettingValue` and `SettingSequence`, using respective suffixes (`_value`, `_sequence`).

### `assert_value($selector, $val)` / `assert_sequence($selector, $val)`
- Ensures the `Setting` and the related `SettingValue` or `SettingSequence` exist.
- If the `Setting` does not exist, it is created.
- The target value is then set via `set_value()` or `set_sequence()`.

### `set_value($selector, $val)` / `set_sequence($selector, $val)`
- Sets the actual value (mixed for `value`, integer for `sequence`) for the specified selector and language (if applicable).
- If the targeted entity does not exist, the operation is **ignored**.

### `get_value($selector, $default)` / `get_sequence($selector, $default)`
- Retrieves the value or sequence.
- Returns the default if the `Setting` or corresponding entity is missing.

### `create_value($selector)` / `create_sequence($selector)`
- Creates a new `SettingValue` or `SettingSequence` based on the selector.
- Ensures the scoping rules are respected.

### `fetch_and_add($selector)` *(only for SettingSequence)*
- Retrieves the current integer value and increments it atomically.
- Useful for generating unique sequential values.

---

## Caching

- A dedicated cache (`$_equal_core_setting_cache`) is used to store retrieved values.
- `get_cache()` and `set_cache()` handle cache access.
- Cache index is automatically computed from `package`, `section`, `code`, selector fields (in `getSelectorKeys()` order), and language.
- The method `build_cache_index(...)` is responsible for building the index key.

---

## Examples

```php
// Get a setting value for user_id 1
$value = Settings::get_value('billing', 'defaults', 'currency', 'EUR', ['user_id' => 1]);

// Assert a default setting (global)
Settings::assert_value('billing', 'defaults', 'currency', 'EUR');

// Fetch and increment a sequence
$nextNumber = Settings::fetch_and_add('invoicing', 'sequence', 'invoice_number', ['organization_id' => 5]);
```

---

## Extensibility

- You may override the `getSelectorKeys()` method in subclasses to add or modify discriminant fields.
- This allows greater flexibility for scoping by team, device, project, etc.
- Ensure database-level unique constraints match your scoping logic (e.g., `UNIQUE(setting_id, user_id, organization_id)` where relevant).

---

## Notes

- `set_value()` does not create new entries — it only updates existing ones.
- `assert_value()` ensures creation and setting.
- Fallback logic (e.g., from user to org to global) is **not** currently implemented — each selector must match **exactly**.

This structured and dynamic approach helps maintain a consistent configuration model, while allowing per-context overrides and automated sequence generation — all while preventing ambiguity and ensuring system integrity.

