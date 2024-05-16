# eQual programming paradigm



The eQual framework philosophy is to focus on the application logic and the systematic description of the manipulated entities and their behaviors. 



Indeed eQual straddles the line between Object-Oriented Programming (OOP) and procedural programming, with a stronger emphasis on entity description rather than pure object-oriented principles. 

While object-oriented concepts are utilized to some extent, the eQual framework doesn't fully leverage the characteristics of traditional OOP paradigm.

eQual favors entity-centric design, where entities are defined based on the description of their properties and behaviors, through static methods common to all models : `getColumns()`, `getRoles()`, `getActions()`, `getPolicies()`, ...

This hybrid approach allows eQual to provide flexibility in entity modeling and management while incorporating some benefits of object-oriented design.



## “Focus on description”
eQual relies on an ORM that implements the Active Record pattern but, unlike other frameworks, it doesn't fully embrace Object-Oriented Programming (OOP) principles in the sense that it isn't designed to create instances of objects for carrying out manipulations based on PHP's OOP syntax and logic.
Instead, eQual prioritizes describing relationships and interactions between entities in a way that ensures the capability to validate, adapt, and export collections of objects.

While some diehard OOP enthusiasts might call this heresy, we rather refer to it as a form of language-agnostic logic in terms of programming language.

As a result, the advantage is that the writing is very simple and follows the same logic both in classes and in controllers.



Here are the major differences with traditional OOP:

* all classes inherits from the \equal\orm\Model class (but the inheritance capabilities are maintained);
* class members are not defined directly in the class, but are described using a dedicated getColumns() method;
* most methods attached to PHP classes are static (and therefore don't use $this as reference);
* objects cannot be modified directly; rather, specific methods (create, update, delete) are used for this purpose,
* objects are mostly manipulated in bulk, using Collections.



Also, the nomenclature is slightly different: we don't talk about classes and objects but rather refer to entities and instances, and the members of an entity (class) are referred to as "properties" or "columns".

That being said, eQual takes advantage of PHP capabilities and entities instances can be manipulated either as PHP objects (stdClass) or as plain PHP arrays.

```php
<?php
use core\User;

$users = User::search()->read(['id', 'name']);
foreach($users as $id => $user) {
    echo $user->id;
    echo PHP_EOL;
    echo $user['name'];
}
```
