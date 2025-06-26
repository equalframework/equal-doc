# Workflows

Workflows are part of the Model definition.

When a workflow must be assigned to an entity, a dedicated method, `getWorkflow()`, must be defined for describing the workflow.

The `getWorkflow()` method must return an associative array in which all possible statuses are identified by a key of the map.

For a given entity, the workflow reflects all possible values of the special field `status` (e.g. \[`validated`, `suspended`, `confirmed`]), along with all possible transitions from one status to another (e.g. \[`validate`, `suspend`, `confirm`]).

### Example

```php
<?php
public static function getWorkflow() {
        return [
            'created' => [
                'transitions' => [
                    'validate' => [
                        'watch'       => ['validated'],
                        'domain'      => ['validated', '=', true],
                        'description' => 'Update the user status as validated.',
                        'status'      => 'validated',
                    ]
                ]
            ],
            'validated' => [
                'transitions' => [
                    'suspend' => [
                        'description' => 'Set the user status as suspended.',
                        'status'      => 'suspended'
                    ],
                    'confirm' => [
                        'domain'      => ['validated', '=', true],
                        'description' => 'Update the user status as confirmed.',
                        'status'      => 'confirmed'
                    ]
                ]
            ],
            ];
    }
```

* By default, no workflow is defined.

* The workflow assumes the existence of a 'status' field. Only values defined in the selection property of the status field can be assigned to it (and referenced in the workflow). The initial value must be specified in the default property.

* There can be only one workflow per class (a behavioral change implies a change in logic, justifying the creation of a new class, potentially in a new package).

* A workflow can be overwritten or overridden by inheritance.

* The workflow allows adding constraints to the fields of an entity (readonly, required, visible) based on the status.
  (canupdate ORM hook: automatic verification of the possibility to modify an object based on the status. In the workflow, at each state, the possibility to add a list of read-only fields (non-modifiables).

* The status field can have a dependencies property to force recalculation of computed fields when the value is modified.

* In the UI, the possible transitions from the current state are visible in the transition menu, actions on the right of the current status.

* If a getWorkflow method is defined, the schema returned by getSchema is possibly adapted based on the current status.

* The initial status is defined in the default property of the status field definition.

* The final status (if any) is the status for which there is no outgoing transition.



## Workflow Graph

* The workflow is composed of nodes that represent "statuses" (successive logical states through which an entity can pass).
* The nodes are connected to each other by vectors that represent "transitions."
* There can be multiple outgoing and incoming transitions on the same status.

If transitions are automatic based on external events, the object needs to be constantly monitored.
Transitions are automatic status changes based on modifications of object field values.

If transitions are actions, the possibility to move from one status to another (action) needs to be conditioned based on the graph.
Only actions available from a specific status can be activated.

!!! note "Transition automated trigger"
    Transitions can be activated either by complex actions (external) or by modifications of the object.

The idea is to allow both "manual" modification of the status (i.e., by the user or controller) by validating these modifications based on a logical schema, and to perform automatic transitions based on pre-established conditions.

The status influences the fields that can be modified on the object, the display of the object, and the accessible statuses.

```json
{
  "status_name_A": {
    "readonly": {},
    "transitions": {
      "transitionX": {
        "watch": ["field1", "field3"],
        "domain": ["field1", ">", "field3"],
        "policies": ["policy1", "policy2"],
        "description": "f1 > f3",
        "status": "status_name_B"
      },
      "transitionY": {
        "watch": ["has_quotation_sent"],
        "description": "has_quotation_sent",
        "status": "status_name_C"
      },
      "transitionZ": {
        "description": "manual",
        "status": "status_name_D",
        "onbefore": "onbeforeTransitionC",
        "onafter": "onafterTransitionC"
      }
    }
  }
}
```

### Manual transitions

A) Controllers can directly modify the status of the object (permitted only if consistent with the workflow, status\_from, status\_to, and fulfilled conditions without any condition)

* Retrieve the workflow and look for the descriptor corresponding to the current status.
* Go through all existing transitions and filter among those whose status property matches the requested new status.
* If there is no match or if there is no transition with all the fulfilled conditions, an error is thrown.
* If there is a match without conditions or with all the fulfilled conditions, the status is updated. If there is a function property, a call is made with the current object.

B) A controller can invoke a transition on an object by emitting a "signal" (transition identifier): `ORM::transition(transition_id)`

* Retrieve the workflow and look for the descriptor corresponding to the current status.
* Search for the transition among those in the descriptor.
* If there is a match and any optional conditions are fulfilled, the status is updated. If there is a function property, a call is made with the current object.
* Otherwise, an error is thrown.

### Automated transitions

C) When modifying an object (all fields), a specific handler checks if conditions are met to automatically modify the status.

* Retrieve the workflow and look for the descriptor corresponding to the current status.
* Go through all existing transitions and filter among those whose depends\_on property contains any of the updated fields.
* If there is a match without conditions or with all the fulfilled conditions, the status is updated based on the status property.
* Otherwise, no action is taken.




## Object Lifecycle vs Business Workflow: `state` vs `status`

In **eQual**, two distinct concepts govern the evolution of an object:

## `state`: Technical Lifecycle (System-Level)

The `state` field describes the **technical lifecycle** of an object, independent of its business logic. It determines whether an object is currently active in the system and what level of interaction is allowed.

### Possible values:

| State      | Description                                                                                                                                  |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `draft`    | The object is being created or edited and is **not yet considered final**. It is excluded from most system operations and may be incomplete. |
| `instance` | The object is **active and usable** in the application. This is the default state for normal operations.                                     |
| `archive`  | The object has been **deactivated or logically deleted**. It is kept for record-keeping but excluded from active workflows and interactions. |

This field is managed internally by the framework and is essential for filtering, safety checks, and data lifecycle management.



## `status`: Business Workflow (Domain-Level)

The `status` field expresses the **functional or business logic progression** of an object. It models the steps of a business workflow specific to the object type (e.g., orders, publications, approvals).

### Recommended generic pattern:

```text
[created] → pending → validated
```

* `created` (optional): initial state upon instantiation, before submission
* `pending`: waiting for review, approval, or further action
* `validated`: object is confirmed, accepted, or finalized

Variants like `published`, `approved`, `rejected`, or `cancelled` can be added depending on the object type and use case.

> ⚠️ **Important:** Although both `state` and `status` may contain a value like `draft`, their meanings are **independent**:
>
> * `state: draft` → object is not yet usable or finalized at the system level
> * `status: draft` → object is in the early stage of its business process (e.g., not yet submitted)



## Best Practices

* Keep `state` values minimal and consistent across all object types.
* Use `status` to implement domain-specific workflows with clear transition rules.
* Avoid using the same labels in both `state` and `status` unless the distinction is clearly documented and enforced.
