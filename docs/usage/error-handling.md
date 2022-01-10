## Console

eQual has it's own console, that you can reach at "equal.local/console".



There you can find informations about your error, here is an **example** :

```
01-05-2022 14:44:43+0.41235100 Warning **@** [`C:\wamp64\www\equal\lib\equal\orm\Collection.class.php:335`] **in** `equal\orm\Domain::toString()`: Undefined offset: 1
```



An other **example**, if I did the request :

```
http://equal.local/?get=model_collect
```

The built-in responses, usually arleady give some informations about the error :

```
"errors": {
        "MISSING_PARAM": "entity"
    }
```

Each Controller tells explicitly which parameters are **required**.



Now, if I check inside the console, it would tell me the same :

```
01-06-2022 12:10:17+0.31526700 Warning @ [C:\wamp64\www\equal\run.php:185] in {main}(): QN_DEBUG_ORM::MISSING_PARAM - entity
```

An entity is **missing**, if I do add one :

```
http://equal.local/?get=model_collect&entity=core\User
```

I will get a JSON-object with all the users. 



The error codes can be found inside the **"eq.lib.php" file**.