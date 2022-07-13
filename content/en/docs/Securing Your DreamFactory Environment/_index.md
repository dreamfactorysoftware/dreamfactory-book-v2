---
title: "Securing Your DreamFactory Environment"
linkTitle: "Securing Your DreamFactory Environment"
weight: 10
---

The DreamFactory platform is built atop the [Laravel](https://www.laravel.com) framework. Laravel is an amazing PHP-based framework that in just a few short years has grown in popularity to become one of the today's most popular framework solutions. There are several reasons for its popularity, including a pragmatic approach to convention over configuration, security-first implementation, fantastic documentation, and a comprehensive ecosystem (in addition to the framework itself, the Laravel team maintains a number of sibling projects, among them an e-commerce framework called Spark, an application adminstration toolkit called Nova, and an application deployment service called Envoyer). Regardless, like any application you're going to want to learn all you can about how to best go about maintaining and securing the environment.

## Security

### CORS Security

CORS (Cross-Origin Resource Sharing) is a mechanism that allows a client to interact with an API endpoint which hails from a different domain, subdomain, port, or protocol. DreamFactory is configured by default to disallow all outside requests, so before you can integrate a third-party client such as a web or mobile application, you'll need to enable CORS.

You can modify your CORS settings in DreamFactory under the `Config` tab. You'll be presented with the following interface:

<img src="/images/10/cors.png" width="800" alt="DreamFactory CORS Configuration">

To enable CORS for a specific originating network address such and an IP address or domain, press the plus `+` button located at the top of the screen. Doing so will enable all of the configuration fields found below:

* `Path`: The `Path` field defines the path associated with the API you're exposing via this CORS entry. For instance if you've created a Twitter API and would like to expose it, the path might be `/api/v2/twitter`. If you want to expose all APIs, use `*`.

* `Description`: The `Description` field serves as a descriptive reference explaining the purpose of this CORS entry.

* `Origins`: The `Origins` field identifies the network address making the request. If you'd like to allow more than one origin (e.g. www.example.com and www2.example.com), separate each by a comma (`www.example.com,ww2.example.com`). If you'd like to allow access from anywhere, supply an asterisk `*`.

* `Headers`: The `Headers` field determines what headers can be used in the request. Several headers are whitelisted by default, including `Accept`, `Accept-Language`, `Content-Language`, and `Content-Type`. When set, DreamFactory will send as part of the preflight request the list of declared headers using the `Access-Control-Allow-Headers` header.

* `Exposed Headers`: The `Exposed Headers` field determines which headers are exposed to the client.

* `Max Age`: The `Max Age` field determines how long the results of a preflight request (the information found in the `Access-Control-Allow-Methods` and `Access-Control-Allow-Headers` headers) can be cached. This field's value is passed along to the client using the `Access-Control-Max-Age` field.

* `Methods`: The `Methods` field determines which HTTP methods can be used in conjunction with this CORS definition. The selected values will be passed along to the client using the `Access-Control-Allow-Methods` field.

* `Supports Credentials`: The `Supports Credentials` field determines whether this CORS configuration can be used in conjunction with user authentication. When enabled, the `Access-Control-Allow-Credentials` header will be passed and set to `true`.

* `Enabled`: To enable the CORS configuration, make sure this field is enabled.

Always make sure your `CORS` settings are only set for the appropriate "scheme/host/port tuple" to ensure you are observing the maximum security you can by only allowing cross origin resources access when there is no other way around it.  For a great explanation of `CORS`, refer to these articles:

* [Cross-Origin Resource Sharing](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)
* [Do You Really Know CORS](https://performantcode.com/web/do-you-really-know-cors)

### Securing Your Web Traffic

From a networking standpoint DreamFactory is a typical web application, meaning you can easily encrypt all web traffic between the platform and client using an SSL certificate. Unless you've already taken steps to add an SSL certificate to your web server, by default your DreamFactory instance will run on port 80, which means all traffic between your DreamFactory server and client will be unencrypted and therefore subject to capture and review. To fix this, you'll want to install an SSL certificate. One of our favorite resources to create SSL certificates is [Let's Encrypt](https://letsencrypt.org/getting-started/).

Below are resources on how to add an SSL cert to your web server:

1. [Nginx](https://nginx.org/en/docs/http/configuring_https_servers.html)
	* [Nginx YouTube Video](https://www.youtube.com/watch?v=X3Pr5VATOyA)
2. [Apache YouTube Example](https://www.youtube.com/watch?v=NfUoiv4FTSs)

### Securing Your Credentials

When generating APIs using DreamFactory's native connectors, you'll logically need to supply a set of credentials so DreamFactory can connect to and interact with the underlying data source. These credentials are stored in the system database, and are encrypted using [AES-256](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) encryption. The credentials are decrypted on-the-fly when DreamFactory connects to the destination data source, and are never cached in plaintext.

### Suppressing Errors

When running DreamFactory in a production environment, be sure to set the `.env` file's `APP_ENV` value to `production` and `APP_DEBUG` to `false`. Leaving it set to `local` will result in detailed error-related information being returned to the client rather than quietly logged to the log file. When set properly in a production environment, your `.env` file will look like this:

```php
...
APP_DEBUG=false
```

## Environment this installation is running in: local, production (default)

```
APP_ENV=production
```

### Separating the Web Administration Interface from the Platform

New DreamFactory users often conflate the web administration interface with the API platform; in fact, the web administration interface is just a client like any other. It just so happens that the DreamFactory team built this particular interface expressly for managing the platform in an administrative capacity. This interface interacts with the platform using a series of administrative APIs exposed by the platform, and accessible only when requests are accompanied by a session token associated with an authenticated administrator.

By default this interface runs on the same server as the platform itself. Some users prefer to entirely separate the two, running the interface in one networking environment and entirely isolating the platform in another.

The interface is maintained within an Angular application hosted in a public GitHub repository. The `README` file contains instructions regarding both building the Angular app and separating it from the platform. To learn more head over to [the GitHub repository](https://github.com/dreamfactorysoftware/df-admin-app).

### Best Practices

For database-backed APIs, create the API using a database account privileges that closely correspond to your API privilege requirements. For instance, if the database includes a table called `employees` but there is no intention for this table to be accessible via the API, then configure the proxy user's privileges accordingly.

Never use a blanket API key for your APIs! Instead, create roles which expressly define the level of privileges intended to be exposed via the API, and then associate the role with a new App and corresponding API Key. Don't be afraid to create multiple roles and therefore multiple corresponding API keys if you'd like to limit API access in different ways on a per-client or group basis.

Should you need to make API documentation available to team members, use DreamFactory's user-centric role assignment feature to make solely the documentation available to the team members, rather than granting unnecessary administrative access.