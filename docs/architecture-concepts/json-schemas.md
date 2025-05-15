## JSON Schema Management in eQual

Schemas are used to define data exchange formats that comply with structured rules compatible with official standards. They enable both documentation and validation of data.
 The format used is JSON, following the [JSON Schema](https://json-schema.org/) validation specification, version **2020-12**.

This section describes the internal conventions used in eQual for storing, identifying, and resolving these schemas.



### 1. File Structure

JSON Schemas are stored in the `schemas/` folder of each functional package:

```
packages/
  <package>/
    schemas/
      <schema-name>.json
```

Examples:

- `packages/finance/schemas/bank-statement.json`
- `packages/identity/schemas/user.json`

At the core level, general schema-related logic is placed in:

```
core/schemas/
```



### 2. Schema Identifier (`$id`)

Each JSON Schema includes a unique `$id` field, following this URN-based format:

```
urn:equal:json-schema:<package>:<schema-name>
```

Examples:

```json
"$id": "urn:equal:json-schema:finance:bank-statement"
"$id": "urn:equal:json-schema:identity:user"
```

This identifier is used to reference and resolve schemas internally, including `$ref` targets.



### 3. Schema Resolution Controller

The following controller is responsible for retrieving a schema by its `$id`:

```
core/data/json-schema.php
```

Usage example:

```
?get=core_json-schema&id=urn:equal:json-schema:finance:bank-statement
```

This controller maps the URN to a file path based on the standard structure under `packages/<package>/schemas/`.



### 4. Resolution Rules

- Only schemas with a valid URN using the `urn:equal:json-schema:` prefix are supported.
- The `<package>` segment must match the name of the functional module.
- The `<schema-name>` must correspond to a `.json` file under the module's `schemas/` folder.
- Resolution is local; no external HTTP fetch is attempted.



### 5. Schema-Based JSON Validation

A dedicated controller is available to validate JSON data against a registered schema:

```
core/data/json-validate.php
```

#### Request

The controller expects a POST request with the following JSON body:

| Field  | Type            | Description                                                  |
| ------ | --------------- | ------------------------------------------------------------ |
| `id`   | string          | The `$id` of the schema (e.g. `urn:equal:json-schema:finance:bank-statement`) |
| `data` | object or array | The JSON payload to be validated                             |

**Example request body:**

```json
{
  "id": "urn:equal:json-schema:finance:bank-statement",
  "data": {
    "account": "BE123456789",
    "date": "2025-05-15",
    "entries": []
  }
}
```

#### Response

The response returns a `valid` boolean and, if the validation fails, an `errors` array containing details for each issue found.

**Example success response:**

```json
{
  "valid": true,
  "errors": []
}
```

**Example failure response:**

```json
{
  "valid": false,
  "errors": [
    {
      "path": "/date",
      "message": "String is not a valid date"
    },
    {
      "path": "/entries",
      "message": "Array must contain at least 1 item"
    }
  ]
}
```

#### Notes

- The schema is resolved using the same logic described in section 3, based on the URN format.
- Validation follows the JSON Schema 2020-12 specification.
- The `path` field uses JSON Pointer notation.