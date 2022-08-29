---
title: "Using the System APIs"
linkTitle: "Using the System APIs"
weight: 17
---

All DreamFactory versions include a web-based administration console used to manage all aspects of the platform. While this console offers a user-friendly solution for performing tasks such as managing APIs, administrators, and business logic, many companies desire to instead automate management through scripting. There are two notable reasons for doing so:

* Multi-environment administration: APIs should always be rigorously tested in a variety of test and QA environments prior to being deployed to production. While DreamFactory does offer a service export/import mechanism, it's often much more convenient to write custom scripts capable of automating multi-environment service creation.
* Integration with third party services: The complexity associated with creating new SaaS products such as API monetization can be dramatically reduced thanks to the ability to integrate DreamFactory into the solution. Following payment, the SaaS could interact with DreamFactory to generate a new role-based access control, API key, and define a volume limit. The new API key could then be presented to the subscriber.

In this chapter we'll walk you through several examples explaining exactly how these two use cases can be achieved.

## General Notes on Making API Calls

As with the APIs that you create using DreamFactory, the system endpoints use the same authentication method, and thus will, at minimum require a session-token (if an admin), or a session-token together with an API-key (if a user). For users to be able to access the system endpoints, the appropiate permissions must be assigned as a [role](../generating-a-database-backed-api/#creating-a-role) to that user.

### Session Tokens and API Keys

If you are the system admin, you will need to provide a session token with any call using the `X-DreamFactory-Session-Token` header. A user who has the permissions to create admins/users will need to also provide an Api Key using the `X-DreamFactory-API-Key` header.

### Using Curl

The majority of the example below will show the request type, along with the endpoint required. When using `curl`, you will want to use the following flags:

* `-X` for your request type (`GET`, `POST` etc.)
* `-H` for your headers, you will most likely use a combination of the following:
```
-H "accept: application/json"
-H "Content-Type: application/json" # For requests with a body
-H "X-DreamFactory-Session-Token: <sessionToken>"
-H "X-DreamFactory-Api-Key: <sessionToken>"
```
* `-d` for your json body when used.
## User Management

Users and Admins can be managed using the `/system/user` / `/system/admin` endpoint respectively.

For the below examples, replace `user` with `admin` when managing admins.

### Get User / Admin details

```
GET https://{url}/api/v2/system/user
```

Curl Equivalent:
```curl
curl -X GET "https://{url}/api/v2/system/user" -H "accept: application/json" \
-H "X-DreamFactory-Session-Token: <session_token>"
```
You can retrieve an individual user / admin's details using the `?ids=<id_number>` parameter. If you don't know the id number off hand, you can filter by any field which you do know, such as an email address by using the `filter` parameter. For example:

```
https://{url}/api/v2/system/user?filter=email=tomo@example.com

```

### Creating a User / Admin

* Without email invitation

```
POST https://{url}/api/v2/system/user
```
(You will need to provide your session token in a `X-DreamFactory-Session-Token` header)

Body:
```JSON
{
  "resource": [
    {
      "name": "display_name",           //Required field
      "first_name": "first_name",
      "last_name": "user_last_name",
      "email": "email_address",         //Required field
      "password": "password"
    }
  ]
}
```
Curl Equivalent:
```curl
curl -X POST "https://<url>/api/v2/system/user" \
-H "accept: application/json" -H "Content-Type: application/json" \
-H "X-DreamFactory-Session-Token: <sessionToken>" \
-d "{\"resource\":[{\"name\":\"<display_name>\",\"first_name\":\"<first_name>\",\"last_name\":\"<last_name>\",\"email\":\"<email_address>\",\"password\":\"<password>\"}]}"
```
* With email invitation

```
POST https://{url}/api/v2/system/user?send_invite=true
```

Body:
```JSON
{
  "resource": [
    {
      "name": "display_name",           //Required field
      "first_name": "first_name",
      "last_name": "user_last_name",
      "email": "email_address",         //Required field
    }
  ]
}
```
```curl
curl -X POST "https://<url>/api/v2/system/user?send_invite=true" \
-H "accept: application/json" -H "Content-Type: application/json" \
-H "X-DreamFactory-Session-Token: <sessionToken>" \
-d "{\"resource\":[{\"name\":\"<userName>\",\"first_name\":\"<firstName>\",\"last_name\":\"<lastName>\",\"email\":\"<emailAddress>\"}]}"
```

### Updating a User / Admin

To update a pre-existing User / Admin, a PATCH request can be made referencing the ID of the user to be changed with the `?ids=<ID>` parameter. (You will need to provide your session token in a `X-DreamFactory-Session-Token` header)

```
PATCH https://{url}/api/v2/system/user?ids=<ID_number>
```
Body:
```JSON
{
  "resource": [
    {
     "field_to_change": "value",
     ...
    }
  ]
}
```
Curl Equivalent:
```curl
curl -X PATCH "https://{url}/api/v2/system/user?ids=<ID_number>" \
-H "accept: application/json" -H "Content-Type: application/json" \
-H "X-DreamFactory-Session-Token: <session_token>" \
-d "{\"resource\":[{\"<field_to_update\":\"value\"}]}"
```
### Deleting a User / Admin

To delete a pre-existing User / Admin, simply send a delete request with the corresponding user ID by using the `?ids=<ID>` parameter
```
DELETE https://{url}/api/v2/system/user?ids=<ID_number>
```
Curl Equivalent
```curl
curl -X DELETE "https://{url}/api/v2/system/user?ids=<ID_number>" \
-H "accept: application/json" -H "Content-Type: application/json" \
-H "X-DreamFactory-Session-Token: <session_token>"
```

## Service Management

Creating a service will no doubt be the first key componenent to when you are using DreamFactory to handle your API management needs. The below example will be for a Database API, but the logic is fundamentally the same for any kind of service that you will create.

### Retrieving Service Types and Configuration Schemas

Each service type naturally requires a different configuration schema. For instance most database service types require that a host name, username, and password are provided, whereas the AWS S3 service type requires an AWS access key ID, secret access key, and AWS region. You can obtain a list of supported service types and associated configuration schemas by issuing a `GET` request to `/api/v2/system/service_type`.

```
GET https://{url}/api/v2/system/service_type
```
Curl equivalent
```curl
curl -X GET "https://{url}/api/v2/system/service_type" -H "accept: application/json" \
-H "X-DreamFactory-Session-Token: <session_token>"
```

This will return a rather lengthy response containing the names and configuration schemas, a tiny portion of which is recreated here:

    {
      "resource": [
        {
          "name": "adldap",
          "label": "Active Directory",
          "description": "A service for supporting Active Directory integration",
          "group": "LDAP",
          "singleton": false,
          "dependencies_required": null,
          "subscription_required": "SILVER",
          "service_definition_editable": false,
          "config_schema": [
            {
              "alias": null,
              "name": "host",
              "label": "Host",
              "description": "The host name for your AD/LDAP server.",
              "native": [],
              "type": "string",
              "length": 255,
            ...
            }
          ]
        }
      ]
    }

If you just want to retrieve a list of service type names, issue the same `GET` request but with the `fields=name` parameter attached:

    /api/v2/system/service_type?fields=name

This will return a list of service type names:

    {
      "resource": [
        {
          "name": "adldap"
        },
        {
          "name": "amqp"
        },
        {
          "name": "apns"
        },
        ...
        {
          "name": "user"
        },
        {
          "name": "v8js"
        },
        {
          "name": "webdav_file"
        }
      ]
    }

Then, if you wish to see the config details for a partiuclar service, you can add it to the end of the url. For example, to see the config details for a MySQL service:

```
GET http://{url}/api/v2/system/service_type/mysql
```
will return:
```
{
  "name": "mysql",
  "label": "MySQL",
  "description": "Database service supporting MySQL connections.",
  "group": "Database",
  "singleton": false,
  "dependencies_required": null,
  "subscription_required": "SILVER",
  "service_definition_editable": false,
  "config_schema": [
    {
      "name": "host",
      "label": "Host",
      "type": "string",
      "description": "The name of the database host, i.e. localhost, 192.168.1.1, etc."
    },
    {
      "name": "port",
      "label": "Port Number",
      "type": "integer",
      "description": "The number of the database host port, i.e. 3306"
    },
    {
      "name": "database",
      "label": "Database",
      "type": "string",
      "description": "The name of the database to connect to on the given server. This can be a lookup key."
    },
    {
      "name": "username",
      "label": "Username",
      "type": "string",
      "description": "The name of the database user. This can be a lookup key."
    },

    ...
```

### Create a Database API

To create a database API, you'll send a `POST` request to the `/api/v2/system/service` endpoint. The request payload will contain all of the API configuration attributes. For instance this payload reflects what would be required to create a MySQL API:

```
POST https://{url}/api/v2/system/service
```

    {
      "resource":[
      {
        "id":null,
        "name":"mysql",
        "label":"MySQL API",
        "description":"MySQL API",
        "is_active":true,
        "type":"mysql",
        "config":{
          "max_records":1000,
          "host":"HOSTNAME",
          "port":3306,
          "database":"DATABASE",
          "username":"USERNAME",
          "password":"PASSWORD"
        },
        "service_doc_by_service_id":null
      }
      ]
    }

Curl Equivalent
```curl
curl -X POST "http://localhost/api/v2/system/service" -H "accept: application/json" \
-H "Content-Type: application/json" -H "X-DreamFactory-Session-Token: <session_token" \
-d "{\"resource\":[{\"id\":null,\"name\":\"mysql\",\"label\":\"MySQL API\", \
\"description\":\"MySQL API\",\"is_active\":true,\"type\":\"mysql\", \
\"config\":{\"max_records\":1000,\"host\":\"HOSTNAME\",\"port\":3306, \
\"database\":\"DATABASE\",\"username\":\"USERNAME\",\"password\":\"PASSWORD\"}, \
\"service_doc_by_service_id\":null}]}"
```
After submitting a successful request, a `201 Created` status code is returned along with the newly created service's ID:

    {
      "resource": [
        {
          "id": 194
        }
      ]
    }


### Retrieve API details

To retrieve configuration details about a specific API, issue a `GET` request to `/api/v2/system/service`. You can pass along either an API ID or the API name (namespace). For instance to retrieve a service configuration by ID, you'll pass the ID like this:

    /api/v2/system/service/8

It is likely more natural to reference an API by it's namespace. You can pass the name in using the `filter` parameter:

    /api/v2/system/service?filter=name=mysql

In both cases, the response will look like this:

    {
      "resource": [
        {
          "id": 8,
          "name": "mysql",
          "label": "MySQL API",
          "description": "MySQL API",
          "is_active": true,
          "type": "mysql",
          "mutable": true,
          "deletable": true,
          "created_date": "2019-02-27 02:14:17",
          "last_modified_date": "2019-08-20 20:40:15",
          "created_by_id": "1",
          "last_modified_by_id": "3",
          "config": {
            "service_id": 8,
            "options": null,
            "attributes": null,
            "statements": null,
            "host": "database.dreamfactory.com",
            "port": 3306,
            "database": "employees",
            "username": "demo",
            "password": "**********",
            "schema": null,
            "charset": null,
            "collation": null,
            "timezone": null,
            "modes": null,
            "strict": null,
            "unix_socket": null,
            "max_records": 1000,
            "allow_upsert": false,
            "cache_enabled": false,
            "cache_ttl": 0
          }
        }
      ]
    }

## Creating a Scripted API Deployment Solution

Now that you understand how to create and query DreamFactory-managed APIs, your mind is probably racing regarding all of the ways at least some of your administrative tasks can be automated. Indeed, there are many different ways to accomplish this, because all modern programming languages support the ability to execute HTTP requests. In fact, you might consider creating a simple shell script that executes [curl](https://curl.haxx.se/) commands. Begin by creating a JSON file that contains the service creation request payload:

    {
        "resource":[
            {
                "id":null,
                "name":"mysqltest09032019",
                "label":"mysql test",
                "description":"mysql test",
                "is_active":true,
                "type":"mysql",
                "config":{
                    "max_records":1000,
                    "host":"HOSTNAME",
                    "port":3306,
                    "database":"DATABASE",
                    "username":"USERNAME",
                    "password":"PASSWORD"
                },
                    "service_doc_by_service_id":null
            }
        ]
    }

Name this file `mysql-production.json`, and don't forget to update the authentication placeholders. Next, create a shell script that contains the following code:

    #!/bin/bash

    curl -d @mysql-production.json \
        -H "Content-Type: application/json" \
        -H "X-DreamFactory-Api-Key: YOUR_API_KEY" \
        -X POST https://YOUR_DOMAIN_HERE/api/v2/system/service

Save this script as `create-service.sh` and then update the permissions so it's executable before running it:

    $ chmod u+x create-service.sh
    $ ./create-service.sh
    {"resource":[{"id":196}]}

Of course, this is an incredibly simple example which can be quickly built upon to produce a more robust solution. For instance you might create several JSON files, one for development, testing, and production, and then modify the script to allow for environment arguments:

    $ ./create-service.sh production


## Creating a Scripted Service to perform Bulk Actions

There is a useful DreamFactory feature that allows the administrator to add a database function to a column so when that column is retrieved by the API, the function runs in its place. For instance, imagine if you want to change the format of the date field, you could use ORACLE's TO_DATE() function to do that:

    TO_DATE({value}, 'DD-MON-YY HH.MI.SS AM')

DreamFactory can be configured to do this by adding TO_DATE({value}, 'DD-MON-YY HH.MI.SS AM')to the field's DB Function Use setting, which is found by going to the Schema tab, choosing a database, then choosing a table. Then click on one of the fields found in the fields section. 

To perform this action at a service level, we can create a scripted service by going to Services -> Scirpt -> And selecting a language of your choice. We will select PHP. The script below adds the datbase function to all the columns and allows us to performs this as a Bulk Action.

    $api = $platform['api'];
    $get = $api->get;
    $patch = $api->patch;
    $options = [];
    set_time_limit(800000);
    // Get all tables URL. Replace the databaseservicename with your API namespace
    $url = '<API_Namespace>/_table';

    // Call parent API
    $result = $get($url);
    $fieldCount = 0;
    $tableCount = 0;
    $tablesNumber = 0;

    // Check status code
    if ($result['status_code'] == 200) {
    // If parent API call returns 200, call a MySQL API
        $tablesNumber = count($result['content']['resource']);
    
    // The next line is to limit number of tables to first 5 to see the successfull run of the script
    //$result['content']['resource'] = array_slice($result['content']['resource'], 0, 5, true);
    
    foreach ($result['content']['resource'] as $table) {
        // Get all fields URL
        $url = "<API_Namespace>/_schema/" . $table['name'] . "?refresh=true";
        $result = $get($url);
         
        if ($result['status_code'] == 200) {
            $tableCount++;
            foreach ($result['content']['field'] as $field) {
                if (strpos($field['db_type'], 'date') !== false || strpos($field['db_type'], 'Date') !== false || strpos($field['db_type'], 'DATE') !== false) {
                    // Patch field URL
                    $fieldCount++;
                    $url = "<API_Namespace>/_schema/" . $table['name'] . "/_field";
                    
                    // Skip fields that already have the function
                    if ($field['db_function'][0]['function'] === "TO_DATE({value}, 'DD-MON-YY HH.MI.SS AM')") continue;
                    // Remove broken function
                    $field['db_function'] = null;
                    $payload = ['resource' => [$field]];
                    $result = $patch($url, $payload);
                    
                    // Add correct function
                    $field['db_function'] = [['function' => "TO_DATE({value}, 'DD-MON-YY HH.MI.SS AM')", "use" => ["INSERT", "UPDATE"]]];
                    $payload = ['resource' => [$field]];
                    $result = $patch($url, $payload);
                    
                    if ($result['status_code'] == 200) {
                        echo("Function successfully added to " . $field['label'] . " field in " . $table['name'] . " table \n");
                        \Log::debug("Function successfully added to " . $field['label'] . " field in " . $table['name'] . " table");

                    } else {
                        $event['response'] = [
                            'status_code' => 500,
                            'content' => [
                                'success' => false,
                                'message' => "Could not add function to " . $field['label'] . " in " . $table['name'] . " table;"
                            ]
                        ];

                    }
                } 
            }
            \Log::debug("SCRIPT DEBUG: Total tables number " . $tablesNumber . " -> Tables  " . $tableCount . " fieldCount " . $fieldCount);
        } else {
            $event['response'] = [
                'status_code' => 500,
                'content' => [
                    'success' => false,
                    'message' => "Could not get all fields."
                ]
            ];
        }
    }
    } else {
    $event['response'] = [
        'status_code' => 500,
        'content' => [
            'success' => false,
            'message' => "Could not get list of tables."
        ]
    ];
    }
    return "Script finished";

### Call the service

From any REST client, make the request GET /api/v2/apinamespace and you should get a status 200. A simple REST client can be found at <your_instance_url>/test_rest.html. Remember if you are not an admin user your user role must allow access to the custom scripting service.

## Managing Roles

After creating an API, you'll typically want to generate a role-based access control and API key. API-based role management is a tad complicated because it involves bit masks. The bit masks are defined as follows:

| Verb   | Mask |
|--------|------|
| GET    | 1    |
| POST   | 2    |
| PUT    | 4    |
| PATCH  | 8    |
| DELETE | 16   |

To create a role, you'll send a `POST` request to `/api/v2/system/role`, accompanied by a payload that looks like this:

    {
      "resource":[
      {
        "name": "MySQL Role",
        "description": "MySQL Role",
        "is_active": true,
        "role_service_access_by_role_id": [
          {
            "service_id": SERVICE_ID,
            "component": "_table/employees/*",
            "verb_mask": 1,
            "requestor_mask": 3,
            "filters": [],
            "filter_op": "AND"
          },
          {
            "service_id": SERVICE_ID,
            "component": "_table/supplies/*",
            "verb_mask": 3,
            "requestor_mask": 3,
            "filters": [],
            "filter_op": "AND"
          }
        ],
        "user_to_app_to_role_by_role_id": []
      }
      ]
    }

This payload assigns the role two permissions:

* It can send `GET` requests to the `_table/employees/*` endpoint associated with the API identified by `SERVICE_ID`.
* It can send `GET` and `POST` requests to the `_table/supplies/*` endpoint associated with the API identified by `SERVICE_ID`.

In the second case, the `verb_mask` was set to `3`, because you'll add permission masks together to achieve the desired permission level. For instance `GET` (1) + `POST` (2) = 3. If you wanted to allow all verbs, you'll add all of the masks together `1` + `2` + `4` + `8` + `16` = `31`. The `requestor_mask` was set to `3` because like the `verb_mask` it is represented by a bitmask. The value `1` represents API access whereas `2` represents access using DreamFactory's scripting syntax. Therefore a value of `3` ensures the endpoint is accessible via both an API endpoint and via the scripting environment.

You can learn more about role management [in our wiki](https://wiki.dreamfactory.com/DreamFactory/Tutorials/Managing_user_role_assignments).

### Viewing a Role's Permissions

You can retrieve basic role information by contacting the `/system/role/` endpoint and passing along the role's ID. For instance to retrieve information about the role associated with ID `137` you'll query this endpoint:

    /api/v2/system/role/137

This will return the following information:

    {
      "id": 137,
      "name": "Dashboard Application Role",
      "description": "Dashboard Application Role",
      "is_active": true,
      "created_date": "2020-04-06 17:56:00",
      "last_modified_date": "2020-04-06 18:10:31",
      "created_by_id": "1",
      "last_modified_by_id": "1"
    }

However you'll often want to learn much more about a role, including notably what permissions have been assigned. To do so you'll need to join the `role_service_access_by_role_id` field:

    /api/v2/system/role/137?related=role_service_access_by_role_id

This will return a detailed payload containing the assigned permissions, including each permission's service identifier, endpoint, verb mask, requestor mask, and any row-level filters (if applicable):

    {
      "id": 137,
      "name": "Dashboard Application Role",
      "description": "Dashboard Application Role",
      "is_active": true,
      "created_date": "2020-04-06 17:56:00",
      "last_modified_date": "2020-04-06 18:10:31",
      "created_by_id": "1",
      "last_modified_by_id": "1",
      "role_service_access_by_role_id": [
        {
          "id": 168,
          "role_id": 137,
          "service_id": 25,
          "component": "_table/customer/*",
          "verb_mask": 1,
          "requestor_mask": 1,
          "filters": [],
          "filter_op": "AND",
          "created_date": "2020-04-06 17:56:00",
          "last_modified_date": "2020-04-06 17:56:00",
          "created_by_id": null,
          "last_modified_by_id": null
        },
        {
          "id": 184,
          "role_id": 137,
          "service_id": 145,
          "component": "_table/account/*",
          "verb_mask": 1,
          "requestor_mask": 1,
          "filters": [],
          "filter_op": "AND",
          "created_date": "2020-04-07 14:39:38",
          "last_modified_date": "2020-04-07 14:39:38",
          "created_by_id": null,
          "last_modified_by_id": null
        }
      ]
    }

### Updating an Existing Role

To add a *new* permission to an existing role, you'll the new role information along within the `role_services_access_by_role_id` JSON object. For instance, to add a new permission to the role identified by ID `137` you'll send a PUT request to this endpoint:

    /api/v2/system/role/137

The minimal JSON payload will look like this:

    {
      "id":137,
      "role_service_access_by_role_id":[
        {
          "service_id":25,
          "component":"_table/customer/*",
          "verb_mask":1,
          "requestor_mask":1,
          "filters":[],
          "filter_op":"AND"
        }
      ]
    }

To delete an existing permission from an existing role, you'll set the `role_id` to `null`:

    {
      "id":137,
      "role_service_access_by_role_id":[
        {
          "id": 168,
          "role_id": null
        }
      ]
    }

## Managing API Keys

It's possible to create and manage API keys via the system API. To retrieve a list of all API keys (known as *Apps* in DreamFactory lingo), send a `GET` request to the URI `/api/v2/system/app`. You'll receive a list of apps that look like this:

    {
      "id": 5,
      "name": "weatherappapi",
      "api_key": "API_KEY",
      "description": "weatherappapi",
      "is_active": true,
      "type": 0,
      "path": null,
      "url": null,
      "storage_service_id": null,
      "storage_container": null,
      "requires_fullscreen": false,
      "allow_fullscreen_toggle": true,
      "toggle_location": "top",
      "role_id": 2,
      "created_date": "2019-02-28 17:55:29",
      "last_modified_date": "2019-02-28 17:55:29",
      "created_by_id": "3",
      "last_modified_by_id": null,
      "launch_url": ""
    },

To retrieve just the application name and associated API key, identify the desired fields using the `fields` parameter:

    /api/v2/system/app?fields=name,api_key

Here's an example response. Note the `launch_url` attribute is always returned:

    {
      "resource": [
        {
          "name": "weatherappapi",
          "api_key": "API_KEY",
          "launch_url": ""
        },
        {
          "name": "HR Application",
          "api_key": "API_KEY",
          "launch_url": ""
        },
        {
          "name": "MySQL",
          "api_key": "API_KEY",
          "launch_url": ""
        }
      ]
    }

You can also return each application's defined role using the `related` parameter. Issue a `GET` request to the following URI:

    /api/v2/system/app?related=role_by_role_id

This will return a list of apps, and additionally any roles associated with the app. Note how in the following example a nested JSON object called `role_by_role_id` includes the role definition:

    {
      "id": 5,
      "name": "Weather Application API",
      "api_key": "API_KEY",
      "description": "Weather Application API",
      "is_active": true,
      "type": 0,
      "path": null,
      "url": null,
      "storage_service_id": null,
      "storage_container": null,
      "requires_fullscreen": false,
      "allow_fullscreen_toggle": true,
      "toggle_location": "top",
      "role_id": 2,
      "created_date": "2019-02-28 17:55:29",
      "last_modified_date": "2019-02-28 17:55:29",
      "created_by_id": "3",
      "last_modified_by_id": null,
      "launch_url": "",
      "role_by_role_id": {
        "id": 2,
        "name": "Weather App API Role",
        "description": "Role for Weather App API",
        "is_active": true,
        "created_date": "2019-02-28 17:54:56",
        "last_modified_date": "2019-02-28 17:54:56",
        "created_by_id": "3",
        "last_modified_by_id": null
      }
    }

### Creating an App (API Key)

To create an App (Api Key) via the system endpoint, you will want to make a `POST` call to:

```
POST /api/v2/system/app
```
with the following body
```
{
    "resource": [
        {
            "name": "<nameOfYourAPP>",
            "description": "<Description>",
            "type": 0,
            "role_id": null,
            "is_active": true / false
        }
    ]
}
```
You can add an id number of a role if you would like a default role for your application. (A default role will mean that anyone can call services associated with that roles permissions without having to login to DreamFactory first, i.e without  a session token, with only the API Key)
## Clear the DreamFactory Service Cache

For performance purposes DreamFactory caches all service definitions so the configuration doesn't have to be repeatedly read from the system database. Therefore when editing a service you'll need to subsequently clear the service cache in order for your changes to take effect. To clear the cache for a specific service, issue a `DELETE` request to the following URI, appending the service ID to it:

    /api/v2/system/admin/session/8

To clear the cache for *all* defined services, issue a `DELETE` request to the following URI:

    /api/v2/system/admin/session
