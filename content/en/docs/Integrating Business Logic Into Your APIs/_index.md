---
title: "DreamFactory API Business Logic: Scripts, Events & Hooks"
linkTitle: "Integrating Business Logic Into Your APIs"
description: "Add custom business logic to DreamFactory APIs using pre/post-process scripts, event-driven hooks, and server-side scripting in PHP, Python, Node.js, or V8."
weight: 7
---

DreamFactory does a very good job of generating APIs for a wide variety of data sources, including Microsoft SQL Server, MySQL, SFTP, AWS S3, and others. The generated API endpoints encompass the majority of capabilities a client is expected to require when interacting with the data source. However, software can rarely be created in cookie-cutter fashion, because no two companies or projects are the same. Therefore DreamFactory offers developers the ability to modify API endpoint logic using the scripting engine.

The scripting engine can also be used to create standalone APIs. This is particularly useful when no native nor third-party API exists to interact with a data source. For instance you might want to create an API capable of converting CSV files into a JSON stream, or you might wish to use a Python package to create a machine learning-oriented API. Such tasks can be accomplished with the scripting engine.

In this chapter you'll learn how to both extend existing APIs and create standalone APIs using the scripting engine. Finally, the chapter concludes with a section explaining how to configure DreamFactory's API request scheduler. First though let's review DreamFactory's scripting engine support.

## Supported Scripting Engines

DreamFactory currently supports four scripting engines, including:

* [PHP](https://www.php.net): PHP is the world's most popular server-side web development language.
* [Python](https://www.python.org): Python is a popular and multifaceted language having many different applications, including artificial intelligence, backend web development, and data analysis. Both versions 2 and 3 are supported.
* [Node.js](https://nodejs.org): Node.js is a JavaScript runtime built atop Chrome's V8 JavaScript engine.

Keep in mind these aren't hobbled or incomplete versions of the scripting engine. DreamFactory works in conjunction with the actual language interpreters installed on the server, and allows you to import third-party libraries and packages into your scripting environment.

### Configuring Python 3

DreamFactory 3.0 added support for Python 3 due to Python 2.X offically being retired on [January 1, 2020](https://pythonclock.org). Keep in mind DreamFactory's Python 2 integration hasn't gone away! We just wanted to provide users with plenty of time to begin upgrading their scripts to use Python 3 if so desired.

Python 3 scripting support will automatically be made available inside all DreamFactory 3 instances. However, there is an important configuration change that new and upgrading users must consider in order for Python 3 scripting to function properly. Whereas DreamFactory's Python 2 support depends upon [Bunch](https://github.com/dsc/bunch), Bunch does not support Python 3 and so a fork of the Bunch package called [Munch](https://github.com/Infinidat/munch) must be used instead.

You'll install Munch via Python's [pip](https://pip.pypa.io/en/stable/) package manager. A Python 3-specific version of pip known as pip3 should be used for the installation. If your server doesn't already include pip3 (find out by executing `which pip3`), you can install it using your server operating system's package manager. For instance on Ubuntu you can install it like this:

    $ apt-get install -y --allow-unauthenticated python3-pip

With pip3 installed, you can install munch:

    $ pip3 install munch

Once installed, you'll need to update your `.env` file (or server environment variables) to point to the Python 3 interpreter:

    DF_PYTHON3_PATH=/usr/local/bin/python3

You can find your Python 3 interpreter path by executing this command:

    $ which python3

After saving these changes, restart your PHP-FPM and Apache/Nginx service.

## Resources Available to Scripts

When a script is executed, DreamFactory passes in two very useful resources that allow each script to access many parts of the system including system states, configuration, and even a means to call other services or external APIs. They are the **event** resource and the **platform** resource.

Note: The term "resource" is used generically here, based on the scripting language used, the resource could either be an object (i.e. Node.js) or an array (i.e. PHP).

### The Event Resource

The event resource contains the structured data about the event triggered (Event Scripting) or from the API service call (Script Services). As seen below, this includes things like the request and response information available to this "event".

Note: Determined by the type of event triggering the script, parts of this event resource are writable. Modifications to this resource while executing the script do not result in a change to that resource (i.e. request or response) in further internal handling of the API call, unless the event script is configured with the allow_event_modification setting to true, or it is the response on a script service. Prior to 2.1.2, the allow_event_modification was accomplished by setting a content_changed element in the request or response object to true.

The **event** resource has the following properties:

| Property            | Type             | Description
| --------------------|------------------|------------------------------------------------------------------------------------
| request             | resource         | A resource representing the inbound REST API call, i.e. the HTTP request.
| response            | resource         | A resource representing the response to an inbound REST API call, i.e. the HTTP response.
| resource            | string           | Any additional resource names typically represented as a replaceable part of the path, i.e. "table name" on a db/_table/{tableName} call.

#### Event Request

The **request** resource contains all the components of the original HTTP request. This resource is always available, and is writable during pre-process event scripting.

| Property            | Type             | Description
| --------------------|------------------|------------------------------------------------------------------------------------
| api_version         | string           | The API version used for the request (i.e. 2.0).
| method              | string           | The HTTP method of the request (i.e. GET, POST, PUT).
| parameters          | resource         | An object/array of query string parameters received with the request, indexed by the parameter name.
| headers             | resource         | An object/array of HTTP headers from the request, indexed by the lowercase header name. Including content-length, content-type, user-agent, authorization, and host.
| content             | string           | The body of the request in raw string format.
| content_type        | string           | The format type (i.e. "application/json") of the raw content of the request.
| payload             | resource         | The body (POST body) of the request, i.e. the content, converted to an internally usable object/array if possible.
| uri                 | string           | Resource path, i.e. /api/v2/php.
| service             | string           | The type of service, i.e. php, nodejs, python.

Please note any allowed changes to this data will overwrite existing data in the request, before further listeners are called and/or the request is handled by the called service.

#### Retrieving A Request Parameter

To retrieve a request parameter using PHP, you'll reference it the parameter name via the **$event['request']['parameters']** associative array:

    // PHP
    $customerKey = $event['request']['parameters']['customer_key'];

To retrieve the filter parameter, reference the **filter** key:

    // PHP
    $filter = $event['request']['parameters']['filter']

This will return the key/value pair, such as "id=50". Therefore you'll want to use a string parsing function such as PHP's explode() to retrieve the key value:

    // PHP
    $id = explode("=", $event['request']['parameters']['filter'])[1];

To retrieve a header value:

    // Python
    request = event.request
    print request.headers['x-dreamfactory-api-key']

#### Event Response

The **response** resource contains the data being sent back to the client from the request.

**Note:** This resource is only available/relevant on post-process event and script service scripts.

| Property            | Type             | Description
| --------------------|------------------|------------------------------------------------------------------------------------
| status_code         | integer          | The HTTP status code of the response (i.e. 200, 404, 500, etc).
| headers             | resource         | An object/array of HTTP headers for the response back to the client.
| content             | mixed            | The body of the request as an object if the content_type is not set, or in raw string format.
| content_type        | string           | The content type (i.e. json) of the raw content of the request.

### The Platform Resource

This **platform** resource may be used to access configuration and system states, as well as, the REST API of your instance via inline calls. This makes internal requests to other services directly without requiring an HTTP call.

The **platform** resource has the following properties:

| Property            | Type             | Description
| --------------------|------------------|------------------------------------------------------------------------------------
| api                 | resource         | An array/object that allows access to the instance's REST API.
| config              | resource         | An array/object consisting of the current configuration of the instance.
| session             | resource         | An array/object consisting of the current session information.

#### Platform API

The **api** resource contains methods for instance API access. This object contains a method for each type of REST verb.

| Function            | Description
| --------------------|------------------
| get                 | GET a resource
| post                | POST a resource
| put                 | PUT a resource
| patch               | PATCH a resource
| delete              | DELETE a resource

They all accept the same arguments:

	method( "service[/resource_path]"[, payload[, options]] );
A breakdown of the above:

| Property            | Is Required      | Description
| --------------------|------------------|------------------------------------------------------------------------------------
| method              | true             | The method/verb listed above.
| service             | true             | The service name (as used in API calls) or external URI.
| resource_path       | optional         | Resources of the service called.
| payload             | optional         | Must contain a valid object for the language of the script.
| options             | optional         | May contain headers, query parameters, and cURL options.

Calling internally only requires the relative URL without the **/api/v2/** portion. You can pass absolute URLs like `'http://example.com/my_api'` to these methods to access external resources. See the [scripting tutorials](https://wiki.dreamfactory.com/DreamFactory/Tutorials/Server_Side_Scripting) for more examples of calling platform.api methods from scripts.

#### Node.js Platform API Example

    var url = 'db/_table/contact';
    var options = null;
    platform.api.get(url, options, function(body, response) {
            var result = JSON.parse(body);
            console.log(result);
    });

#### PHP Platform API Example

    $url = 'db/_table/contact';
    $api = $platform['api'];
    $get = $api->get;
    $result = $get($url);
    var_dump($result);

#### Python Platform API Example

    url = 'db/_table/contact'
    result = platform.api.get(url)
    data = result.read()
    print data
    jsonData = bunchify(json.loads(data))

#### Platform Config

The **config** object contains configuration settings for the instance.

| Function            | Description
| --------------------|------------------
| df                  |	Configuration settings specific to DreamFactory containing but not limited to the version, api_version, always_wrap_resources, resources_wrapper, and storage_path.

#### Platform Session

| Function            | Description
| --------------------|------------------
| api_key             |	DreamFactory API key.
| session_token       |	Session token, i.e. JWT.
| user                |	User information derived from the supplied session token, i.e. JWT. Includes display_name, first_name, last_name, email, is_sys_admin, and last_login_date
| app                 |	App information derived from the supplied API key.
| lookup              |	Available lookups for the session.

## Adding HTTP Headers, Query Parameters, or cURL Options to API Calls

You can specify any combination of headers and query parameters when calling platform.api functions from a script. This is supported by all script types using the **options** argument.

**Node.js**

    var url = 'http://example.com/my_api';
    var payload = {"name":"test"};
    var options = {
        'headers': {
            'Content-Type': 'application/json'
        },
        'parameters': {
            'api_key': 'my_api_key'
        },
    };
    platform.api.post(url, payload, options, function(body, response) {
            var result = JSON.parse(body);
            console.log(result);
    }

**PHP**

    $url = 'http://example.com/my_api';
    $payload = json_decode("{\"name\":\"test\"}", true);
    $options = [];
    $options['headers'] = [];
    $options['headers']['Content-Type'] = 'application/json';
    $options['parameters'] = [];
    $options['parameters']['api_key'] = 'my_api_key';
    $api = $platform['api'];
    $post = $api->post;
    $result = $post($url, $payload, $options);
    var_dump($result);

**Python**

    url = 'http://example.com/my_api'
    payload = '{\"name\":\"test\"}'
    options = {}
    options['headers'] = {}
    options['headers']['Content-Type'] = 'application/json'
    options['parameters'] = {}
    options['parameters']['api_key'] = 'my_api_key'
    result = platform.api.post(url, payload, options)
    data = result.read()
    print data
    jsonData = bunchify(json.loads(data))

For PHP scripts, which use cURL to make calls to external URLs, you can also specify any number of cURL options. Calls to internal URLs do not use cURL, so cURL options have no effect there.

    // PHP
    $options = [];
    $options['headers'] = [];
    $options['headers']['Content-Type'] = 'application/json';
    $options['parameters'] = [];
    $options['parameters']['api_key'] = 'my_api_key';
    $options['CURLOPT_USERNAME'] = 'user@example.com';
    $options['CURLOPT_PASSWORD'] = 'password123';

cURL options can include HTTP headers using CURLOPT_HTTPHEADER, but it's recommended to use $options['headers'] for PHP to send headers as shown above.

## Modifying Existing API Endpoint Logic

The scripting interface is accessible via the `Scripts` tab located at the top of the DreamFactory administration console. Once entered, you'll be presented with a list of APIs hosted within your DreamFactory instance. Enter one of the APIs and you'll see a top-level summary of the endpoint branches associated with that API. For instance, if you enter a database-backed API you'll see branches such as `_func` (stored function), `_proc` (stored procedure), `_schema` (table structure), and `_table` (tables). For instance, this screenshot presents the top-level interface for a Microsoft SQL Server API:

<img src="/images/06/scripting-sql-server.png" width="400" alt="Script Interface for SQL Server API">

If you keep drilling down into the branch, you'll find you can apply logic to a very specific endpoint. Additionally, you can choose to selectively apply logic to the request (pre-process) or response (post-process) side of the API workflow, can queue logic for execution outside of the workflow, and can specify that the logic executes in conjunction with a specific HTTP verb (GET, POST, etc.). We'll talk more about these key capabilities later in the chapter.

If you continue drilling down to a specific endpoint, you'll eventually arrive at the script editing interface. For instance in the following screenshot we've navigated to a SQL Server API's `customer` table endpoint. Specifically, this script will execute only when a `GET` request is made to this endpoint, and will fire *after* the data has been returned from the data source.

<img src="/images/06/leaf-sql-server.png" width="800" alt="Script for Sql Server GET Table endpoint">

{{< alert color="success" title="Tip" >}}
DreamFactory's ability to display a comprehensive list of API endpoints is contingent upon availability of corresponding OpenAPI documentation. This documentation is automatically generated for the native connectors, however for connectors such as Remote HTTP and Scripted, you can supply the OpenAPI documentation in order to peruse the endpoints via the scripting interface. One great solution for generating OpenAPI documentation is [Stoplight.io](https://stoplight.io/).
{{< /alert >}}

Although the basic script editor is fine for simple scripts, you'll probably want to manage more complicated scripts using source control. After configuring a source control API using one of the native Source Control connectors (GitHub, BitBucket, and GitLab are all supported), you'll be able to link to a script by selecting the desired API via the `Link to a service` select box located at the bottom left of the interface presented in the above screenshot.

### Examples

Let's review a few scripting examples to get your mind racing regarding what's possible.

#### Validating Input Parameters

When inserting a new record into a database you'll naturally want to first validate the input parameters. To do so you'll add a `pre_process` event handler to the target table's `post` method endpoint. For instance, if the API namespace was `mysql`, and the target table was `employees`, you would add the scripting logic to the `mysql._table.account.post.pre_process` endpoint. Here's a PHP-based example that examines the `POST` payload for missing values and also confirms that a salary-related parameter is greater than zero:

    $payload = $event['request']['payload'];

    if(!empty($payload['resource'])){
        foreach($payload['resource'] as $record){
            if(!array_key_exists('first_name', $record)){
                throw new \Exception('Missing first_name.');
            }

            if(!array_key_exists('hire_date', $record)){
                throw new \Exception('Missing hire_date.');
            }

            if($record['salary'] <= 0){
                throw new \Exception('Annual salary must be > 0');
            }
        }
    }

#### Transforming a Response

Suppose the API data source returns a response which is not compatible with the destination client. Perhaps the client expects response parameters to be named differently, or maybe some additional nesting should occur. To do so, you can add business logic to a `post_process` endpoint. For instance, to modify the response being returned from the sample MySQL database API's `employees` table endpoint, you'll add a script to `mysql._table.employees.get.post_process`. As an example, here's what a record from the default response looks like:

    {
        "emp_no": 10001,
        "birth_date": "1953-09-02",
        "first_name": "Georgi",
        "last_name": "Facello",
        "gender": "M",
        "hire_date": "1986-06-26"
    }

Suppose you instead want it to look like this:

    {
        "emp_no": 10001,
        "birth_date": "1953-09-02",
        "name": "Georgi Facello",
        "gender": "M"
    }

Specifically, we've combined the `first_name` and `last_name` parameters, and removed the `hire_date` parameter. To accomplish this you can add the following PHP script to the `mysql._table.employees.get.post_process` endpoint:

    $responseBody = $event['response']['content'];

    foreach ($responseBody['resource'] as $n => $record) {
        $record["name"] = $record["first_name"] . " " . $record["last_name"];
        unset($record["first_name"]);
        unset($record["last_name"]);
        unset($record["hire_date"]);
        $responseBody['resource'][$n] = $record;
    }

    $event['response']['content'] = $responseBody;

## Stopping Script Execution

Just like in normal code execution, execution of a script is stopped prematurely by two means, throwing an exception, or returning.

    // Stop execution if verbs other than GET are used in Custom Scripting Service
    if (event.request.method !== "GET") {
        throw "Only HTTP GET is allowed on this endpoint."; // will result in a 500 back to client with the given message.
    }

    // Stop execution and return a specific status code
    if (event.resource !== "test") {
        // For pre-process scripts where event.response doesn't exist yet, just create it
        event.response = {};
        // For post-process scripts just update the members necessary
        event.response.status_code = 400;
        event.response.content = {"error": "Invalid resource requested."};
        return;
    }

    // defaults to 200 status code
    event.response.content = {"test": "value"};

### Throwing An Exception

If a parameter such as filter is missing, you can throw an exception like so:

    // PHP
    if (! array_key_exists('filter', $event['request']['parameters'])) {
        throw new \DreamFactory\Core\Exceptions\BadRequestException('Missing filter');
    }

will return the following:

```
// JSON
{
    "error": {
        "code": 400,
        "context": null,
        "message": "Missing filter",
        "status_code": 400
    }
}
```

Using the same example above, we can also add our own internal error code by passing an integer as the second argument:

```
// PHP
if (! array_key_exists('filter', $event['request']['parameters'])) {
    throw new \DreamFactory\Core\Exceptions\BadRequestException('Missing filter', 123456);
}

```

will return:

```
// JSON
{
    "error": {
        "code": 123456,
        "context": null,
        "message": "Missing filter",
        "status_code": 400
    }
}
```
(Note that "code" will return the status code of the exception unless you provide one).

The Following Exceptions are available to you:
|Exception                   |Returned Status Code     |Argument 1           |Argument 2      |Argument 3    |
|----------------------------|-------------------------|---------------------|----------------|--------------|
|BadRequestException         |400 Bad Request          |message (string)     |code (integer)  |              |
|BatchException              |400 Bad Request          |resources (array)*   |message (string)|code (integer)|
|ConflictResourceException   |409 Conflict             |message (string)     |code (integer)  |              |
|DFException                 |500 Internal Server Error|Exception**          |                |              |
|ForbiddenException          |403 Forbidden            |message (string)     |code (integer)  |              |
|InternalServerErrorException|500 Internal Server Error|message (string)     |code (integer)  |              |
|InvalidJsonException        |400 Bad Request          |message (string)     |code (integer)  |              |
|NotFoundException           |404 Not Found            |message (string)     |code (integer)  |              |
|NotImplementedException     |501 Not Implemented      |message (string)     |code (integer)  |              |
|RestException               |Whatever you want***     |status code (integer)|message (string)|code (integer)|
|ServiceUnavailableException |503 Service Unavailable  |message (string)     |code (integer)  |              |
|TooManyRequestsException    |429 Too Many Requests    |message (string)     |code (integer)  |              |
|UnauthorizedException       |401 Unauthorized         |message (string)     |code (integer)  |              |

and can be implemented with (php) `throw new \DreamFactory\Core\Exceptions\<ExceptionFromAbove>(<arguments>)`

\* The array of resources given as the first argument for a batch exception can contain "regular" data (e.g. data from a succesful call or a string saying "success") as well as instances of Exceptions. For example if we made a POST batch request, of which two failed, and one was succesful, you could use `BatchException` in the following manner (hardcoded but it should give you an idea):

Our Script:
```
//PHP
$errorOne = new \DreamFactory\Core\Exceptions\BadRequestException('Missing Name');
$errorTwo = new \DreamFactory\Core\Exceptions\BadRequestException('Missing Location');

$successfulCall = array("Name"=>"Tomo", "Location"=>"London");

$errorArray = array($errorOne, $successfulCall, $errorTwo);

throw new \DreamFactory\Core\Exceptions\BatchException($errorArray,'Some failed requests');
```
This would return:
```
//Json
{
    "error": {
        "code": 1000,
        "context": {
            "error": [
                0,
                2
            ],
            "resource": [
                {
                    "code": 400,
                    "context": null,
                    "message": "Missing Name",
                    "status_code": 400
                },
                {
                    "Name": "Tomo",
                    "Location": "London"
                },
                {
                    "code": 400,
                    "context": null,
                    "message": "Missing Location",
                    "status_code": 400
                }
            ]
        },
        "message": "Some failed requests",
        "status_code": 400
    }
}
```
Here our "Error" array shows that the first (0) and third (2) "resource" failed, as can be seen in the "resource" object following.

\** You can use the DFException if you want the status code to be a 500 internal error, but containing an instance of a seperate Exception. For example if we wanted to show our BadRequestException but throw it as a 500 our script may look something like:
```
\\PHP
$error = new \DreamFactory\Core\Exceptions\BadRequestException('Missing Location');
throw new \DreamFactory\Core\Exceptions\DFException($error);
```
Which would return a 500 status code response, _but_ the response itself would contain the following, including the status code of our actual error (in this case 400):
```
//JSON
{
    "error": {
        "code": 400,
        "context": null,
        "message": "Missing Location"
    }
}
```

\*** By using a RestException, you can have the response be any accepted HTTP status code you like. It must be provided as the first argument, otherwise the Exception will simply return a 500.

For example, to throw a "I'm a teapot" Exception, the end of your script might well be:
```
//PHP
...
throw new \DreamFactory\Core\Exceptions\RestException(418, "Sorry Mate");
```
and your response would be:
```
\\JSON
{
    "error": {
        "code": 418,
        "context": null,
        "message": "Sorry Mate",
        "status_code": 418
    }
}
```
## Creating Standalone Scripted Services

To create a standalone scripted service, you'll navigate to `Services > Create` and then click the `Select Service Type` dropdown. There you'll find a scripted service type called `Script`, and under it you'll find links to the supported scripting engine languages (PHP, Python, and NodeJS):

<img src="/images/06/choose-scripted-language.png" width="800" alt="Creating a Standalone Scripted Service">

After choosing your desired language you'll be prompted to supply the usual namespace, label, and description for your API. Click the `Next` button and you'll be presented with a simple text editor. You're free to experiment by writing your script inside this editor, or could use the `Link to a service` option to reference a script stored in a file system, or within a repository. Keep in mind you'll first need to configure the source control or file API in order for it to be included in the `Link to a service` dropdown.

In addition to taking full advantage of the scripting language syntax, you can also use special data structures and functionality DreamFactory injects into the scripting environment. For instance, you can listen for request methods using the `$event['request']['method']` array value. For instance try adding the following code to a scripted service:

	if ($event['request']['method'] == "POST") {
	  dd("POST request!");
	} elseif ($event['request']['method'] == "GET") {
	  dd("GET request!");
	}

Save the changes, and then try contacting the scripted service endpoint with `GET` and `POST` methods. The `dd()` function will fire for each respective conditional block.

For more sophisticated routing requirements, we recommend taking advantage of one of the many OSS routing libraries. For instance [bramus/router](https://github.com/bramus/router) offers a lightweight PHP routing package that can easily be added to DreamFactory (see the next section, "Using Third-Party Libraries"). Once added, you'll be able to create sophisticated scripted service routing solutions such as this:

	set_include_path("/home/dreamfactory/libraries");

	require_once('CustomResponse.php');

	$router = new \Bramus\Router\Router();
	$response = new \DreamFactory\CustomResponse();

	$router->before('GET', '/.*', function () {
	  header('X-Powered-By: bramus/router');
	});

	$router->get('/.*', function() use($response) {
	  $response->setContent('Hello Router World!');
	});

	$router->set404(function() {
	  header('HTTP/1.1 404 Not Found');
	  $response->setContent('404 not found');
	});

	$router->run();

	return $response->getContent();

### Example Standalone Scripted Services

#### Obfuscate Table Endpoints (PHP)

This script allows you to obfuscate table endpoints to a more concise endpoint. For example, you might want to change `service_name/_table/employees` to just `exployees`. 

Paste the script into the PHP scripted service and change `api_path` variable to be whatever service/_table/tablename you want to obfuscate. Save the service. It is now available using the standard DreamFactory table record API procedures, except the endpoint is shortened.

	<?php

	// Set up the platform object with shortcuts to each verb
	$api = $platform['api'];
	$get = $api->get;
	$post = $api->post;
	$put = $api->put;
	$patch = $api->patch;
	$delete = $api->delete;

	$api_path = 'db/_table/todo'; // the service/_table/tablename you wish to obfuscate
	$method = $event['request']['method']; // get the HTTP Method
	$options['parameters'] = $event['request']['parameters']; // copy params from the request to the options object
    
    // if there are additional resources in the request path add them to our request path
	if ( $event['resource'] && $event['resource'] != '' ) { 
	  $api_path = $api_path . '/' . $event['resource'];
	}

	if ( $event['request']['payload'] ) { // if the payload is not empty assign it to the payload var
	  $payload = $event['request']['payload'];
	} else { //else make the payload null
	  $payload = null;
	}

	switch ( $method ) { // Determine which verb to use when making our api call
    case 'GET':
      $result = $get ( $api_path, $payload, $options );
      break;
    case 'POST':
      $result = $post ( $api_path, $payload, $options );
      break;
    case 'PUT':
      $result = $put ( $api_path, $payload, $options );
      break;
    case 'PATCH':
      $result = $patch ( $api_path, $payload, $options );
      break;
    case 'DELETE':
      $result = $delete ( $api_path, $payload, $options );
      break;
    default:
      $result['message'] = 'Invalid verb.';
      break;
	}

	return $result; // return the data response to the client

	?>

## Using Third-Party Libraries

As mentioned earlier in this chapter, DreamFactory passes the scripts along to the designed scripting language that's installed on the server. This means you not only have access to all of the scripting language's syntax (as opposed to some hobbled version), but also the language community's third-party packages and libraries!

### Adding a Composer Package

DreamFactory is built atop the PHP language, and uses [Composer](https://getcomposer.org/) to install and manage a number of internally built and third-party packages which are used throughout the platform. If you'd like to take advantage of a Composer package within your scripts, install it globally using the `global` modifier. For instance, suppose you wanted to send out a Tweet from a script. You can use the [twitteroauth](https://github.com/abraham/twitteroauth) package to do so:

	$ composer global require abraham/twitteroauth

Once installed, you can use the package within a DreamFactory script via it's namespace as demonstrated in the following example:

	$consumerKey    = env('TWITTER_CONSUMER_KEY');
	$consumerSecret = env('TWITTER_CONSUMER_SECRET');
	$oauthToken     = env('TWITTER_OAUTH_TOKEN');
	$oauthSecret    = env('TWITTER_OAUTH_SECRET');

	$connection = new \Abraham\TwitterOAuth\TwitterOAuth(
	  $consumerKey,
	  $consumerSecret,
	  $oauthToken,
	  $oauthSecret
	);

	if ($event['request']['method'] == "POST") {
	  $message = $event['request']['payload']['resource'][0]['message'];
	  $response = $connection->post("statuses/update", ["status" => $message]);
	}

	return json_encode(["response" => $response]);

{{< alert color="success" title="Tip" >}}
You'll want to install packages globally because the only other alternative is to install them locally via DreamFactory's Composer files. The packages will behave identically to those installed globally, however you'll eventually overwrite DreamFactory's Composer files when it's time to upgrade.
{{< /alert >}}

### Adding a PHP Class Library

If you'd like to reuse custom code within scripts, and don't want to manage the code within a Composer package, you could alternatively add the class to PHP's include path using the [set_include_path()](https://www.php.net/manual/en/function.set-include-path.php) function. Once included, you can use the [require_once](https://www.php.net/require_once) statement to import the class. This approach is demonstrated in the following example script:

	set_include_path("/home/wjgilmore/libraries");

	require_once('Filter.php');

	$filter = new \WJGilmore\Validate\Validate();

	try {

	  $filter->username("dreamfactory");

	} catch (\Exception $e) {

	  $event['response'] = [
	    'status_code' => 400,
	    'content' => [
	      'success' => false,
	      'message' => $e->getMessage()
	    ]
	];

	}

The referenced `Filter` class is found in a file named `Filter.php` and looks like this:

	<?php

	namespace WJGilmore\Validate;

	use Exception;

	class Validate {

	  public function username($username) {

	    if (preg_match("/^[a-zA-Z0-9\s]*$/", $username) != 1) {
	      throw new Exception("Username must be alphanumeric.");
	    }

      return true;

	  }

	}

If you'd like to permanently add a particular directory to PHP's include path, modify the [include_path](https://www.php.net/manual/en/ini.core.php#ini.include-path) configuration directive.  

## Queued Scripting Setup

DreamFactory queued scripting takes advantage of Laravel's built-in queueing feature, for more detailed information, see their documentation [here](https://laravel.com/docs/5.3/queues). Every DreamFactory instance comes already setup with the 'database' queue setting with all necessary tables created (scripts and failed_scripts). The queue configuration file is stored in `config/queue.php` and can be updated if another setup is preferred, such as [Beanstalkd](https://github.com/beanstalkd/beanstalkd), [Amazon SQS](https://aws.amazon.com/sqs), or [Redis](https://redis.io/).

DreamFactory also fully supports the following artisan commands for configuration and runtime execution:

	queue:failed                       List all of the failed queue scripts
	queue:flush                        Flush all of the failed queue scripts
	queue:forget                       Delete a failed queue script
	queue:listen                       Listen to a given queue
	queue:restart                      Restart queue worker daemons after their current script
	queue:retry                        Retry a failed queue script
	queue:work                         Process the next script on a queue

#### Specifying The Queue

You may also specify the queue a script should be sent to. By pushing scripts to different queues, you may categorize your queued scripts, and even prioritize how many workers you assign to various queues. This does not push scripts to different queue connections as defined by your queue configuration file, but only to specific queues within a single connection. To specify the queue, use the queue configuration option on the script or service.

#### Specifying The Queue Connection

If you are working with multiple queue connections, you may specify which connection to push a script to. To specify the connection, use the connection configuration option on the script or service.

#### Delayed Scripts

Sometimes you may wish to delay the execution of a queued script for some period of time. For instance, you may wish to queue a script that sends a customer a reminder e-mail 5 minutes after sign-up. You may accomplish this using the delay configuration option on your script or service. The option values should be in seconds.

### Running The Queue Listener

#### Starting The Queue Listener

Laravel includes an Artisan command that will run new scripts as they are pushed onto the queue. You may run the listener using the `queue:listen` command:

	php artisan queue:listen

You may also specify which queue connection the listener should utilize:

	php artisan queue:listen connection-name

Note that once this task has started, it will continue to run until it is manually stopped. You may use a process monitor such as [Supervisor](https://supervisord.org/) to ensure that the queue listener does not stop running.

#### Queue Priorities

You may pass a comma-delimited list of queue connections to the listen script to set queue priorities:

	php artisan queue:listen --queue=high,low

In this example, scripts on the high queue will always be processed before moving onto scripts from the `low` queue.

#### Specifying The Script Timeout Parameter

You may also set the length of time (in seconds) each script should be allowed to run:

	php artisan queue:listen --timeout=60

#### Specifying The Queue Sleep Duration

In addition, you may specify the number of seconds to wait before polling for new scripts:

	php artisan queue:listen --sleep=5

Note that the queue only sleeps if no scripts are on the queue. If more scripts are available, the queue will continue to work them without sleeping.

#### Processing The First Script On The Queue

To process only the first script on the queue, you may use the `queue:work` command:

	php artisan queue:work

### Dealing with Failed Scripts

To specify the maximum number of times a script should be attempted, you may use the `--tries` switch on the `queue:listen` command:

	php artisan queue:listen connection-name --tries=3

After a script has exceeded this amount of attempts, it will be inserted into a `failed_jobs` table.

#### Retrying Failed Scripts

To view all of your failed scripts that have been inserted into your `failed_jobs` database table, you may use the `queue:failed` Artisan command:

	php artisan queue:failed

The `queue:failed` command will list the script ID, connection, queue, and failure time. The script ID may be used to retry the failed script. For instance, to retry a failed script that has an ID of 5, the following command should be issued:

	php artisan queue:retry 5

To retry all of your failed scripts, use `queue:retry` with `all` as the ID:

	php artisan queue:retry all

If you would like to delete a failed script, you may use the `queue:forget` command:

	php artisan queue:forget 5

To delete all of your failed scripts, you may use the `queue:flush` command:

	php artisan queue:flush

## Scheduled Tasks

DreamFactory does not natively support scheduled tasks but you can setup a CRON job for this purpose. Let's create an example that calls an API every minute of the day.

### Creating the Script

First we will create the script to call the API. One easy way to do so is by navigating to the `API Docs` tab and copying the cURL command for the appropriate call we would like to make. In this case we have business logic attached to `GET` on `_table/employees` that is synchronizing data between [two databases](../generating-a-database-backed-api/#synchronizing-records-between-two-databases).

<p>
<img src="/images/06/curl-schedule.png" width="800" alt="Creating a script for a Scheduled Task">
</p>

Once we have the cURL command we can convert it to PHP by using this [useful tool](https://incarnate.github.io/curl-to-php/). After we will create a file named `cron.php` in the `public` folder containing the generated PHP code.

### Running the CRON job

To start let's define the CRON job parameters:

	* * * * * /usr/bin/php /opt/dreamfactory/public/cron.php >/dev/null 2>&1

This can be broken into 4 parts, the timing, execute PHP, path to script, and the output. In this example the `* * * * *` means it will run once every minute. The second portion is the path to PHP to allow it to be executed. The important part is now providing the full path to the file you would like to run. Finally you can write the output to a file or discard it, in this case I have set it to be discarded. If you would like to learn more about the structure, check out this [article](https://crontab-generator.org/).

Next you will edit the `crontab` by running the following:

	$ crontab -e

You will be put into the text editor where you can simply paste in your CRON job and save it. Now you have a scheduled task running every minute to call your API!

## Example Scripts

This section contains example scripts in PHP, Python, and NodeJS. For additional examples please consult these resources:

* [https://github.com/dreamfactorysoftware/example-scripts](https://github.com/dreamfactorysoftware/example-scripts)
* [https://wiki.dreamfactory.com/DreamFactory/Tutorials/Server_Side_Scripting](https://wiki.dreamfactory.com/DreamFactory/Tutorials/Server_Side_Scripting)

### NodeJS Custom Logging

This script monitors usage of a particular service, saving history in a database table. Each time a GET call is made on an API endpoint, write the transaction details to a 'TransactionHistory' table. Record the user name, application API key, and timestamp.

    // To enable Node.js scripting, set the path to node in your DreamFactory .env file.
    // This setting is commented out by default.
    //
    // DF_NODEJS_PATH=/usr/local/bin/node
    //
    // Use npm to install any dependencies. This script requires 'lodash.'
    // Your scripts can call console.log to dump info to the log file in storage/logs.
    
    var payload = {
    
        user_name: platform.session.user.email,
        api_key: platform.session.api_key,
        timestamp: (new Date()).toString()
    };
    
    platform.api.post("db/_table/TransactionHistory", {"resource": [payload]}, '', function(body, response){
    
        console.log(response.statusCode + " " + response.statusMessage);
        event.setResponse(JSON.parse(body), response.statusCode, 'applicaton/json');
    });

### PHP Dynamic Authorization Headers

This PHP script is retrieving the configuration details for a particular Service and modifying the authorization headers. It can be very useful to have a script automatically do this for you if you are maintaining many Services that rotate passwords. The `$randomNum` variable can even be replaced with a seperate API call to an OAuth endpoint for example that returns a JWT for authentication against your Service. Finally this script can be attached to our Scheduler feature to automatically run this script to ensure you are always authenticated.

    // Configuring variables
    $url = 'system/service/<service_id>';
    $api = $platform['api'];
    $get = $api->get;
    $options = [];
    $randomNum = rand(1000000000000,5000000000000000000);

    // Retrieve the service details
    $calling = $get($url);
    $configAuth = $calling["content"];
    // Set the new Auth Header for the Service
    $configAuth["config"]["options"] = "abc".$randomNum;

    // Setting the payload to have the new Auth Header
    $payload = $configAuth;

    // Execute a PATCH to replace the previous Auth Header of the Service
    $postURL = 'system/service';
    $patch = $api->patch;

    $result = $patch($url, $payload, $options);

    return $result;

## More Information

We're still in the process of migrating scripting documentation into this guide, so for the time being please consult our wiki for more information about scripting:

* [https://wiki.dreamfactory.com/DreamFactory/Features/Scripting](https://wiki.dreamfactory.com/DreamFactory/Features/Scripting)
* [https://wiki.dreamfactory.com/DreamFactory/Tutorials/Server_Side_Scripting](https://wiki.dreamfactory.com/DreamFactory/Tutorials/Server_Side_Scripting)