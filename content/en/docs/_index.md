
---
title: "Getting Started With DreamFactory"
linkTitle: "Guides"
no_list: true
weight: 20
menu:
  main:
    weight: 20
---

Welcome to the DreamFactory platform! Whether you’re an open source user, or a paid customer taking advantage of DreamFactory’s advanced capabilities, we wrote this guide to help you begin incorporating the platform into your organization in the most efficient way possible.

{{< alert color="warning" >}}
This guide is under heavy development, and is a work-in-progress. Check back often as we'll be publishing updates regularly!
{{< /alert >}}

## About this Guide

This guide consists of numerous chapters covering the following topics:

### [Introducing REST and DreamFactory](./introducing-rest-and-dreamfactory/)

So why would you want to use the DreamFactory platform in the first place? It’s likely because even world class developers and administrators are faced with ever-increasing complexity due in large part to the extraordinary number of internal and third-party data sources which must be integrated with mobile and web applications, ERP solutions, and myriad other services. In this chapter you’ll learn how DreamFactory can bring order to this chaos by introducing silo breaking capabilities to your enterprise, offering a platform for which not only can you auto-generate the APIs used to connect to these data sources, but also secure and monitor them.

### [Installing and Configuring DreamFactory](./installing-and-configuring-dreamfactory/)

Much of the DreamFactory platform is open source, with the code made available via GitHub. But this doesn’t mean you have to be a command-line wizard to begin generating APIs in a flash. In this chapter you’ll learn how to install and configure DreamFactory regardless of your operating system or experience level. We’ll also talk about configuring DreamFactory to suit your specific needs, and highlight key configuration changes which will make your life much easier.

### [Generating a Database-backed API](./generating-a-database-backed-api)

After installing your DreamFactory instance, you'll naturally want to take the platform for a test drive. Most users desire to begin by generating a database API, because the advantages of doing so are so evident. By merely supplying a set of authentication credentials, DreamFactory will generate an API for any of an array of popular databases, including MySQL, SQL Server, Oracle, PostgreSQL, MongoDB, and others. Once generated, you can immediately beging issuing REST API calls to carry out record creation, retrieval, modification, and deletion tasks. You’ll also be able to perform advanced queries using the REST API, including filters, grouping, joins, limiting, and more.

### [Advanced Database API Features](./advanced-database-api-features)

The ability to instantaneously generate a database API is a productivity game changer. However that alone won't be enough to completely replace your use of SQL. In this chapter you'll learn about DreamFactory's advanced database API features, including transaction support, using database functions within API calls, and more.

### [Authenticating and Monitoring Users](./authenticating-your-apis)

From the moment your API is generated, rest assured it is protected by at minimum a complicated API key. However this represents only the beginning in terms of your options regarding securing an API. You can use DreamFactory's user authentication and authorization features to provide user-specific login via a variety of authentication solutions, including basic auth, LDAP, Active Directory, and SSO. In this chapter you'll learn all about these solutions, and additionally learn how to use DreamFactory's rate limiting and logging capabilities to closely monitor request volume and behavior.

### [Creating Scripted Services and Endpoints with DreamFactory](./creating-scripted-services-and-endpoints)

DreamFactory offers an extraordinarily powerful solution for creating APIs and adding business logic to existing APIs using a variety of popular scripting languages including PHP, Python (versions 2 and 3), Node.js, and JavaScript. In this chapter we'll walk you through several examples which will hopefully spur the imagination regarding the many ways in which you can take advantage of this great feature.

### [Integrating Business Logic Into Your DreamFactory APIs](./integrating-business-logic-into-your-apis)

The ability to merely auto-generate a REST API is already going to produce an immediate productivity boost, however eventually you're going to want to tweak one or more API endpoints' default behavior to accommodate more sophisticated project requirements. Most often this involves using DreamFactory’s scripting feature, which allows you to write custom code used to validating input parameters, call other APIs, and much more. In this chapter we'll walk through several real-world examples which highlight how easy it is to extend your API endpoints with one of four supported scripting engines (NodeJS, PHP, and Python).

### [Limiting and Logging API Requests](./limiting-and-logging-api-requests)

In this chapter you'll learn how to use DreamFactory's API limiting and logging capabilities to assign and monitor access to your restricted APIs.

### [Securing Your DreamFactory Environment](./securing-your-dreamfactory-environment)

While DreamFactory is already secure, and relatively maintenance free, there are quite a few modifications you can make to enhance your instance. In this chapter we'll provide a wide ranging overview of the many changes you can make to maintain, and secure your environment.

### [Performance Considerations](./performance-considerations)

DreamFactory is already very performant out of the box, however logically you'll want to do everything practical to ensure your instance can really fly. In this chapter we'll provide some benchmarks, and guidance regarding how to properly tune your instance environment.

### [Installing DreamFactory on a Raspberry Pi](./installing-dreamfactory-on-raspberry-pi)

DreamFactory's a really fascinating project in that its architecture is suitable for infinite horizontal and vertical scaling, yet can be run on small appliance-like devices such as the Raspberry Pi. In this chapter we'll talk about a few configuration-related gotchas associated with installing DreamFactory's prerequisites on the Raspberry Pi.

### [Demo DreamFactory Applications](./demo-dreamfactory-applications)

In this chapter we'll provide a few JavaScript-based examples demonstrating how web applications can interact with DreamFactory-exposed APIs.

### [Creating File System APIs](./creating-file-system-apis)

DreamFactory supports file system-based API generation, meaning you can create REST APIs for AWS S3, SFTP, local file storage, and more. In this chapter we'll show you how.

### [Integrating Salesforce Data Using DreamFactory](./integrating-salesforce-data)

In this chapter you'll learn how to configure the connector, and then interact with your Salesforce database using the DreamFactory-generated REST API.

### [Using DreamFactory's Remote HTTP and SOAP Connectors](./using-remote-http-and-soap-connectors)

Although the DreamFactory Platform is best known for the ability to generate REST APIs, many also take advantage of the platform's Remote Service connectors. In this chapter you'll learn how to proxy third-party HTTP APIs through DreamFactory, and additionally mount an existing SOAP service to DreamFactory and interact with it using an auto-generated REST interface.

### [Using the System APIs](./using-the-system-apis)

All DreamFactory versions include a web-based administration console used to manage all aspects of the platform. While this console offers a user-friendly solution for performing tasks such as managing APIs, administrators, and business logic, many companies desire to instead automate management tasks through scripting. In this chapter you'll learn how to interact with the system APIs to easily manage multiple DreamFactory environments, and integrate DreamFactory features into third-party applications such as an API monetization SaaS.

### [Migrating Your System Database to a New Instance](./migrating-your-system-database-to-a-new-instance)

In this chapter you'll learn how to safely migrate existing data to a new instance in a variety of ways.

### [Modifying the Service Definition](./modifying-the-service-definition)

We explore the possibilities of customizing your API Docs tab for customized documentation.

### [Appendix A. Configuration Parameter Reference](./appendices/a-configuration-parameter-reference)

DreamFactory is packed with features capable of being tweaked via configuration parameters. These parameters can be managed as server environment variables or within a `.env` file found in the platform's root directory. This appendix defines all available parameters.

### [Appendix B. Security FAQ](./appendices/b-security-faq)

Customers tasked with managing sensitive data often ask rigorous questions regarding company and platform security policies.

### [Appendix C. Leveraging an API Gateway for GDPR Readiness](./appendices/c-leveraging-an-api-gateway-for-gdpr-readiness)

This paper outlines how to leverage an API platform to retrofit existing infrastructure for
"GDPR readiness", essentially as a byproduct of implementing a modern architecture for digital
transformation.

### [Appendix D. Examining DreamFactorys Architecture](./appendices/d-architecture-faq)

This paper answers frequently asked questions pertaining to Dreamfactorys system and an anatomy of various API calls as they travel
through the system.

### [Appendix E. Scaling DreamFactory](./appendices/e-scalability)

This paper is designed to provide information to enterprise customers about how to scale a DreamFactory Instance. The sections below talks about horizontal, vertical, and cloud scaling capabilities.

## More Ways to Learn

Hopefully you'll find this guide indispensable, however it's just one of several learning resources at your disposal. Check out the following links to learn more about what else is available!

### The DreamFactory Wiki

The [DreamFactory wiki](https://wiki.dreamfactory.com) is our definitive reference guide, providing a terse but comprehensive summary of the platform's key features. Here you'll find installation instructions, scripting examples, and a great deal of other information.

### Videos

Dozens of videos are available via the [DreamFactory Youtube channel](https://www.youtube.com/user/dreamfactorysoftware/videos). Also check out [DreamFactory Academy](https://academy.dreamfactory.com/)

### The DreamFactory Forum

Volunteers and DreamFactory staff alike regularly patrol our [community forum](https://community.dreamfactory.com/). If Stack Overflow is preferred, be sure to tag the question using the [dreamfactory](https://stackoverflow.com/questions/tagged/dreamfactory) tag!

### API Cost Calculator

Wondering how much it costs to build an API? Check out our [API calculator](https://calculator.dreamfactory.com/), which calculates API development costs based on numerous research studies and our own interactions with thousands of customers around the globe.

## Contact us

Do you have any input or questions about this guide, or the DreamFactory platform? We’d love to hear from you! E-mail our [support team](mailto:dspsupport@dreamfactory.com) with your feedback.


