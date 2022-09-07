# Exception Hanling



## Exception and Throwable

Since PHP 7, all Exceptions and Errors are handled as `Throwable` objects.

eQual natively handles most errors as exceptions.

While it remains possible to use try/catch blocks, any "throwable" that is not explicitly unhandled will by handled by eQual.

Every raised error/exception is either handled in contollers, or catched by the `run()` method and is eventually turned into a HTTP error code.

The error codes are defined inside the `eq.lib.php` file.

| CONSTANT                   | VALUE | HTTP | DESCRIPTION                                                  |
| -------------------------------- | --- | --- | -------------------------------------------------------- |
| `QN_ERROR_UNKNOWN`         | -1    | 500  | Something went wrong (that requires to check the logs). Equivalent to  HTTP 'Internal Server Error'. |
| `QN_ERROR_MISSING_PARAM`   | -2    | 400  | One or more mandatory parameters are missing. Equivalent to  HTTP 'Bad Request'. |
| `QN_ERROR_INVALID_PARAM`   | -4    | 400  | One or more parameters have invalid or incompatible value. Equivalent to  HTTP 'Bad Request'. |
| `QN_ERROR_SQL`             | -8    | 456  | There was an error while building SQL query or processing it (check that object class matches DB schema). Equivalent to  HTTP 'Unrecoverable Error'. |
| `QN_ERROR_UNKNOWN_OBJECT`  | -16   | 404  | The request Unknown resource (class, object, view, ...). Equivalent to  HTTP 'Not Found'. |
| `QN_ERROR_NOT_ALLOWED`     | -32   | 403  | Action violates some rule (including UPLOAD_MAX_FILE_SIZE for binary fields) or user don't have required permissions. Equivalent to  HTTP 'Forbidden'. |
| `QN_ERROR_LOCKED_OBJECT`   | -64   | 423  | Object cannot be updated because it is locked by another user. Equivalent to  HTTP 'Locked'. |
| `QN_ERROR_CONFLICT_OBJECT` | -128  | 409  | Version conflict (object has been changed in between). Equivalent to  HTTP 'Conflict'. |
| `QN_ERROR_INVALID_USER`    | -256  | 401  | Authentication failure (invalid user or token). Equivalent to  HTTP 'Unauthorized'. |
| `QN_ERROR_UNKNOWN_SERVICE` | -512  | 503  | Server error : missing service. Equivalent to  HTTP 'Service Unavailable'. |
| `QN_ERROR_INVALID_CONFIG`  | -1024 | 500  | Server error : faulty configuration. Equivalent to  HTTP 'Internal Server Error'. |

