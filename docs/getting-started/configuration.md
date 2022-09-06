# Configuration



eQual config file allows to customize a wide range of pre-defined constants that are used across the different services.

The default config file is located at: `./config/default.inc.php`.  
That file holds comprehensive description of role and usage for each constant.

As a convention, it is best to leave the default config file untouched, and create a copy of it, named `config.inc.php` for customizing the configuration of your installation.

Below is the detail of these constants (that are mandatory and cannot be overriden) and their roles.

### General constants


|**CONSTANT**|**DEFAULT VALUE**|**DESCRIPTION**|
|--|--|--|
|ROUTING_METHOD|JSON||
|ROUTING_CONFIG_DIR|QN_BASEDIR.'/config/routing'|Routing configuration directory.|
|FILE_STORAGE_MODE|FS|Binary type storage mode (Possible values: 'DB' (database) and 'FS' (filesystem)).|
|FILE_STORAGE_DIR|QN_BASEDIR.'/bin'|Binaries storage directory.|
|DEFAULT_RIGHTS|QN_R_CREATE \| QN_R_READ \| QN_R_DELETE \| QN_R_WRITE|If no ACL is defined (which is the case by default) for an object nor for its class, any user will be granted the permissions in this constant.|
|ACCESS_CONTROL_LEVEL|class|By default, the control is done at the class level. It means that a user will be granted the same rights for every objects of a given class.|
|DEFAULT_LANG|fr|The language in which the content must be displayed by default (ISO 639-1).|
|GUEST_USER_LANG|fr||
|DEFAULT_PACKAGE|core|Package we'll try to access if nothing is specified in the url (typically while accessing root folder).|


### Email constants

|**CONSTANT**|**DEFAULT VALUE**|**DESCRIPTION**|
|--|--|--|
|EMAIL_SMTP_HOST|SSL0.PROVIDER.NET|Email host.|
|EMAIL_SMTP_PORT|587|Email port.|
|EMAIL_SMTP_ACCOUNT_DISPLAYNAME|Full Name|Email display name.|
|EMAIL_SMTP_ACCOUNT_USERNAME|email.as.username@provider.com|Email username.|
|EMAIL_SMTP_ACCOUNT_PASSWORD|password|Email password.|
|EMAIL_SMTP_ACCOUNT_EMAIL|email.to.send.from@provider.com||
|EMAIL_SMTP_ABUSE_EMAIL|abuse@example.com||
|EMAIL_SPOOL_DIR|QN_BASEDIR.'/spool'|Email spooler directory.|

### DB constants

|**CONSTANT**|**DEFAULT VALUE**|**DESCRIPTION**|
|--|--|--|
|DB_REPLICATION|MS|Database replication : \* - 'NO': no replication \*  - 'MS' ('master-slave'): 2 servers; write operations are performed on both servers, read operations are performed on the master only \* - 'MM' ('multi-master'): any number of servers; write operations are performed on all servers, read operations can be performed on any server.|
|DB_DBMS|MYSQL|Database management system.|
|DB_CHARSET|UTF8||
|DB_HOST|getenv('EQ_DB_HOST')?getenv('EQ_DB_HOST'):'127.0.0.1')|DB host.|
|DB_PORT|getenv('EQ_DB_PORT')?getenv('EQ_DB_PORT'):'3306')|DB port.|
|DB_USER|getenv('EQ_DB_USER')?getenv('EQ_DB_USER'):'root')|DB user.|
|DB_PASSWORD|getenv('EQ_DB_PASS')?getenv('EQ_DB_PASS'):'test')|DB password.|
|DB_NAME|getenv('EQ_DB_NAME')?getenv('EQ_DB_NAME'):'equal')|DB name.|





## Cascade configuration

Some constants can be overridden in optional package specific config files.

When an operation is invoked, the system checks if a config file is defined in the targeted package. If so, constants from that file **override** the ones from the general config file.

|**CONSTANT**|**DEFAULT VALUE**|**DESCRIPTION**|
|--|--|--|
|EXPORT_FLAG|true|flag constant allowing to detect if config has been exported.|
|DEBUG_MODE|QN_DEBUG_PHP \| QN_DEBUG_ORM \| QN_DEBUG_SQL \| QN_DEBUG_APP||
|UPLOAD_MAX_FILE_SIZE|64\*1024*1024|maximum authorized size for file upload (in octet).|
|LOGGING_ENABLED|true|Enable/Disable the logs (to keep track of object changes).|
|DRAFT_VALIDITY|0|Draft validity in days.|
|VERSIONING_ENABLED|true||
|DRAFT_FORMAT|d/m/Y|Date formatting.|
|CURRENCY_FORMAT|£#,##0.00|Currency formatting.|
|NUMERIC_DECIMAL_PRECISION|2|Default precision for floating point values.|
|AUTH_SECRET_KEY|my_secret_key||
|AUTH_ACCESS_TOKEN_VALIDITY|3600*1|validity duration of the access token, in seconds.|
|AUTH_REFRESH_TOKEN_VALIDITY|3600\*24*90|set refresh token validity, in days here.|
|AUTH_TOKEN_HTTPS|false|limit sending of auth token to HTTPS.|
|ROOT_APP_URL|http://localhost||