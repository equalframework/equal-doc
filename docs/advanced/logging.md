# Logging

### Logging

The Logger `equal/log/Logger.class.php` class adds logs to the database using 4 different parameters :

- **user_id** (identifier of the user responsible for the action)
- **action** (action of the user)
- **object_class** (name of the entity on which the action is done)
- **object_id** (identifier of the entity on which the action is done)

Those logs are system object, no permissions must be applied.

The logs allow users to keep an overview of object changes (action log).



### Reporting

The Reporter `lib/equal/error/Reporter.class.php` class will keep track of debug (can be used in any script, to check variables values), warning (the action is done, but incomplete), error (the action can't be done) and fatal errors (the system stops) messages.

The logs are kept inside the `log`(CSV) folder (and appear in http://equal.local/console), they are written in a human readable way, to keep track easily.

> The logs are brief, and could, in the future, be written in JSON, to add info's.



The logs content is written following the `core/Log.class.php` structure. They are just like any other object and may use any of their functions.

For example, an other class could point at the log object ("log_id"), every time that object is subject to debug, warnings, errors and fatal errors.
