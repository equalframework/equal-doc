# Managing Complex Cases - Patterns and Solutions



## Case : Handling efficient Data Consistency in Complex Entity Logic

### Presentation
For scenarios involving complex entities logic, ensuring data consistency can be challenging and involve non-trivial logic. 

Such a situation occurs when updating certain fields requires additional processing to ensure coherence, whether the updates involve not only computed fields but also direct fields that must be assigned according to specific logic, or when child objects need to be automatically regenerated, or finally, when changes to child objects trigger modifications to parent objects, and vice versa.

Additionally, maintaining coherence may sometimes require a specific sequence of updates, as the order in which these updates are applied is crucial to ensuring consistency and avoiding data conflicts

In such scenarios, relying solely on `onupdate` callbacks can be insufficient or even inappropriate.

### Strategy

The solution involves a strategy combining the use of classes and controllers.

Within the targeted classes, `refresh` methods are created to group the logic that ensures data consistency. These methods can be called on demand, based on the following principles:

* The **method names** `refresh` are arbitrary but follow a coherent logic.
* `refresh` methods are designed to update only **one object at a time**.
* These methods act solely on **a single object level**, without affecting higher or lower levels (unless cases with a strong hierarchy, where sub-objects directly depends on the parent object state).
* `refresh` methods are **not triggered automatically**. They are invoked only when a valid version of the data is required, typically at the end of the update cycle, after all modifications have been applied.

In parallel, controllers are defined to handle the actions executed via the front-end application. It is important to restrict available actions on the front-end to only authorized operations, excluding direct calls to functions like `model_update` or `model_delete`.

These controllers contain business logic and group the appropriate actions. Based on the fields modified, they trigger the necessary refreshes on the affected objects via the `refresh` methods. These refreshes are limited to a single object level or, in some cases, two levels—especially when child objects directly depend on the configuration of their parent (e.g., assignments, consumption, etc.).

When necessary, these controllers use the `ObjectManager::disableEvents()` method to avoid any conflicts with other events defined on the classes or fields involved.

## Case : Dealing with large datasets

(This can be applied in conjunction with other strategies presented here.)

- Avoid non-stored computed fields.
- If computed fields are involved, prefer using 'instant' fields.
- Use the ORM service without relying on collections.

## Case : Special datasets architecture

### Deep datasets : TREES
Using `collections` makes it easy to present data in a tree-like structure by pre-loading several levels of related objects and efficiently building hierarchical relationships.

By combining this approach with specific front-end logic, UI components can refresh only the parts of the tree that have changed.

To prevent performance issues, ensure the tree's depth remains limited.


### MAPS

To simplify handling large datasets in the front-end, datasets can be structured as associative arrays (e.g., key-value relationships or graph-like data). This approach organizes data in a way that enables efficient lookups, updates, and navigation, making it easier to work with extensive and complex datasets.
