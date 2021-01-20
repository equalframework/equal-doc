# Testing

## Writing the tests

The testing folder should be located at the root of each package, under the name **/tests**

In this folder you have to create a new file named **default.php**

Here is a minimal template for test-writing :

```php
// \tests\default.php
<?php
use qinoa\http\HttpRequest;
use myapp\Myobject
    
$providers = inject(['context']);

$tests = [
    '0001' => [
        'description'   =>  '',	// what the test does
        'return'        =>  [],	// expected value type
        'expected'      =>  [],	// expected result of 'test'
        'test'          =>  function(){
            					// testing goes here
                            }
    ],
    // working example
    '0002' => [
        'description'   =>  'checking test_value',
        'return'        =>  ['string'], 
        'expected'      =>  "test",
        'test'          =>  function(){
            					$test_value = "test"
            					return $test_value;
                            }
    ],
	// ... add as many as you want!
]
```

There is 2 additional parameters you can use in case you need to run some specific function before and after a test :

- **'arrange' =>** is used before the test
- **'rollback' =>** is used after the test

They have to fit between 'expected' and 'test' in order to work



## Syntax must-know

The type of value expected in **'return'** must be in array (ex: ['integer'], ['boolean'], ['array'] ... )

The **'expected'** field can be anything but **null**

In the **'test'** part you can do pretty much any logic you want, but you have to **return** something in the end. When this 'test' function returns a value, it's going through the built-in function test() to tell us whether it's "ok" or "ko"

To understand that we can look at the source code of **Tester.class.php** :

```php
public function test() {
// ...
    $result = $test['test']();
    $status = 'ok';
    
    if(in_array(gettype($result), $test['return'])) {						// (1)
        if(isset($test['expected'])) {										// (2)
            if(gettype($result) == gettype($test['expected'])) {			// (3)
                if(gettype($result) == "array") {							// (4)
                    if(!self::array_equals($result, $test['expected'])) {	// (5)
                        $status= 'ko';
                    }
                }
                else {
                    if($result != $test['expected']) {						// (6)
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

It tells $status = 'ok' as default, and stores the 'test' function results in $result

Inside the **if** logic :

**(1)** Checking if both $result and 'return' are stored in array

**(2)** Checking if the 'expected' field has been set

**(3)** Comparing the type of $result with 'return'

**(4)** If $result is an array, **(5)** calls the array_equals() method to verify if $result and 'expected' are identical

**(6)** If it's not an array, use a simpler method to verify if $result and 'expected' are identical



## Running the tests

Open your CLI at the root of eQual (where run.php is), then launch this :

```bash
php run.php --do=test_package --package=myapp
```

Each test you wrote will result as "ok" or "ko" depending on the expected results

But, if you can't see anything it means you've made an error somewhere in your code. Tip: use **/console.php** in your browser for potential insights