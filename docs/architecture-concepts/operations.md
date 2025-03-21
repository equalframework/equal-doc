# Operations

The **operations** in eQual allow to define **aggregation or computation logic** to be applied on one or more fields of the records being displayed (typically in a list view). 

These operations are useful for **showing totals, averages, counts, or custom calculations** on the dataset.

### Operation types

There are two types of operators:

**1) unary operators**

```php
<?php
/**
 * Unary Operators a single operand (either a single value, or a field reference [array of values])
 * @var array
 */
private static $unary_operators = ['ABS', 'AVG', 'COUNT', 'DIFF', 'MAX', 'MIN', 'SUM'];
```

**2) binary operators**

```php
<?php
/**
 * Operators that take two operands (each operand can be an Operation, a single value, or a field reference [array of values])
 * @var array
 */
private static $binary_operators = [
    '+',            // addition
    '-',            // subtraction
    '*',            // multiplication
    '/',            // division
    '%',            // modulo
    '^'             // exponentiation
];
```


### Operation Syntax

```
Unary operators : [ OPERATOR, {FIELD | OPERATION} ]
```
OR
```
Binary operators : [ OPERATOR, {FIELD | OPERATION}, {FIELD | OPERATION} ]
```



## Where Can Operations Be Used?

Operations can be defined at **three different levels** within a list view configuration:

### 1. As a Simple Field in `group_by`

This is used to group results and can optionally apply an operation to that field.

**Example:**

```json
"group_by": ["date"]
```



### 2. As an Object Inside `group_by`

You can use an object to apply an operation to a specific field while adding additional options like sort order.

**Example:**

```json
"group_by": [
  {
    "field": "time_slot_id",
    "operation": ["SUM", "object.qty"],
    "order": "order"
  }
]
```

#### `operations` Attribute Inside `group_by` Object

The `operations` attribute (plural) lets you define **multiple operations** per group, mapping them to specific fields.

**Example:**

```json
"group_by": [
  {
    "field": "ownership_id",
    "colspan": 2,
    "operations": {
      "called_amount": {
        "operation": "SUM",
        "usage": "amount/money:2"
      }
    }
  }
]
```

> 💡 This adds flexibility, enabling multiple computed values per group with specific formatting.



### 3. In the `operations` Property (Global)

This section defines **named rows of operations**, independent from groupings. Each row can contain one or more named calculations.

**Example:**

```json
"operations": {
  "total": {
    "total_paid": {
      "id": "operations.total.total_paid",
      "label": "Total received",
      "operation": "SUM",
      "usage": "amount/money:2"
    },
    "total_due": {
      "operation": "SUM",
      "usage": "amount/money:2"
    }
  }
}
```



## Operation Syntax

Operations follow a functional-style array syntax that supports both unary and binary operators.

**Unary operator:**

```json
["SUM", "object.qty"]
```

**Binary operator:**

```json
["DIV", ["SUM", "object.total"], ["COUNT", "object.id"]]
```



## Operation Object Format

Each operation supports the following properties:

- **`operation`**: Operator or nested operation array
- **`usage`**: Output format (e.g., `amount/money:2` for 2-decimal currency)
- **`label`**: Display label (optional, used in UI)
- **`id`**: Translation key (optional, used for i18n)



## Summary 

| Level                   | Example                                                      | Description                                   |
| ----------------------- | ------------------------------------------------------------ | --------------------------------------------- |
| `group_by` (simple)     | `"group_by": ["date"]`                                       | Basic grouping                                |
| `group_by` (object)     | `{ "field": "product_id", "operation": ["SUM", "object.qty"] }` | Group with aggregation                        |
| `group_by[].operations` | `{ "called_amount": { "operation": "SUM" } }`                | Field-level operations in a group             |
| `operations` (global)   | `"operations": { "total": { ... } }`                         | Aggregated values in named rows (like totals) |



