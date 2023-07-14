# Actions

Actions are a convenient way to describe the available actions that have been designed for a given entity, and that can be manually applied on it.

Furthermore, each action can be attached to a series of policies in order to automatically restrict the availability of the action according to the context (user, object, collection, ...).

Actions can be invoked using the method `::do($action)` on any entity Collection.

If the action is unknown or if it cannot be performed due to one or more broken policies, an exception is raised.