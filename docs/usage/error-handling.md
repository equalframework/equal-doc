# Exception Hanling



## Exception and Throwable

Since PHP 7, all Exceptions and Errors are handled as `Throwable` objects.

eQual natively handles everything as exceptions.

While it remains possible to use try/catch blocks, any "throwable" that is not explicitly unhandled will by handled by eQual.



Every raised error/exception will eventually be turned into a HTTP error code.

The error codes can be found inside the `eq.lib.php` file.

```
define('QN_ERROR_UNKNOWN',             -1);        // something went wrong (that requires to check the logs)
define('QN_ERROR_MISSING_PARAM',       -2);        // one or more mandatory parameters are missing
define('QN_ERROR_INVALID_PARAM',       -4);        // one or more parameters have invalid or incompatible value
define('QN_ERROR_SQL',                 -8);        // error while building SQL query or processing it (check that object class matches DB schema)
define('QN_ERROR_UNKNOWN_OBJECT',     -16);        // unknown resource (class, object, view, ...)
define('QN_ERROR_NOT_ALLOWED',        -32);        // action violates some rule (including UPLOAD_MAX_FILE_SIZE for binary fields) or user don't have required permissions
define('QN_ERROR_LOCKED_OBJECT',      -64);
define('QN_ERROR_CONFLICT_OBJECT',   -128);        // version conflict
define('QN_ERROR_INVALID_USER',      -256);        // auth failure
define('QN_ERROR_UNKNOWN_SERVICE',   -512);        // server errror : missing service
define('QN_ERROR_INVALID_CONFIG',   -1024);        // server error : faulty configuration
```