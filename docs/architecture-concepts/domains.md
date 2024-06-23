# Domains

In eQual, **conditional domains** play a crucial role in condition-matching operations and filtering. 

A **Domain** is essentially a structure that defines a set of conditions used to create filtered lists of objects or to determine the boolean value of a flag depending on a given context.

Domains are mostly used either for visibility (Views) or filtering (Models and Controllers).



## Concept definition

The purpose of a conditional domain is to define a logical filter targeting all objects that comply to a series of criteria.

To represent such a filter, we take advantage of the fact that, whatever the logical configuration, it is always possible to convert it to a list of operands on which the OR operator is applied (disjunction).

Here below are some mathematical equivalences to illustrate this (`∨` = OR, `∧` =  AND):

```
(A ∨ B) ∧ (C ∨ D) = (A ∧ C) ∨ (A ∧ D) ∨ (B ∧ C) ∨ (B ∧ D)
```

In turn, each operand can be either a direct value or a conjunction of operands (logical AND).

In other words, a domain is a structure that represents a list of "disjunctions (OR) of conjunctions (AND)".

Based on that fact, it is possible to build a domain in an incremental way.

To help with domain manipulations, eQual offers a `Domain` class (`lib\equal\orm\Domain.class.php`) that both holds helper methods and allows to instantiate OOP modelization of domains.



## Structure

A domain is a list of one or more clauses, each clause consisting of one or more conditions.

The minimal domain being a single clause with a single condition.

### Condition

A condition is represented as an array holding 3 elements:

```
[ operand, operator, value ]
```

| **PART**  | **ROLE**                            |
| --------- | ----------------------------------- |
| operand   | Indicates the field on which the condition must be applied and matches one of the properties returned by `Model::getColumns()`. |
| operator  | Provides the operator of the condition. Accepted operators are : `=`, `<`, `>`,` <=`, `>=`, `<>`, `like`, `ilike` (case-insensitive), `in`, `contains`. |
| value  | Provides the value against which the operator must be applied on the operand. |

!!! note "About value type"
    The operator influences the type of the value (e.g.: 'in' implies that the value is an array).


### Clause

A clause is a series or conditions on which the AND operator is applied.

```
[ [ operand1, operator1, value1 ], [ operand1, operator1, value2 ] ]
```



For instance, the below notation can be interpreted as "*field1 must be equal to 1 AND field2 must be equal to 2*":

```
[ [ "field1", "=" , 1 ], [ "field2", "=", 2 ] ]
```



## Notation

The complete domain syntax is : 

``` 
[ [ [operand, operator, value], {...} ], {...} ]
```



!!! note "Shortcut notations"
    eQual accepts shortcut notations : `[operand, operator, value]` and `[[operand, operator, value]]` (two pairs of square brackets) will both be interpreted as a domain with a single clause and a single condition.



Here is a commented recap of the syntax : 


```php
<?php
$domain = [                                        // domain
	[                                              // clause
		[
			'{operand}', '{operator}', '{value}'   // condition
		],
		[
			'{operand}', '{operator}', '{value}'   // another contition (AND)
		]
	],
	[		                                       // another clause (OR)
		[
			'{operand}', '{operator}', '{value}'   // condition
		],
		[
			'{operand}', '{operator}', '{value}'   // another contition (AND)
		]
	]
];
```



One advantage of using the array notation is that the syntax is very similar in PHP controllers and in JSON views.

**PHP:**

```php
<?php
$domain = [
	["login", "like", "%@equal.run"],
	["validated", "=", true]
];
```
**JSON:**

```json
{
	"domain": [
        ["login", "like", "%@equal.run"],
        ["validated", "=", true]
    ]
}
```



Domains can be used in URL (must be URL encoded).

Example:
```http
http://equal.local/?get=model_search&entity=core\User&domain=[[id,in,[1,2,3]]
```

## References

A few references can be used in the domain to improve the search.

| **REFERENCE** | **DESCRIPTION**     | **EXAMPLE**                                  |
| ------------- | ------------------- | -------------------------------------------- |
| `object.`     | The current object. | `["object_id", "=", "object.id"]`            |
| `user.`       | The current user.   | `["creator", "=", "user.id"]`                |
| `date.`       | The current date.   | `["date_from", "=", "date.this.year.first"]` |



### Object Reference

In a Model or a View, a domain might refers to the current object. This feature is particularly useful when it comes to filtering which objects can be assigned to the current object based on the context.

**Example:**

Let's say we have two entities: **Project** and **Techie**.

- A **Project**  depends on the company but is assigned to a specific department.
- One or more **Techies** can be assigned to a project, but only amongts those who belong to the same department as the project.

Here is how the domain might look in eQual:

```json
{
    "techies_ids": {
        "type": "one2many",
        "domain": ["department_id", "=", "object.department_id"]
    }
}
```

In a View form for editing Project, the techies not belonging to the same departement of the project will be filtered out.




### User Reference

Domains can also use the current user as reference . The current user is the user from which originates the current processing.

The available fields are : 

* id (identifier of the user)
* login
* validated
* language
* groups_ids (list of groups identifiers)
* groups (list of groups names)



### Date Reference

eQual provides a structured way to reference dates allowing to describe and retrieve any date based on the current date, which can be used in Domains for filtering or conditioning visibility. 

Date References allow to reference dates relatively to various intervals and dynamically compute dates for a wide range of scenarios by combining methods and parameters.



A **Date Reference** descriptor is built using the following structure : 

```
date.<origin>(<offset>).<interval>.<method>(<arguments>)
```

or, in a more explicit notation : 

```
date.{this|prev|next}[(<offset>)].{day|week|month|quarter|semester|year}.{first|last|get(reference:index)}
```



### Attributes and Arguments

#### 1. Origin

| Method  | Description                                                | Offset | Possible Values   |
| ------- | ---------------------------------------------------------- | ------ | ----------------- |
| prev(n) | First day of previous period with an offset of n intervals | n > 0  | `prev`, `prev(n)` |
| next(n) | First day of next period with an offset of n intervals     | n > 0  | `next`, `next(n)` |
| this()  | First day of current period                                | n = 0  | `this`, `this(0)` |

#### 2. Interval

| Interval | Description |
| -------- | ----------- |
| day      | Day         |
| week     | Week        |
| month    | Month       |
| quarter  | Quarter     |
| semester | Semester    |
| year     | Year        |

#### 3. Method

| Method  | Description                        |
| ------- | ---------------------------------- |
| first() | First day of the interval          |
| last()  | Last day of the interval           |
| get()   | Get a specific date with arguments |



### Tables of Possible Values

#### Origin

| Method              | Possible Values           |
| ------------------- | ------------------------- |
| `prev(<increment>)` | `prev`, `prev(n)` (n > 0) |
| `next(<increment>)` | `next`, `next(n)` (n > 0) |
| `this()`            | `this`, `this(0)`         |

#### Interval

| Interval   | Description |
| ---------- | ----------- |
| `day`      | Day         |
| `week`     | Week        |
| `month`    | Month       |
| `quarter`  | Quarter     |
| `semester` | Semester    |
| `year`     | Year        |

#### Method

Methods can be applied on the chosen interval (except for `day`, for which the method has no effect).

| Method  | Description               |
| ------- | ------------------------- |
| first() | First day of the interval |
| last()  | Last day of the interval  |
| get()   | Get a specific date       |

#### Arguments for the `get()` Method

| Argument (reference:index) | Concerned Interval             | Possible Values                                              |
| -------------------------- | ------------------------------ | ------------------------------------------------------------ |
| day:<number>               | month, quarter, semester, year | Month: `day:1` to `day:31`<br>Year: `day:1` to `day:365`     |
| week:<number>              | month, quarter, semester, year | `week:1` to `week:52` (or `week:53`)                         |
| <day_of_week>:first        | month, quarter, semester, year | `monday:first` to `sunday:first`                             |
| <day_of_week>:last         | month, quarter, semester, year | `monday:last` to `sunday:last`                               |
| <day_of_week>:<number>     | month, quarter, semester, year | `monday:1` to `monday:5` (month)<br>`monday:1` to `monday:14` (quarter)<br>`monday:1` to `monday:26` (semester)<br>`monday:1` to `monday:52` (or `monday:53`) (year) |

### Usage Examples

* **today**

    ```
    date.this.day
    ```

* **Seven days from now**

    ```
    date.next(7).day
    ```

* **First day of the month 5 months before the current month:**

    ```
    date.prev(5).month.first()
    ```

* **Last day of the quarter 2 quarters after the current quarter:**

    ```
    date.next(2).quarter.last()
    ```

* **34th week of the next year:**

    ```
    date.next(1).year.get(week:34)
    ```

* **First Monday of the semester 3 semesters before the current semester:**

    ```
    date.prev(3).semester.get(monday:first)
    ```

* **Second Wednesday of the month 4 months after the current month:**

    ```
    date.next(4).month.get(wednesday:2)
    ```

* **first day of current year:**

    ```
    date.this.year.first
    ```

    or

    ```
    date.this.year.get(day:1)
    ```



