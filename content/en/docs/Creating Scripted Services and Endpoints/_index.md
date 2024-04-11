


---
title: "Creating Scripted Services and Endpoints"
linkTitle: "Creating Scripted Services and Endpoints"
weight: 7
---

DreamFactory offers an extraordinarily powerful solution for creating APIs and adding business logic to existing APIs using a variety of popular scripting languages including PHP, Python (versions 2 and 3), Node.js, and JavaScript. In this chapter we'll walk you through several examples which will hopefully spur the imagination regarding the many ways in which you can take advantage of this great feature.

## Supported Scripting Languages
DreamFactory has several scripting languages available for customized scripting of endpoints and services. To get a list of the currently supported and configured scripts on a particular instance, use the API endpoint: 

    http:/example.com/api/v2/system/script_type

A sample output from this endpoint is below:

    {
        "resource": [
            {
                "name": "nodejs",
                "label": "Node.js",
                "description": "Server-side JavaScript handler using the Node.js engine.",
                "sandboxed": false,
                "supports_inline_execution": false
            },
            {
                "name": "php",
                "label": "PHP",
                "description": "Script handler using native PHP.",
                "sandboxed": false,
                "supports_inline_execution": false
            },
            {
                "name": "python",
                "label": "Python",
                "description": "Script handler using native Python.",
                "sandboxed": false,
                "supports_inline_execution": false
            },
            {
                "name": "python3",
                "label": "Python3",
                "description": "Script handler using native Python3.",
                "sandboxed": false,
                "supports_inline_execution": false
            }
        ]
    }

>The sandbox parameter means that the script execution is bound by memory and time and is not allowed access to other operating system functionalities outside of PHP's context. Therefore, be aware that DreamFactory cannot control what is done inside scripts using non-sandboxed languages on a server.

The following languages are typically supported on most DreamFactory installations:

* Node.js
* PHP
* Python

## Places to use DreamFactory Scripting

Scripts can be used within two places in DreamFactory, ***Event Scripting*** and ***Scripted Services***. ***Event Scripting*** is tied to and triggered by API calls, internal events or other scrips. The other way is through customize-able script services. There is a scripting service type for each supporting scripting language (above). For mor information see ***Script Services***.

## Programatic Resources Available to a Script

When a script is executed, DreamFactory passes the script(s) two primary resources to allow the script to access many parts of the system including the state, configuration and even the ability call other internal APIs (services) or external APIs. They are the **event** and **platform** resources and are described below.

> The "resource" term is used generically in this context. Depending on the scripting language being used, the resource could be an object (Node.js, Python) or an array (PHP)

### The Event Resource

The event resource holds data about the event triggering the script in question (***Event Scripting***) or from the API service call in the case of ***Scripted Services***. 

> In order for a script to be able to modify the event resource, the script must be configured to allow modification to events. This is done by checking the "Allow script to modify request payload" checkbox in the script editing screen. 

The following are the properties of the event resource. Below are similar tables for the properties of each property. 

| **Property** | **Type** | **Description**|
|---|---|---|
|**request**|resource| Represents the inbound REST API call, i.e. the HTTP request
|**response**|resource|Represents the response to an inbound REST API call, i.e the HTTP response|
|**resource**|string|Any additional resource names typically represented as a replaceable part of the path, i.e. "table name" on a db/_table/{table_name} call.
|

#### event.request
The request resource contains all the components of the original HTTP request. This resource is always available, and is writeable during pre-process event scripting. 

| **Property**|**Type**|**Description**|
|---|---|---|
|**api_version**|string|The API version used for the request (i.e. 2.0)|
|**Method**|string|The HTTP method of the request (i.e. GET, POST, PUT)|
|**Parameters**|resource|An object/array or query string parameters received with the request, indexed by parameter name|
|**headers**|resource|An object/array of HTTP headers from the request, indexed by the lowercase header name|
|**content**|string|The body of the request in raw string format|
|**content_type**|string|The format type (i.e. "application/json") of the raw **content** of the request|
|**payload**|resource|The body (POST body) of the request, i.e. the **content** converted to an internally useable object.array if possible

Any changes to this data will overwrite existing data in the request. Before further listeners are called and/or the request is handled by the called service. 

### event.response

The **response** resource contains all of the data being sent back to the client from the request. 

> This resource is only available on **post-process** event and script service scripts. 

| **Property**|**Type**|**Description**|
|---|---|---|
|**status_code**|integer|The HTTP status code of the response (i.e. 200, 404, 500 etc)|
|**headers**|resource|An object/array of the HTTP headers for the response back to the client|
|**content**|mixed|The body of the request as an object if the **content_type** is not set, or in raw string format|
|**content_type**|string|The content type (i.e. json) or the raw **content** of the request|
|

Just like **request**, any allowed changes to **response** will overwrite existing data in the response, before it is sent back to the caller

## The Platform Resource

The **platform** resource is used to access configuration and system states as well as the the actual REST API of your instance via inline calls. This makes internal requests to other services directly without requiring an HTTP call. 

The **platform** resource has the following properties. 

| **Property**|**Type**|**Description**|
|---|---|---|
|**api**|resource|An array/object that allows access to the instance's REST API|
|**config**|resource|An array/object consisting of the current configuration of the instance|
|**session**|resource|An array/object consisting of the current session information|
|

### platform.api

The **api** resource for the platform object contains methods for instance API access. The object contains a method for each type of REST verb.

| **Function**|**Description**|
|---|---|
|**get**|GET a resource|
|**post**|POST a resource|
|**put**|PUT a resource|
|**patch**|PATCH a resource|
|**delete**|DELETE a resource|
|

All of the methods above accept the same arguments:

    method("service[/resource_path]"[,payload[, options]] );

* **method** - Required. The method/vern listed above 
* **service** - Required. The service name (as used in APU calls) or an external URI
* ** resource_path** - Optional depending on your call. Resources of the service called
* **payload** - Optional but must contain a valid object for the language of the script
* **options** - Optional, may contain headers, query params and cURL options

> When you are only calling internally, only the relative URL is required (without the /api/v2 portion). Absolute URLs (`"https://example.com/my_api"`) can be passed to access external resources. See ***Scripting Tutorials and Examples*** for examples of calling platform.api methods from scripts. 

### platform.config

The config resource of the platform object contains configuration settings for the DreamFactory instance. There is one property of the config object:

|**Function**|**Description**|
|---|---|
|**df**|Configuration settings specific to DreamFactory.|

An example config return is shown below:

           (
           [version] => "2.1.0"
           [api_version] => "2.0"
           [always_wrap_resources] => true
           [resources_wrapper] => "resource"
           [storage_path] => "my/install/path/storage"
           ...
       )

### platform.session

The **session** resource of the platform object contains information about the state of the current session for the event. 

|**Function**|**Description**|
|---|---|
|**api_key**|DreamFactory API key|
|**session_token**|Session token, i.e. JWT|
|**user**|User information derived form the supplied session token, i.e. JWT|
|**app**|App information derived from the supplied API key|
|**lookup**|Available lookups for the session|
|



