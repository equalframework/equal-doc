## Config

The config constants are present globally inside the `config/config.inc.php` file. 

Some constants can be overridden in optional package specific config files.

When an operation is invoked, the system checks if a config file is defined in the targeted package. If so, constants from that file override the ones from the general config file. 

Every overriden constant is limited to the package it belongs to.

If they are used by a controller, they must be listed by it. If there is an error, the system displays `Error 500`.

The constants are checked by the announce() function inside `eq.lib.php`.

The constants that can be overriden inside the `config/config.inc.php` file are :



| constant                    | default value                                                | description |
| --------------------------- | ------------------------------------------------------------ | ----------- |
| EXPORT_FLAG                 | true                                                         |             |
| DEBUG_MODE                  | QN_DEBUG_PHP \| QN_DEBUG_ORM \| QN_DEBUG_SQL \| QN_DEBUG_APP |             |
| UPLOAD_MAX_FILE_SIZE        | 64\*1024*1024                                                |             |
| LOGGING_ENABLED             | true                                                         |             |
| DRAFT_VALIDITY              | 0                                                            |             |
| VERSIONING_ENABLED          | true                                                         |             |
| DRAFT_FORMAT                | d/m/Y                                                        |             |
| CURRENCY_FORMAT             | £#,##0.00                                                    |             |
| NUMERIC_DECIMAL_PRECISION   | 2                                                            |             |
| AUTH_SECRET_KEY             | my_secret_key                                                |             |
| AUTH_ACCESS_TOKEN_VALIDITY  | 3600*1                                                       |             |
| AUTH_REFRESH_TOKEN_VALIDITY | 3600\*24*90                                                  |             |
| AUTH_TOKEN_HTTPS            | false                                                        |             |
| ROOT_APP_URL                | http://localhost                                             |             |