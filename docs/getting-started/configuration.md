# Configuration



eQual config file allows to customize a wide range of pre-defined constants that are used across the different services.


The default config file is located at: `./config/default.inc.php`.  
That file holds comprehensive description of role and usage for each constant.

As a convention, it is best to leave the default config file untouched, and create a copy of it, named `config.inc.php` for customizing the configuration of your installation.

Below is the detail if these constants and their roles.

Mandatory constant that cannot be overridden.

|constant|default value|description|
|--|--|--|
|ROUTING_METHOD|||
|ROUTING_CONFIG_DIR|||
|FILE_STORAGE_MODE|||
|FILE_STORAGE_DIR|||
|DEFAULT_RIGHTS|||
|CONTROL_LEVEL|||
|DEFAULT_LANG|||
|GUEST_USER_LANG|||
|EMAIL_SMTP_HOST|||
|EMAIL_SMTP_PORT|||
|EMAIL_SMTP_ACCOUNT_DISPLAYNAME|||
|EMAIL_SMTP_ACCOUNT_USERNAME|||
|EMAIL_SMTP_ACCOUNT_PASSWORD|||
|EMAIL_SMTP_ACCOUNT_EMAIL|||
|EMAIL_SPOOL_DIR|||
|DB_REPLICATION|||
|DB_DBMS|||
|DB_CHARSET|||
|DB_HOST|||
|DB_PORT|||
|DB_USER|||
|DB_PASSWORD|||
|DB_NAME|||
|DEFAULT_PACKAGE|||



## Cascade configuration

Some constants can be overridden in optional package specific config files.

When an operation is invoked, the system checks if a config file is defined in the targeted package. If so, constants from that file override the ones from the general config file.

|constant|default value|description|
|--|--|--|
|DEBUG_MODE|||
|UPLOAD_MAX_FILE_SIZE|||
|LOGGING_ENABLED|||
|DRAFT_VALIDITY|||
|VERSIONING_ENABLED|||
|AUTH_SECRET_KEY|||
|AUTH_ACCESS_TOKEN_VALIDITY|||
|AUTH_REFRESH_TOKEN_VALIDITY|||
|AUTH_TOKEN_HTTPS|||
|ROOT_APP_URL|||