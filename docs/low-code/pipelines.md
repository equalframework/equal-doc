
# Pipeline Editor Documentation

## What is a Pipeline?

A **pipeline** is a sequence of connected operations used to process and transform data step by step. Each operation is represented by a component called a **Controller**, which performs a specific task and passes its result to the next component in the chain. This concept enables developers to design and implement algorithms visually and modularly.

In the Pipeline Editor, part of the Workbench App, you can build these pipelines using a graphical interface, eliminating the need for writing boilerplate code manually. It simplifies development, enhances accuracy, and makes data flow and logic easy to understand and modify.

Pipelines are especially useful in applications that need to process data in stages, transform structures, apply business logic, or coordinate a series of backend operations.

## Glossary

- **Controller**: A PHP script based on the eQual framework that performs a specific operation in the pipeline.
- **Node**: A visual block in the pipeline representing either a controller or a router.
- **Router**: A special node used to direct outputs from one or more controllers to one or more subsequent nodes.
- **Input parameters (input params)**: Values or configurations passed to a controller for its execution.
- **Output values**: The results produced by a controller after its execution.
- **Link**: A visual and logical connection between the output of one node and the input of another.

## Creating a Pipeline

### Add a Controller

To add a node to the pipeline:

1. Click the '+' button in the top-right corner.
2. A list of available controllers will appear.
3. Use the search or filters to find your desired controller (based on type or package).
4. You can also add a **Router** node from this menu.

![Add Controller](/_assets/img/pipeline_add_controller.png)

### Create a Link

To create a connection between nodes:

1. Click the connector button on the **source** node.
2. Then click the connector on the **target** node.

![Create Link](/_assets/img/pipeline_create_link.png)

### Edit an Element

#### Edit a Node

- Click a node to see its options.
- You can:
  - Delete the node.
  - Rename it and update its description.
  - Change its appearance (icon, color).
  - View or set input parameters from the **Data** tab.

If the node is a **Router**, the right panel will show which nodes feed into it.

![Edit Node](/_assets/img/pipeline_edit_node.png)

#### Edit a Link

- Click a link to configure or delete it.
- On the right panel, you can map the output from the source to the input of the target.
- If the target is a router, the input field will remain empty.

![Edit Link](/_assets/img/pipeline_edit_link.png)

### Save, Load, and Run a Pipeline

- Click the **Save** button to store your pipeline.
- Click **Load** to retrieve an existing pipeline.
- Use the **Run** button to execute the pipeline and view logs in real time.

![Save Load Run](/_assets/img/pipeline_save_load_run.png)
![Modal Run](/_assets/img/pipeline_modal_run.png)


# Pipeline Output Specification (Detailed)

This section explains in detail how controller **responses** are defined in a pipeline. Each controller declares the format and structure of the **output values** it produces, which helps the frontend interpret data and chain it to subsequent nodes.

---

## Terminology

- **Input Parameters (input params)**: Configuration values passed to a controller at runtime.
- **Output Values**: The data returned by a controller, which can be used by the next node in the pipeline.

---

## Response Signature

Each controller declares its response using the following signature:

```php
"response" => [
  "content-type" => "application/json",
  "schema" => [
    "type"   => "entity" | "any" | scalar type,
    "usage"  => "{usage notation}", // optional
    "qty"    => "one" | "many",
    "entity" => "core\User",       // required if type is 'entity'
    "values" => []                  // required if type is 'any'
  ]
]
```

---

## Response Types

### 1. Scalar Response

A scalar response returns a primitive value such as a string, integer, boolean, or float.

#### Example

```php
"schema" => [
  "type" => "string",
  "qty"  => "one" // or "many" if it returns an array of strings
]
```

- `type`: a primitive PHP type (`string`, `integer`, `boolean`, etc.).
- `qty`: `"one"` if it's a single value, `"many"` if it's an array.

#### Use Case

Used when the output is a basic data type such as the result of a count, ID list, or status flag.

---

### 2. Entity-Based Response

Returns an object from the ORM, represented as an entity class.

#### Example

```php
"schema" => [
  "type"   => "entity",
  "qty"    => "one",         // or "many" for multiple entities
  "entity" => "core\User"
]
```

- `entity`: specifies the ORM class that represents the data.

#### Notes

- **No nested objects**: Only IDs of sub-objects are returned by default.
- This keeps the payload light and allows the frontend to decide if it wants to fetch full details later.

#### Use Case

Used when you return user, order, or domain-specific entities directly.

---

### 3. Arbitrary or Custom Object Response

Returns a structured JSON object not bound to ORM entities.

#### Example

```php
"schema" => [
  "description" => "custom address",
  "type" => "any",
  "qty"  => "one",
  "values" => [
    "code" => [
      "type" => "integer",
      "usage" => "number/integer{0,100}",
      "description" => ""
    ],
    "name" => [
      "type" => "string",
      "usage" => "text/plain:64",
      "description" => ""
    ],
    "choice" => [
      "type" => "string",
      "selection" => ["a", "b", "c"],
      "description" => ""
    ]
  ]
]
```

- `type`: must be `any`.
- `values`: a map describing the structure of the returned object.
  - each field has its own type, usage constraints, and optional description.

#### Use Case

Used for complex results such as transformed data, grouped statistics, or configuration-like outputs.

#### Tip

Providing the `values` field allows the frontend to auto-fill compatible input params for chained controllers.

---

## Examples

### model_collect

```php
"schema" => [
  "type" => "any",
  "qty"  => "many"
]
```

- Returns an array of arbitrary JSON objects.

### model_search

```php
"schema" => [
  "type" => "integer",
  "qty"  => "many"
]
```

- Returns an array of integers, often used for IDs.

---

## Output Schema Summary

Each controller must clearly define its response to enable the frontend to visualize, validate, and link data flows.

- **Scalar**: lightweight values.
- **Entity**: ORM-based outputs (only IDs of sub-entities).
- **Custom (any)**: JSON objects with optional schema hints for better interoperability.

> The clearer your response schema is, the more dynamic and powerful your pipeline becomes.

## Summary

The Pipeline Editor revolutionizes how developers design, connect, and execute logical operations within an application. By abstracting the complexity of code into a clean and visual format, it fosters rapid development and reduces errors.

- Compose algorithms visually.
- Integrate controllers and routers.
- Manage input/output relationships.
- Test and debug workflows seamlessly.

> The more consistent and descriptive your controller responses are, the more powerful and dynamic your pipelines become.
