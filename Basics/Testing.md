# Testing



(draft)



The type of value expected in **'return'** must be in array (ex: ['integer'], ['boolean'], ['array'] ... )

When the 'test' function returns a value, it's going through some logic that tells us whether it's "ok" or "ko"

To understand that we can look at the source code of **Tester.class.php** :

```php
public function test() {
// ...
    $result = $test['test']();
    $status = 'ok';
    
    if(in_array(gettype($result), $test['return'])) {
        if(isset($test['expected'])) {
            if(gettype($result) == gettype($test['expected'])) {
                if(gettype($result) == "array") {
                    if(!self::array_equals($result, $test['expected'])) {
                        $status= 'ko';
                    }
                }
                else {
                    if($result != $test['expected']) {
                        $status = 'ko';
                    }
                }
            }
            else {
                $status = 'ko';
            }
        }
    }
    else {
        $status = 'ko';
    }
// ...
}
```

What it does :

- Tells $status = 'ok' as default, and stores the 'test' function results in $result
- Inside the **if** logic :
  - Checking if both $result and 'return' are stored in array
  - Checking if the 'expected' field has been set
  - Comparing the type of $result with 'return'
  - If $result is an array, calls the array_equals() method to verify if $result and 'expected' are identical
  - If it's not an array, use a simpler method to verify if $result and 'expected' are identical



