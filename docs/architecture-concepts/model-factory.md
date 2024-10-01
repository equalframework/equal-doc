# Model Factory

The `ModelFactory` class in eQual provides a convenient way to **generate entity objects** for testing or other non-persistence use cases. Entities are created with random values by default but can be customized as needed. These generated entities are not saved in the database, making them ideal for use in temporary scenarios such as testing or mock data creation.

## Features Overview
- **Randomized Data**: Entities are generated with random attributes.
- **Customizable Values**: Specific fields can be forced to predefined values.
- **Batch Creation**: Supports generating multiple entities at once.
- **Sequence Control**: Allows defining custom sequences for multiple entities.
- **Unique Constraint Handling**: Optionally respect or ignore unique constraints.
- **Relation Handling**: Supports the generation of related entities.

## Usage Examples

### 1. Basic Entity Creation

Without any additional options, a single entity is generated with random values for all attributes.

```php
<?php

use equal\orm\ModelFactory;

$user = ModelFactory::create('core\User');
```

**Example Output**:

```json
{
    "login": "ukag6qr4@postbox.com",
    "username": "trueBeliever3590",
    "password": "DaAiiahaGO",
    "firstname": "Caroline",
    "lastname": "Nguyen",
    "language": "es",
    "validated": false,
    "status": null
}
```

### 2. Creating Multiple Entities

You can generate more than one entity by specifying the `qty` (quantity) option.

```php
<?php

use equal\orm.ModelFactory;

$users = ModelFactory::create('core\User', ['qty' => 2]);
```

**Example Output**:

```json
[
    {
        "login": "lg8nv@postbox.com",
        "username": "silentStorm726",
        "password": "NI6elTCY6w",
        "firstname": "Michael",
        "lastname": "Maillard",
        "language": "ki",
        "validated": false,
        "status": "created"
    },
    {
        "login": "jytxp@webmail.org",
        "username": "superstar874",
        "password": "Em9515qBtr",
        "firstname": "Sawyer",
        "lastname": "Mendoza",
        "language": "is",
        "validated": true,
        "status": "created"
    }
]
```

### 3. Forcing Specific Values

To control certain attributes, use the `values` option. In the example below, the `firstname` and `lastname` fields are set to "John" and "Doe", while other fields remain randomized.

```php
<?php

use equal\orm\ModelFactory;

$user = ModelFactory::create('core\User', [
    'values' => [
        'firstname' => 'John',
        'lastname'  => 'Doe'
    ]
]);
```

**Example Output**:

```json
{
    "login": "q1a22xm5pm@sample.org",
    "username": "luckyCharm2140",
    "password": "J1IvaJEo3e",
    "firstname": "John",
    "lastname": "Doe",
    "language": "br",
    "validated": false,
    "status": "created"
}
```

### 4. Sequences of Forced Values

The `sequences` option allows you to force different values for each entity in a batch. Below, the first user is "John Doe" with English as their language, and the second is "Jean Dupont" with French.

```php
<?php

$users = ModelFactory::create('core\User', [
    'qty'       => 2,
    'sequences' => [
        [
            'firstname' => 'John',
            'lastname'  => 'Doe',
            'language'  => 'en'
        ],
        [
            'firstname' => 'Jean',
            'lastname'  => 'Dupont',
            'language'  => 'fr'
        ]
    ]
]);
```

**Example Output**:

```json
[
    {
        "login": "wzr0yqsf8@postbox.com",
        "username": "coolUser392",
        "password": "UDrQP3oMNI",
        "firstname": "John",
        "lastname": "Doe",
        "language": "en",
        "validated": true,
        "status": "created"
    },
    {
        "login": "87388@webmail.org",
        "username": "happyCamper930",
        "password": "OYOaKwGRtc",
        "firstname": "Jean",
        "lastname": "Dupont",
        "language": "fr",
        "validated": true,
        "status": "created"
    }
]
```

#### Handling Sequence Overflow
If the number of sequences is less than the `qty`, the sequence will repeat from the beginning. In the example below, the third user reuses the first sequence.

```php
<?php

$users = ModelFactory::create('core\User', [
    'qty'       => 3,
    'sequences' => [
        [
            'firstname' => 'John',
            'lastname'  => 'Doe',
            'language'  => 'en'
        ],
        [
            'firstname' => 'Jean',
            'lastname'  => 'Dupont',
            'language'  => 'fr'
        ]
    ]
]);
```

**Example Output**:

```json
[
    {
        "login": "n81ve5nfx@mywebsite.net",
        "username": "starGazer4621",
        "password": "7AZvchY9BV",
        "firstname": "John",
        "lastname": "Doe",
        "language": "ar",
        "validated": true,
        "status": "created"
    },
    {
        "login": "gm7tz@hostmail.co",
        "username": "digitalDynamo4120",
        "password": "8ttyoRpbBh",
        "firstname": "Jean",
        "lastname": "Dupont",
        "language": "na",
        "validated": true,
        "status": "created"
    },
    {
        "login": "ozqrrm@mailservice.io",
        "username": "hyperLink504",
        "password": "UKY72nyTK5",
        "firstname": "John",
        "lastname": "Doe",
        "language": "mt",
        "validated": false,
        "status": "created"
    }
]
```

### 5. Ignoring Unique Constraints

By default, the `ModelFactory` respects unique constraints. However, by setting the `unique` option to `false`, you can generate entities that violate uniqueness constraints. Below, both groups are given the same name, "Group 1", even though the name should be unique.

```php
<?php

use equal\orm\ModelFactory;

$group = ModelFactory::create('core\Group', [
    'qty'    => 2,
    'values' => ['name' => 'Group 1'],
    'unique' => false
]);
```

```json
[
    {
        "name": "Group 1",
        "display_name": "Ad veniam tempor",
        "description": "Nisi consectetur ad ut exercitation labore ut ut ut. Incididunt dolor sit ea minim sed amet sit. Nostrud adipiscing"
    },
    {
        "name": "Group 1",
        "display_name": "Ex consectetur sit do do",
        "description": "Adipiscing ipsum ut eiusmod laboris dolor aliquip consectetur minim lorem dolor elit. E"
    }
]
```

### 6. Generating Related Entities

The `relations` option allows you to generate related entities along with the "parent" entity. You can specify the quantity and other options for the related entities in the same way as the main entity.

```php
<?php

use equal\orm\ModelFactory;

$group = ModelFactory::create('core\Group', [
    'relations' => [
        'users_ids' => ['qty' => 2]
    ]
]);
```

**Example Output**:

```json
{
    "name": "Aliquip dolo",
    "display_name": "Ipsum sed nostrud adipiscing...",
    "description": "Magna sed minim enim aliqua...",
    "users_ids": [
        {
            "login": "5ow55engy@inbox.net",
            "username": "happyCamper6100",
            "password": "ingB2tK9xS",
            "firstname": "William",
            "lastname": "Gautier",
            "language": "nd",
            "validated": true,
            "status": "created"
        },
        {
            "login": "oae1e@mail.com",
            "username": "stellar-voyager5379",
            "password": "udSpMEk2NM",
            "firstname": "Ella",
            "lastname": "Marechal",
            "language": "ja",
            "validated": false,
            "status": "created"
        }
    ]
}
```

## Notes
- **Handling Sequences**: If the `sequences` array is shorter than the number of entities (`qty`), the sequences will loop. This ensures that all entities are created with at least one set of custom values.
- **Default Behavior**: If no options are provided, the `ModelFactory` generates one entity with completely randomized values.
- **Unique Constraints**: By default, the `ModelFactory` ensures unique constraints are followed unless explicitly overridden with the `unique` option set to `false`.
