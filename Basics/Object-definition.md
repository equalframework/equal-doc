# Object definition

Every class of object is defined by the **.class.php** file type, and is located in the **/classes** file of your project (see [Directory Structure](Directory-structure.md))

To see the syntax for class definition is as follows :

```php


class Task extends Model {
    public static function getColumns() {
        return array(
            'title'     => ['type' => 'string'],
            'content'   => ['type' => 'text']
        );
    }
}
```

