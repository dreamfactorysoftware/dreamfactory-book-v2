---
title: "Creating File System APIs"
linkTitle: "Creating File System APIs"
weight: 14
---

This chapter shows you how to quickly create REST APIs for SFTP, AWS S3, and more using the DreamFactory platform.

## Creating an SFTP REST API

SFTP (SSH File Transfer Protocol) is the secure version of FTP, capable of transferring data over a Secure Shell (SSH) data stream. Despite the media buzz being focused on file services like Dropbox and AWS S3, SFTP-based file transfers remain an indispensable part of IT infrastructures large and small. But incorporating SFTP functionality into a web application or system management script can be a real drag. Fortunately you can use DreamFactory to easily create a full-featured REST API for your SFTP servers. This API can perform all of the standard tasks associated with an SFTP server, including:

* Creating, listing, updating, and deleting folders
* Creating, listing, retrieving, updating, and deleting files

In this tutorial we'll show you how to configure DreamFactory's SFTP connector, and then walk through several usage examples.

### Generating the SFTP API and Companion Documentation

To generate your SFTP API, log into DreamFactory and click on the `Services` tab, then click the `Create` link located on the left side of the page. Click on the `Select Service Type` dropdown, navigate to the `File` category, and select `SFTP File Storage`:

<p>
<img src="/images/12/sftp-select.png" width="600">
</p>

You'll be prompted to supply an API name, label, and description. Keep in mind the name must be lowercase and alphanumeric, as it will be used as the namespace within your generated API URI structure. The label and description are used for reference purposes within the administration console so you're free to title these as you please:

<p>
<img src="/images/12/sftp-create-info.png" width="600">
</p>

Next, click the `Config` tab. There you'll supply the SFTP server connection credentials. There are however only 5 required fields:

* **Host**: The SFTP server hostname or IP address.
* **Port**: The SFTP server port. This defaults to 22.
* **Username**: The connecting account username.
* **Password**: The connecting account password.
* **Root folder**: The designated SFTP account root directory.

The other fields (**Timeout**, **Host Finger Print**, **Private Key**) are not always required, and depend upon your particular SFTP server's configuration.

After saving your changes, head over to the `API Docs` tab to review the generated documentation. You'll be presented with a list of 13 generated endpoints:

<p>
<img src="/images/12/sftp-api-docs.png" width="600">
</p>

### Listing Directory Contents

If the root directory you identified during the configuration process already contains a few files and/or directories, click on the `List the folder's content, including properties` endpoint and press `Try It Out`. Doing so will enable all of the supported parameters for this endpoint, allowing you to experiment. Scroll down to the `folder_path` parameter, set it to `/`, and press `Execute`. You should see output similar to the following:

	{
	  "resource": [
	    {
	      "path": "Marketing/",
	      "type": "folder",
	      "name": "Marketing",
	      "last_modified": "Tue, 23 Jul 2019 15:31:31 GMT"
	    },
	    {
	      "path": "Operations/",
	      "type": "folder",
	      "name": "Operations",
	      "last_modified": "Tue, 23 Jul 2019 15:31:20 GMT"
	    }
	  ]
	}

### Creating a Folder

To create a folder, you can use one of two endpoints:

* `POST / Create some folders and/or files`
* `POST /{folder_path}/ Create a folder and/or add content`

These endpoints are identical in functionality, but their URI signatures differ so you might choose one over the other depending upon the desired approach. Let's start by creating a single empty folder. To do so, click on the `POST / Create some folders and/or files` endpoint inside API Docs, press the `Try It Out` button, and enter a folder name in the `X-Folder-Name` field. In the `folder_path` field enter the destination path, such as `/`. Press `Execute` and the folder will be created and a `201` response code returned with a response body that looks like this:

	{
	  "name": "Marketing",
	  "path": "Marketing"
	}

Note the `X-Folder-Name` field is identified as a header, meaning you'll need to handle it accordingly when performing an API call outside of API Docs. The screenshot below shows you how this is handled in the great HTTP testing client Insomnia:

<p>
<img src="/images/12/sftp-create-directory.png" width="600">
</p>

### Uploading Files

To upload a file, you'll send a `POST` request to the SFTP API. You must specify the file name, and can do so either via the URL like this:

https://example.com/api/v2/sftp/dreamfactory-ebook.png

Alternatively you can use the `X-File-Name` in header to identify file name.

Upload size limitations aren't so much a function of DreamFactory as they are web server configuration. For instance, Nginx' default maximum body size is 1MB, so if you plan on uploading files larger than that you'll need to add the following configuration directive to your `nginx.conf` file:

	client_max_body_size 10M;

You'll know if the `client_max_body_size` setting isn't suffice because you'll receive a `413 Request Entity Too Large` HTTP error if the file size surpasses the setting.

Additionally, you'll receive a `413 Payload Too Large` HTTP error if PHP's `upload_max_filesize` setting isn't suffice. To change this setting you'll open the `php.ini` file associated with the PHP-FPM daemon and modify it accordingly:

	upload_max_filesize = 100M

Don't forget to restart the respective daemons after making changes to the Nginx and PHP configuration files.

### Downloading Files

To download a file you'll send a GET request to the SFTP API, identifying the path and file name in the URL:

	https://demo.dreamfactory.com/api/v2/sftp/Marketing/df-ebook.png

If you're using a tool such as Insomnia, you can view many file types within the response preview:

<p>
<img src="/images/12/sftp-download-file.png" width="600">
</p>

## Creating an AWS S3 REST API


The DreamFactory AWS S3 connector offers a convenient REST-based interface for interacting with S3 objects and buckets. Supporting all of the standard CRUD operations, it's easy to interact with and manage your S3 data. Further, because the S3 API is native to DreamFactory, it's easy to integrate S3 actions alongside other API-driven tasks such as:

* Uploading a newly registered user avatar to S3 while inserting the user's registration information into a database
* Emailing a website visitor a link to your product PDF after writing the user's email address to your marketing automation software
* Creating a new S3 bucket as part of a Dev Ops workflow

Further, because the API is part of the DreamFactory API management ecosystem, you can apply DreamFactory's context-specific role-based access * controls to your AWS S3 API, attach API request volume limiting controls, and audit access logs via DreamFactory's Logstash integration.



### Generating the AWS S3 API and Companion Documentation

To generate your S3 API, log into DreamFactory and click on the `Services` tab, then click the `Create` link located on the left side of the page. Click on the `Select Service Type` dropdown, navigate to the `File` category, and select `AWS S3`:

<p>
<img src="/images/aws-s3/new-service.png" width="600">
</p>

You'll be prompted to supply an API name, label, and description. Keep in mind the name must be lowercase and alphanumeric, as it will be used as the namespace within your generated API URI structure. The label and description are used for reference purposes within the administration console so you're free to title these as you please:

<p>
<img src="/images/aws-s3/service-info.png" width="600">
</p>

Next, click the `Config` tab. There you'll supply the AWS S3 bucket connection credentials. There are however only 3 required fields:

* **Access Key ID**: An AWS account root or IAM access key.
* **Secret Access Key**: An AWS account root or IAM secret key.
* **Region**: Select the region to be accessed by this service connection.
* **Container**: Enter a Container (root directory) for your storage service. It will be created if it does not exist already.


<p>
<img src="/images/aws-s3/service-config.png" width="600">
</p>


After saving your changes, head over to the `API Docs` tab to review the generated documentation. You'll be presented with a list of generated endpoints. the operations listed **above** for an SFTP connector are the same for the AWS S3 Container as well. One of the examples is listed below


### Downloading Files

To download a file you'll send a GET request to the AWS S3 API, identifying the path and file name in the URL:

	https://abhii.apps.dreamfactory.com/api/v2/dreamfactorys3/DreamFactoryExtends&EnhancesERPFunctionality.pdf

If you're using a tool such as Insomnia, you can view many file types within the response preview:

<p>
<img src="/images/aws-s3/s3call.png" width="600">
</p>

Snapshot of the file on S3


<p>
<img src="/images/aws-s3/bucket.png" width="600">
</p>
