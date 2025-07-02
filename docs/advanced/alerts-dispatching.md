# Alerts Dispatching

## Overview

The alert system exposes a structured way to notify users or groups when a situation requires attention. Alerts are managed through the `Dispatcher` service and stored as `Message` instances bound to a `MessageModel`.

## Alerts Lifecycle

### Message Model (`core\alert\MessageModel`)

Defines the semantics of an alert type:

- `name` – internal identifier
- `label` and `description` – multilang text describing the alert
- `type` – optional tag for logical grouping
- `messages_ids` – references all alert instances for this model

This registry of alert types ensures consistency across the system.

### Message (`core\alert\Message`)

Represents an emitted alert linked to a specific object. It contains:

- `object_class` and `object_id` – target of the alert
- `message_model_id` – reference to the alert model
- `severity` – one of `notice`, `warning`, `important`, `error`
- `controller` – optional fallback controller to re-evaluate the condition
- `params` / `links` – JSON payload with controller input or related resources
- `user_id` / `group_id` – optional recipient scoping

Alerts are persisted through the ORM, and a uniqueness constraint ensures one alert per `(object_class, object_id, message_model_id)` tuple.

## Dispatcher Service (`equal\dispatch\Dispatcher`)

The `Dispatcher` service is responsible for creating, cancelling and managing alert messages.

### Methods

#### `dispatch($message_model, $object_class, $object_id, $severity, $controller, $params, $links, $user_id, $group_id)`

Creates a new alert **only if none exists** for the target object and model.

1. Look up `MessageModel` by name.
2. Check for existing messages targeting the same object and model.
3. Insert a new `Message` record when none is found.
4. Optionally reset the `alert` field of the object if defined.

This operation is idempotent and prevents alert duplication.

#### `cancel($message_model, $object_class, $object_id)`

Removes any existing alert matching the given model and object reference without invoking a controller.

#### `dismiss($id)`

Deletes a specific `Message` and re-runs its controller (if set) for reevaluation. Typically used when a user dismisses an alert through the UI.

## Use Case: Meal Preferences Assignment Check

The controller `sale_booking_check-mealprefs-assignments` verifies consistency between the number of participants (`nb_pers`) and the quantity of assigned meal preferences (`meal_preferences_ids.qty`) for a `Booking`.

1. **Booking is loaded** via its ID.
2. Each `booking_lines_group` of type `sojourn` or `event` is evaluated.
3. If a mismatch is detected, a `warning` alert is dispatched for the model `lodging.booking.mealprefs_assignment`:

```php
$dispatch->dispatch(
    'sale.booking.mealprefs_assignment',
    'sale\booking\Booking',
    $params['id'],
    'warning',
    'sale_booking_check-mealprefs-assignments',
    ['id' => $params['id']],
    [],
    null,
    $booking['center_office_id']
);
```

HTTP status is set to `403`.

4. If everything matches, any existing alert is cancelled:

```php
$dispatch->cancel('lodging.booking.mealprefs_assignment', 'sale\booking\Booking', $params['id']);
```

A `200` status with an empty body is returned. This keeps alerts symmetrical: raised on inconsistency, removed on resolution.


!!! note "Additional Notes"
    - The `alert` field, if defined on a model, is automatically cleared on dispatch.
    - Alerts can be listed or filtered in the UI for a given object or user context.
    - Priority of alerts is implicitly derived from severity and age.



## Check Controllers and Alert Integration

### Purpose

To validate business-specific consistency rules, the eQual framework provides a standard mechanism through **check controllers**, which are a special category of controllers extending `model_check`.

These controllers are designed to operate either interactively or programmatically and act as the primary mechanism for triggering and resolving alerts.

### Characteristics

A **check controller** has the following properties:

* **Entity-specific**: Always scoped to a given entity (based on its namespace) and bound to a specific object (`id`) or list of objects (`ids`).
* **Callable independently**: Can be executed via API, scheduled job, action hook, or alert resolution trigger.
* **Structured response**:

  * Returns a standard HTTP status code (`200` if no error, or appropriate error status otherwise).
  * Returns a JSON associative array mapping object IDs to a list of error codes (corresponding to `MessageModel.name` values).
* **Alert-aware**: Can **create or cancel alert messages**, regardless of whether the result is actively used.
* **Integrated logic**:

  * Can be invoked from within an action (via `getActions()`), controller transitions (`onbefore`, `onafter`), or custom logic.
  * The return value can be interpreted to raise an exception if business rules are violated.
* **Symmetrical logic**: A single check controller is responsible for both **raising** and **clearing** alerts, ensuring consistent lifecycle management.

### UI and User Feedback

* In the front-end, error codes returned by check controllers can be translated using the associated `MessageModel` definitions (multilang `label` and `description`).
* This allows seamless integration between backend consistency checks and user-visible warnings.

### Alert Re-evaluation via Controller

Each `Message` instance may optionally reference the controller that created it. This enables:

* **Re-evaluation of alert state**: The system can re-run the associated controller (with preserved `params`) when a user dismisses the alert or upon certain events.
* **Stateless verification**: The check controller remains the single source of truth for whether a situation still justifies the presence of an alert.


!!! note "1 Alert = 1 Object "
    Alerts are always bound to one and only one object and are managed by their related check controller.

