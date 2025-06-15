# Logging

### Logging

The Logger (`equal/log/Logger.class.php`) class adds logs to the database using 4 different parameters :

| **PARAMETER** | **DESCRIPTION**                                              |
| ------------- | ------------------------------------------------------------ |
| user_id       | Identifier of the user responsible for the action.           |
| action        | Action of the user.                                          |
| value         | Complementary value of the action (**example**: previous value of the object). |
| object_class  | Name of the entity on which the action is done.              |
| object_id     | Identifier of the entity on which the action is done.        |

Those logs are system object, no permissions must be applied.

The logs allow users to keep an overview of object changes (action log).

The actions are CRUDS by default, but custom actions could also be created, **example** : SENT, when a message is sent.

As of now, the logs don't keep track of the content of the changes or reason behind it.



The logs can be enabled or disabled in the global config file :

```php
define('LOGGING_ENABLED', true);
```


### Audit log & changes history

The logging system relies on two entities to keep track of object
modifications:

1. `core\Log` — metadata about an action performed on an object
2. `core\Change` — the detailed payload of the modifications (the "diff")

`core\Log` is indexed for fast queries while `core\Change` holds the full
history and can be purged if required.

#### Entities summary

**`core\Log` (metadata)**

* stores **who** did **what** on **which object**
* key fields:
  * `creator` – user ID (`0` or `1` means "System")
  * `action` – type of action (`R_UPDATE`, `R_CREATE`, ...)
  * `object_class`, `object_id` – points to the affected object
* may or may not be linked to a `core\Change` entry

**`core\Change` (diff payload)**

* stores only the **changed fields** when an object is modified
* linked to `Log` through `log_id`
* also stores:
  * `object_class`, `object_id` – for direct access
  * `description` – contextual text
  * `diff` – field‑level differences in JSON
* can be archived or purged over time

#### When changes are made

* During `ObjectManager::create()` and `ObjectManager::update()`
* A `Log` is always created if `LOGGING_ENABLED === true`
* A `Change` is only created if there are field‑level differences that can be
  computed

#### Rebuilding history

**Changes history retrieval**

```
1. For each LOG (log_id = $id):
   └── Fetch its linked CHANGE:
       └── extract: description + diff
                ↓
             $map_new_values = fields + new values
                ↓
             $fields = list of changed fields
```

---

```
2. Prepare to look BACK in time:

   Search previous changes (older than current log):
   ┌──────────────────────────────────────────────┐
   │ WHERE:                                       │
   │   object_class = log.object_class            │
   │   object_id    = log.object_id               │
   │   created      < log.created                 │
   │   log_id       <> current log_id             │
   └──────────────────────────────────────────────┘
     ↓
   $changes_ids = last 25 matching Change IDs
```

---

```
3. Iterate each previous CHANGE in reverse (newest → oldest):
   └── For each field in $fields:
       └── If field exists in change.diff:
           └── Save as old value
           └── Remove field from list
   → Stop when all fields are resolved
```

**Use case: reconstruct an object’s state at time `T`**

1. Get all `Change` records related to an object
2. Filter only those with `created <= T`
3. Walk through the `Change` entries in reverse chronological order
4. For each field, capture the most recent previous value
5. Merge with the current object to build a snapshot at time `T` if needed

#### Human‑readable rendering (HTML)

Each change can be displayed as an HTML block:

```html
<table>
  <tbody>
    <tr><td>title</td><td>“Draft”</td><td>→</td><td>“Published”</td></tr>
    <tr><td>status</td><td>“pending”</td><td>→</td><td>“validated”</td></tr>
  </tbody>
</table>
```

> Special case: `creator == 0 or 1` is rendered as "System".



### Versioning

An other way to keep track of the object changes is the use of the version (`core/version.class.php`) class.

In which you could have an evolving tracking of an object, going through changes over times, with the value changes (`serialized_value`).



The versioning can be enabled or disabled in the global config file :

```php
define('VERSIONING_ENABLED', true);
```



### Reporting

The Reporter `lib/equal/error/Reporter.class.php` class will keep track of 

- debug (can be used in any script, to check variables values);
-  warning (the action is done, but incomplete);
- error (the action can't be done);
- and fatal errors (the system stops) messages.

The logs are kept inside the `log`(CSV files) folder (and appear in http://equal.local/console), they are written in a human readable way, to keep track easily.

> The logs are brief, and could, in the future, be written in JSON, to add info's.



The logs content is written following the `core/Log.class.php` structure. They are just like any other object and may use any of their functions.

For example, an other class could point at the log object ("log_id"), every time that object is subject to debug, warnings, errors and fatal errors.

> In the future, a timestamps journal could be enabled in the global `config.inc.php`, to keep track of the length of use of any eQual ressources.
