# View Generator



eQual leverages a **view generator** and **UI components framework** to create dynamic and structured user interfaces. The central part of the screen is managed by a **frame** that holds a **stack of contexts**, allowing for dynamic navigation and interaction with data.

When opening a view (e.g., by clicking on an item in a list), eQual creates a new context and pushes it onto the top of the stack. Switching back to a previous view pops the context off the stack. Apps define the initial bootstrap view, and menus structure a series of view descriptors with links to load corresponding contexts.


## Contexts

A **context** in eQualUI defines the target of a navigation or action within the application. It encapsulates the metadata needed to dynamically load a view for a given business entity, including its filter, rendering mode (`view`), and optionally its interaction type.

Contexts are used in:

* Action routes (`routes`, `actions`, `menus`)
* Error fallbacks (`on_error`)



### `context` Object Structure

| Property      | Type   | Required | Description                                                                   |
| ------------- | ------ | -------- | ----------------------------------------------------------------------------- |
| `entity`      | string | ✅        | Fully qualified name of the ORM entity (e.g., `finance\\accounting\\Journal`) |
| `domain`      | array  | ⛔        | Filtering logic, using `[field, operator, value]` or combined clauses         |
| `view`        | string | ✅        | Target view ID to load (e.g., `list.default`, `form.extended`)                |
| `type`        | string | ⛔        | Optional type to categorize context (e.g., `entry`, `detail`, `action`)       |
| `id`          | string | ✅        | Unique identifier for the context (used in routing and UI references)         |
| `label`       | string | ⛔        | Display label shown in UI                                                     |
| `icon`        | string | ⛔        | Material icon or custom icon name                                             |
| `description` | string | ⛔        | Optional short description of the context                                     |


!!!note "Additional Notes"
    * Contexts can use **dynamic variables** (e.g., `object.id`, `user.id`, etc.) inside their `domain` expressions.
    * The `view` must be available for the specified entity; if not, a fallback via `on_error` is recommended.
    * The context’s `id` must be unique globally across view definitions, especially if referenced in UI routes or menus.


### Contexts examples

#### Example 1: Filtered list of accounting journals
Displays the `list.default` view for the `Journal` entity, filtered to show only records not linked to any condo.

```json
{
    "id": "item.accounting.journals",
    "type": "entry",
    "label": "Journals",
    "icon": "auto_stories",
    "description": "",
    "context": {
        "entity": "finance\\accounting\\Journal",
        "domain": ["condo_id", "=", null],
        "view": "list.default"
    }
}
```

#### Example 2: Profile form for current user
Dynamically loads the profile form for the currently logged-in user.
```json
{
    "id": "context.user.profile",
    "label": "My Profile",
    "icon": "person",
    "context": {
        "entity": "core\\auth\\User",
        "domain": ["id", "=", "current_user_id"],
        "view": "form.profile"
    }
}
```

####  Example 3: Context used in a menu or action
Used in a dashboard or action to list all active subscriptions.
```json
{
    "id": "action.manage.subscriptions",
    "label": "Manage Subscriptions",
    "icon": "subscriptions",
    "context": {
        "entity": "crm\\Subscription",
        "domain": ["active", "=", true],
        "view": "list.admin"
    }
}
```


#### Example: Action route (non-contextual)
This route points directly to a file generation endpoint. It does not use a `context`, but could be enhanced to do so.

```json
{
    "id": "print.pdf",
    "label": "Print tender",
    "icon": "print",
    "route": "/?get=renover_tender-pdf&id=object.id",
    "target": "_blank",
    "absolute": true
}
```

#### Example: Fallback context in `on_error`
Automatically redirects to a list view when the main object is missing or deleted.
```json
"on_error": {
    "missing": {
        "context": {
            "entity": "sale\\pay\\Funding",
            "view": "list.default",
            "domain": ["status", "!=", "archived"]
        },
        "label": "Funding list"
    }
}
```



##  Stacking Contexts

#### What is a Context
A **context** defines the scope and environment in which a view operates. It includes:
- The **entity** being displayed or modified (e.g., `User`, `Post`).
- The **view type** (e.g., `list.default`, `form.default`).
- Additional parameters like domain (filter), actions, or associated routes.

#### How Context Stacking Works
Context stacking manages **nested or hierarchical contexts** for interacting with multiple data layers or UI components. Each new context is shown on the top of the previous one, creating a logical stack:
1. **Initial Context**: Application-specific context related to the initial view.

2. **Subcontexts**: Nested contexts for related entities or actions.

    

!!! Note "Contexts linked to routes (url)"
    Alternatively, some contexts can be associated to specific routes .



##  Types of Views

eQual supports various view types tailored to specific use cases:

#### a. List Views (`list.default`)
- **Purpose**: Display multiple objects in a grid or table format.
- **Example**: A user list showing `name`, `email`, and `role`.
- **Features**:
  - Pagination, sorting, and filtering.
  - Domain filters applied dynamically (e.g., `domain=[name,like,John]`).

#### b. Form Views (`form.default`)
- **Purpose**: Enable creation or modification of a single entity.
- **Example**: A form to update user details.
- **Features**:
  - Input widgets for each field.
  - Dynamic validation and behavior based on context.

#### c. Chart Views (`chart.default`)
- **Purpose**: Visualize data in charts or graphs.
- **Example**: A pie chart showing user roles distribution.
- **Features**:
  - Integrates with computed or aggregated data fields.

#### d. Dashboard Views
- **Purpose**: Summarize data from multiple views or entities.
- **Example**: A dashboard showing user statistics and recent activity.
- **Features**:
  - Aggregates multiple subcontexts and views.

#### e. Custom Views
- **Purpose**: Provide flexibility for unique requirements.
- **Example**: A calendar view for scheduling events.
- **Features**:
  - Custom layouts and behaviors defined programmatically or in JSON.



## Interaction Between Contexts and Views

When users interact with the application:
1. **Context Stack Evolves**: Each interaction (e.g., opening a form) creates a new context atop the stack.
2. **Dynamic Data Scope**: The current context defines which data is fetched or filtered.
3. **Rendering Views**: UI components are rendered based on the context, including inherited and user-applied filters.

For example:
- In a **list view**, clicking an item dynamically loads a **form view** with the selected entity’s details, while retaining the original context for navigation.

