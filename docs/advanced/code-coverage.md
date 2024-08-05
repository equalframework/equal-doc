# Code Coverage 



## Code Coverage setup

Config for PHP 7.4 with xdebug

In dockerfile, xdebug can be added with following command
```
RUN pecl install xdebug-3.1.6
```

php.ini
```
zend_extension=xdebug
```

Create tests directory
```
mkdir tests
```

Create `tests/CoverageTest.php` file
```
<?php
use PHPUnit\Framework\TestCase;
use equal\test\Tester;
include('eq.lib.php');

final class CoverageTest extends TestCase {

	public function test(): void {
		$tests_path = "packages/core/tests";
		if(is_dir($tests_path)) {
			foreach (glob($tests_path."/*.php") as $filename) {
				include($filename);
				$tester = new Tester($tests);
				$tester->test()->toArray();
			}
		}
		$this->assertTrue(true);
	}

}
```

composer.json
```
{
    "require": {
    },
    "require-dev": {
        "phpunit/phpunit": "^9.5",
        "phpunit/php-code-coverage": "^9.2",
        "symplify/easy-coding-standard": "^11.1"
    }
}
```

Install composer and dependencies
```
./equal.run --do=init_composer
```

phpunit.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="https://schema.phpunit.de/9.5/phpunit.xsd"
         bootstrap="vendor/autoload.php"
         cacheResultFile="cache/test-results"
         executionOrder="depends,defects"
         forceCoversAnnotation="false"
         beStrictAboutCoversAnnotation="false"
         beStrictAboutOutputDuringTests="true"
         beStrictAboutTodoAnnotatedTests="true"
         convertDeprecationsToExceptions="true"
         failOnRisky="false"
         failOnWarning="true"
         verbose="true">
    <testsuites>
        <testsuite name="default">
            <directory>tests</directory>
        </testsuite>
    </testsuites>

    <coverage cacheDirectory="cache/code-coverage"
              processUncoveredFiles="true">
        <include>
            <directory suffix=".php">lib</directory>
        </include>
    </coverage>
</phpunit>
```

Alternate script generation:
```
./vendor/bin/phpunit --generate-configuration
```


Run coverage tests and HTML reporting
```
XDEBUG_MODE=coverage ./vendor/bin/phpunit --coverage-html tests/report
```