# Logging

The Logger `equal/log/Logger.class.php` class adds logs to the database using 4 different parameters :

- **user_id** (identifier of the user responsible for the action)
- **action** (action of the user)
- **object_class** (name of the entity on which the action is done)
- **object_id** (identifier of the entity on which the action is done)

Those logs are system object, no permissions must be applied.



Example :

![Log](C:\Users\Jean\Pictures\Log.PNG)



The logs allow users to keep an overview of object changes.
