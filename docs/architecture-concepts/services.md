# Services



## Built-in services


Here is the full list of eQual built-in services and their purpose:



|ID|class|description|
|--|--|--|
|report|`equal\error\Reporter`|Intercept, handle and filter error and exceptions messages and store them into log file. |
|auth|`equal\auth\AuthenticationManager`| Manages the credentials & authentication tokens. |
|access|`equal\access\AccessController`| Manages the access/permissions of User & Groups to entities & controllers. |
|context|`equal\php\Context`| |
|validate|`equal\data\DataValidator`| Checks the fields consistency of entities & controllers. |
|adapt|`equal\data\DataAdapter`| |
|orm|`equal\orm\ObjectManager`| |
|route|`equal\route\Router`| Returns an existing route. |
|spool|`equal\email\EmailSpooler`| |



## How services are instantiated



## Defining a custom Service



## Overloading a built-in service



## Services invocation

@See [*Dependency injection*](dependency-injection.md)






​       

