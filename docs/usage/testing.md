# Testing

Before anything, please check your package consistency :

```bash
php run.php --do=test_package-consistency --package=mypackage
```

It will tell you if you have available tests. If it's not the case, scroll down to "**Writing the tests**"



## Running the tests

Open your CLI at the root of eQual (where run.php is), then launch this :

```bash
php run.php --do=test_package --package=mypackage
```

It should log an overview of each test you wrote, resulting as "ok" or "ko" depending on the expected results

If you can't see anything it means there is an error somewhere in your code. Tip: use **/console.php** in your browser for potential insights



## Writing the tests

The testing folder should be located at the root of each package, under the name **/tests**

In this folder add a file **mytests.php** (or whatever name you want)

Here is a minimal template for test-writing :

```php
// \tests\mytests.php
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
	// [...] add as many as you want!
]
```

There is 2 additional parameters you can use in case you need to run some specific function before and after a test :

- **'arrange' => function(){  }**    (used before the test)
- **'rollback' => function(){  }**    (used after the test)

They have to fit between 'expected' and 'test' in order to work



## Syntax details

The type of value expected in **'return'** must be in array (ex: ['integer'], ['boolean'], ['array'] ... )

The **'expected'** field value can be anything **but null**

The **'test'** field is where the logic is written, it has to end with a **return**. This returned value will go through the built-in function test() which tells us whether it's "ok" or "ko"

Let's have a look at **Tester.class.php** to understand how the testing works :

```php
<?php

public function test() {
// [...]
    $result = $test['test']();
    $status = 'ok';
    
    if(in_array(gettype($result), (array) $test['return'])) {				// (1)
      if(isset($test['expected'])) {										// (2)
        if(gettype($result) == gettype($test['expected'])) {				// (4)
          if(gettype($result) == "array") {									// (5)
            if(!self::array_equals($result, $test['expected'])) {			// (6)
              $success = false;
            }
          }
          else {
            if($result != $test['expected']) {								// (7)
              $success = false;
            }
          }
        }
        else {
          $success = false;
        }
      }
      else if(isset($test['assert']) && is_callable($test['assert'])) {		// (3)
        $success = $test['assert']($result);
      }
    }
    else {
      $success = false;
    }
// [...]
}
```

**What it does :**

Store the 'test' function results in $result, and tell $status = 'ok' by default

Cascading logic :

- **(1)** If $result and 'return' value are respectively inside an array (if 'return' isn't, do so automatically)
- **(2)** If the 'expected' field has been set. If not, **(3)** Check if there is an 'assert' field available
- **(4)** If the type of $result is the same as 'return'
- **(5)** If $result is of type "array", **(6)** Call the array_equals() method to verify if $result and 'expected' are identical. If not, **(7)** Use a simpler logic to do the verification

'$success = false' is the equivalent of '$status = ko'





