# Configuration



eQual config file allows to customize a wide range of pre-defined constants that are used across the different services.


The default config file is located at: `./config/default.inc.php`.  
That file holds comprehensive description of role and usage for each constant.

As a convention, it is best to leave the default config file untouched, and create a copy of it, named `config.inc.php` for customizing the configuration of your installation.

Below is the detail of these constants and their roles.

Mandatory constant that cannot be overridden.

|constant|default value|description|
|--|--|--|
|ROUTING_METHOD|JSON||
|ROUTING_CONFIG_DIR|QN_BASEDIR.'/config/routing'||
|FILE_STORAGE_MODE|FS||
|FILE_STORAGE_DIR|QN_BASEDIR.'/bin'||
|DEFAULT_RIGHTS|QN_R_CREATE \| QN_R_READ \| QN_R_DELETE \| QN_R_WRITE||
|CONTROL_LEVEL|class||
|DEFAULT_LANG|fr||
|GUEST_USER_LANG|fr||
|EMAIL_SMTP_HOST|SSL0.PROVIDER.NET||
|EMAIL_SMTP_PORT|587||
|EMAIL_SMTP_ACCOUNT_DISPLAYNAME|Full Name||
|EMAIL_SMTP_ACCOUNT_USERNAME|email.as.username@provider.com||
|EMAIL_SMTP_ACCOUNT_PASSWORD|password||
|EMAIL_SMTP_ACCOUNT_EMAIL|email.to.send.from@provider.com||
|EMAIL_SMTP_ABUSE_EMAIL|abuse@example.com||
|EMAIL_SPOOL_DIR|QN_BASEDIR.'/spool'||
|DB_REPLICATION|MS||
|DB_DBMS|MYSQL||
|DB_CHARSET|UTF8||
|DB_HOST|getenv('EQ_DB_HOST')?getenv('EQ_DB_HOST'):'127.0.0.1')||
|DB_PORT|getenv('EQ_DB_PORT')?getenv('EQ_DB_PORT'):'3306')||
|DB_USER|getenv('EQ_DB_USER')?getenv('EQ_DB_USER'):'root')||
|DB_PASSWORD|getenv('EQ_DB_PASS')?getenv('EQ_DB_PASS'):'test')||
|DB_NAME|getenv('EQ_DB_NAME')?getenv('EQ_DB_NAME'):'equal')||
|DEFAULT_PACKAGE|core||



## Cascade configuration

Some constants can be overridden in optional package specific config files.

When an operation is invoked, the system checks if a config file is defined in the targeted package. If so, constants from that file override the ones from the general config file.

|constant|default value|description|
|--|--|--|
|EXPORT_FLAG|true||
|DEBUG_MODE|QN_DEBUG_PHP \| QN_DEBUG_ORM \| QN_DEBUG_SQL \| QN_DEBUG_APP||
|UPLOAD_MAX_FILE_SIZE|64\*1024*1024||
|LOGGING_ENABLED|true||
|DRAFT_VALIDITY|0||
|VERSIONING_ENABLED|true||
|DRAFT_FORMAT|d/m/Y||
|CURRENCY_FORMAT|£#,##0.00||
|NUMERIC_DECIMAL_PRECISION|2||
|AUTH_SECRET_KEY|my_secret_key||
|AUTH_ACCESS_TOKEN_VALIDITY|3600*1||
|AUTH_REFRESH_TOKEN_VALIDITY|3600\*24*90||
|AUTH_TOKEN_HTTPS|false||
|ROOT_APP_URL|http://localhost||