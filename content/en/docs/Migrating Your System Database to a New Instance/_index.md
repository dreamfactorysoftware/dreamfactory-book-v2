---
title: "Migrating Your System Database to a New Instance"
linkTitle: "Migrating Your System Database to a New Instance"
weight: 17
---

There can be a variety of reasons for wanting to copy all your data over to a new instance and this chapter goes over various ways to do so. We will cover using our Import/Export feature, System APIs, and manually moving the System Database to a new server.

## Import and Export

TODO

## System APIs

If you would like to export your instance into a .zip file to be Imported to a new instance you can do so by also using the System APIs.

### Get a System overview

    GET /api/v2/system/package

This will output all details pertaining to your instance including Services, Roles, and more.

### Retrieve the .zip of the System

Once we have the details from our system overview, we can now perform a POST call with the same data. We will provide the data from our previous API call as the JSON body.

    POST /api/v2/system/package

You should get this returned

    {
    "success": true,
    "path": "https://{YOUR_DOMAIN}/api/v2/files/__EXPORTS/system_20.27.45.zip",
    "is_public": false
    }

Now we can download that file via cURL, wget, or your preferred method. Below is an example of downloading the file without an API key but rather using basic authentication through the URL.

    curl -LO http://{YOUR_EMAIL}%40{EMAIL_PROVIDER}:{PASSWORD}@{YOUR_DOMAIN}/api/v2/files/__EXPORTS/system_20.27.45.zip

### Uploading the Data to the Instance

Now you have a zip file with all the JSON for your Services, Admins, Roles, etc. We can now upload this to the new instance but I recommend unzipping the file just to get a brief overview of everything it contains.

Upon unzipping the file you will notice all the JSON files and there is a particular order they must be uploaded since they can rely on each other.

::: tip
Please note removing the System, API Docs, Files, Logs, db, email, and user from the JSON (typically ids 1-7) help in avoiding any duplication errors being thrown.
:::

#### Uploading Services

First you will want to upload all your Services with the following endpoint and passing the JSON as apart of the body.

    POST /api/v2/system/service

#### Uploading Roles

    POST /api/v2/system/role

#### Uploading API Keys

    POST /api/v2/system/app

#### Uploading Admins

    POST /api/v2/system/admin

#### Uploading Users

    POST /api/v2/system/user

