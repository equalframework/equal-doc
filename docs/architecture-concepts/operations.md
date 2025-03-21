# Operations

Operations are determined processes to obtain a result, using operators to do so.

In eQual, there exists two types of operators:

#### 1) unary operators

```php
<?php
/**
 * Unary Operators a single operand (either a single value, or a field reference [array of values])
 * @var array
 */
private static $unary_operators = ['ABS', 'AVG', 'COUNT', 'DIFF', 'MAX', 'MIN', 'SUM'];
```

#### 2) binary operators 

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


#### Operation Syntax

```
Unary operators : [ OPERATOR, {FIELD | OPERATION} ]
```
OR
```
Binary operators : [ OPERATOR, {FIELD | OPERATION}, {FIELD | OPERATION} ]
```


**Examples:**

```json
{
    "name": "Fund Request Line Entry Lot",
    "description": "Interface for model definition classes.",
    "group_by": [
        {
            "open": true,
            "field": "ownership_id",
            "operation": ["SUM", "object.called_amount"],
            "operations": {
                "called_amount": {
                    "operation": "SUM",
                    "usage": "amount/money:2"
                }
            }
        }
    ],
    "operations": {
        "total": {
            "called_amount": {
                "operation": "SUM",
                "usage": "amount/money:2"
            }
        }
    },
```

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




In the charts views, if we want to display the `SUM` (unary operator) of a specific field : 

```json
"layout": {
        "entity": "lodging\\sale\\booking\\BookingLine",
        "group_by": "range",
        "range_interval": "month",
        "range_from": "date.this.year.first",
        "range_to": "date.this.year.last",
        "datasets": [
            {
                "label": "Repas CA TVAC",
                "operation": ["SUM", "object.total"],
                "domain": ["is_meal", "=", 1]
            },
            {
                "label": "Repas CA HTVA",
                "operation": ["SUM", "object.price"],
                "domain": ["is_meal", "=", 1]
            }
        ]
    }
```

In this example, we would have the `SUM`of the total field of all the `BookingLine objects`.

> NB: It also needs to match the domain : `["is_meal", "=", 1]`.

