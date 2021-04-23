---
title: "Appendix A: Configuration Parameter Reference"
linkTitle: "A. Configuration Parameter Reference"
weight: 1
---

* `APP_CIPHER`: Database encryption cipher, options are AES-128-CBC or AES-256-CBC (default). Only change this if you are starting from a clean database
* `APP_DEBUG`: When your application is in debug mode, detailed error messages with stack traces will be shown on every error that occurs within your application. If disabled, a simple generic error page is shown
* `APP_ENV`: This may determine how various services behave in your application
* `APP_KEY`: This key is used by the application for encryption and should be set to a random, 32 character string, otherwise these encrypted strings will not be safe. Use 'php artisan key:generate' to generate a new key. Please do this before deploying an application!
* `APP_LOCALE`: The application locale determines the default locale that will be used by the translation service provider. Currently only 'en' (English) is supported
* `APP_LOG`: This setting controls the placement and rotation of the log file used by the application
* `APP_LOG_LEVEL`: The setting controls the amount and severity of the information logged by the application. This is hierarchical and goes in the following order: DEBUG -> INFO -> NOTICE -> WARNING -> ERROR -> CRITICAL -> ALERT -> EMERGENCY. If you set log level to WARNING then all WARNING, ERROR, CRITICAL, ALERT, and EMERGENCY will be logged. Setting log level to DEBUG will log everything. Default is WARNING
['APP_NAME']="This value is used when the framework needs to place the application's name in a notification or any other location as required by the application or its packages
* `APP_TIMEZONE`: Here you may specify the default timezone for your application, which will be used by the PHP date and date-time functions
* `APP_URL`: This URL is used by the console to properly generate URLs when using the Artisan command line tool. You should set this to the root of your application so that it is used when running Artisan tasks
* `DF_LANDING_PAGE`: This is the starting point (page, application, etc.) when a browser points to the server root URL

## Database settings

* `DB_CONNECTION`: This corresponds to the driver that will be supporting connections to the system database server
* `DB_HOST`: The hostname or IP address of the system database server
* `DB_PORT`: The connection port for the host given, or blank if the provider default is used
* `DB_DATABASE`: The database name to connect to and where to place the system tables
* `DB_USERNAME`: Credentials for the system database connection if required
* `DB_PASSWORD`: Credentials for the system database connection if required
* `DB_CHARSET`: The character set override if required. Defaults use utf8, except utf8mb4 for MySQL-based databases - may cause problems for pre-5.7.7 (MySQL) or pre-10.2.2 (MariaDB), if so, use utf8
* `DB_COLLATION`: The character set collation override if required. Defaults use utf8_unicode_ci, except utf8mb4_unicode_ci for MySQL-based database - may cause problems for pre-5.7.7 (MySQL) or pre-10.2.2 (MariaDB), if so, use utf8_unicode_ci
* `DB_MAX_RECORDS_RETURNED`: This is the default number of records to return at once for database queries
* `DF_SQLITE_STORAGE`: This is the default location to store SQLite3 database files

## FreeTDS configuration (Linux and OS X only)

* `DF_FREETDS_DUMP`: Enabling and location of dump file, defaults to disabled or default freetds.conf setting
* `DF_FREETDS_DUMPCONFIG`: Location of connection dump file, defaults to disabled

## Cache

* `CACHE_DRIVER`: What type of driver or connection to use for cache storage
* `CACHE_DEFAULT_TTL`: Default cache time-to-live, defaults to 300
* `CACHE_PREFIX`: A prefix used for all cache written out from this installation
* `CACHE_PATH`: The path to a folder where the system cache information will be stored
* `CACHE_TABLE`: The database table name where cached information will be stored
* `REDIS_CLIENT`: What type of php extension to use for Redis cache storage
* `CACHE_HOST`: The hostname or IP address of the memcached or redis server
* `CACHE_PORT`: The connection port for the host given, or blank if the provider default is used
* `CACHE_USERNAME`: Credentials for the service if required
* `CACHE_PASSWORD`: Credentials for the service if required
* `CACHE_PERSISTENT_ID`: Memcached persistent ID setting
* `CACHE_WEIGHT`: Memcached weight setting
* `CACHE_DATABASE`: The desired Redis database number between 0 and 16 (or the limit set in your redis.conf file

## Limits

* `LIMIT_CACHE_DRIVER`: What type of driver or connection to use for limit cache storage
* `LIMIT_CACHE_PREFIX`: A prefix used for all cache written out from this installation
* `LIMIT_CACHE_PATH`: Path to a folder where limit tracking information will be stored
* `LIMIT_CACHE_TABLE`: The database table name where limit tracking information will be stored
* `LIMIT_CACHE_HOST`: The hostname or IP address of the redis server
* `LIMIT_CACHE_PORT`: The connection port for the host given, or blank if the provider default is used
* `LIMIT_CACHE_USERNAME`: Credentials for the service if required
* `LIMIT_CACHE_PASSWORD`: Credentials for the service if required
* `LIMIT_CACHE_PERSISTENT_ID`: Memcached persistent ID setting
* `LIMIT_CACHE_WEIGHT`: Memcached weight setting
* `LIMIT_CACHE_DATABASE`: The desired Redis database number between 0 and 16 (or the limit set in your redis.conf file

## Queuing

* `QUEUE_DRIVER`: What type of driver or connection to use for queuing jobs on the server
* `QUEUE_NAME`: Name of the queue to use, defaults to 'default'
* `QUEUE_RETRY_AFTER`: Number of seconds after to retry a failed job, defaults to 90
* `QUEUE_TABLE`: The database table used to store the queued jobs
* `QUEUE_HOST`: The hostname or IP address of the beanstalkd or redis server
* `QUEUE_PORT`: The connection port for the host given, or blank if the provider default is used
* `QUEUE_DATABASE`: The desired Redis database number between 0 and 16 (or the limit set in your redis.conf file
* `QUEUE_PASSWORD`: Credentials for the service if required
* `SQS_KEY`: AWS credentials
* `SQS_SECRET`: AWS credentials
* `SQS_REGION`: AWS storage region
* `SQS_PREFIX`: AWS SQS specific prefix for queued jobs

## Event Broadcasting

* `BROADCAST_DRIVER`: What type of driver or connection to use for broadcasting events from the server
* `PUSHER_APP_ID`:
* `PUSHER_APP_KEY`:
* `PUSHER_APP_SECRET`:
* `BROADCAST_HOST`: The hostname or IP address of the redis server
* `BROADCAST_PORT`: The connection port for the host given, or blank if the provider default is used
* `BROADCAST_DATABASE`: The desired Redis database number between 0 and 16 (or the limit set in your redis.conf file
* `BROADCAST_PASSWORD`: Credentials for the service if required

## User Management

* `DF_LOGIN_ATTRIBUTE`: By default DreamFactory uses an email address for user authentication. You can change this to use username instead by setting this to 'username'
* `DF_CONFIRM_CODE_LENGTH`: New user confirmation code length. Max/Default is 32. Minimum is 5
* `DF_CONFIRM_CODE_TTL`: Confirmation code expiration. Default is 1440 minutes (24 hours)
* `DF_ALLOW_FOREVER_SESSIONS`: false
* `JWT_SECRET`: If a separate encryption salt is required for JSON Web Tokens, place it here. Defaults to the APP_KEY setting
* `DF_JWT_TTL`: The time-to-live for JSON Web Tokens, i.e. how long each token will remain valid to use
* `DF_JWT_REFRESH_TTL`: The time allowed in which a JSON Web Token can be refreshed from its origination
* `DF_CONFIRM_RESET_URL`: Application path to where password resets are to be handled
* `DF_CONFIRM_INVITE_URL`: Application path to where invited users are to be handled
* `DF_CONFIRM_REGISTER_URL`: Application path to where user registrations are to be handled

## Server-side Scripting

* `DF_SCRIPTING_DISABLE`: To disable all server-side scripting set this to 'all', or comma-delimited list of v8js, nodejs, python, and/or php to disable individually
* `DF_NODEJS_PATH`: The system will try to detect the executable path, but in some environments it is best to set the path to the installed Node.js executable
* `DF_PYTHON_PATH`: The system will try to detect the executable path, but in some environments it is best to set the path to the installed Python executable

## API

* `DF_API_ROUTE_PREFIX`: By default, API calls take the form of http://<server_name>/<api_route_prefix>/v<version_number>
* `DF_STATUS_ROUTE_PREFIX`: By default, API calls take the form of http://<server_name>/[<status_route_prefix>/]status
* `DF_STORAGE_ROUTE_PREFIX`: By default, API calls take the form of http://<server_name>/[<storage_route_prefix>/]<storage_service_name>/<file_path>
* `DF_XML_ROOT`: XML root tag for HTTP responses
* `DF_ALWAYS_WRAP_RESOURCES`: Most API calls return a resource array or a single resource, if array, do we wrap it?
* `DF_RESOURCE_WRAPPER`: Most API calls return a resource array or a single resource, if array, what do we wrap it with?
* `DF_CONTENT_TYPE`: Default content-type of response when accepts header is missing or empty

## Storage

* `DF_FILE_CHUNK_SIZE`: File chunk size for downloadable files in bytes. Default is 10MB

## Other settings

* `DF_PACKAGE_PATH`: Path to a package file, folder, or URL to import during instance launch
* `DF_LOOKUP_MODIFIERS`: Lookup management, comma-delimited list of allowed lookup modifying functions like urlencode, trim, etc. Note: Setting this will disable a large list of modifiers already internally configured
* `DF_INSTALL`: This designates from where or how this instance of the application was installed, i.e. Bitnami, GitHub, DockerHub, etc.