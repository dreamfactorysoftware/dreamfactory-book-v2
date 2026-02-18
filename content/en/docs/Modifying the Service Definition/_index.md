---
title: "Modifying the Service Definition"
linkTitle: "Modifying the Service Definition"
description: "Learn how to modify DreamFactory service definitions to customize API behavior, override default schemas, and configure service-level parameters for database and HTTP connectors."
weight: 19
---

There can be a variety of reasons for wanting to modify the pre-defined API documentation that is generated in the API Docs tab. We will cover modifying the documentation and exporting the documentation to have a developer portal without any coding.

## Exporting API Documentation

When in the API Docs for any Service you will see `Download Service Doc` at the top of the page. This will download the documentation in JSON format to use elsewhere. In this example we are downloading the Service Definition and importing it into [SwaggerHub](https://app.swaggerhub.com/). This tool enables us to leverage the DreamFactory documentation, modify the endpoints, and expose it as a developer portal.

<p>
<img src="/images/17/download-service-doc.png" width="800" alt="Download Service Doc">
</p>

We can then use a tool such as [JSON2YAML](https://www.json2yaml.com/) to convert our Service Definition from JSON to YAML. Now we can paste it into [SwaggerHub](https://app.swaggerhub.com/). You might notice that it is not playing well and that is because we need to point it to our DreamFactory instance.

Under the `servers` section you will want to add your DreamFactory instance details like below:

```
servers:
  - url: '{server}/api/v2/{service_name}'
    description: 'DreamFactory Demo'
    variables:
      server:
        default: https://YOUR_INSTANCE.com
```

Now you have a fully functioning developer portal for your API!

<p>
<img src="/images/17/swaggerhub-docs.png" width="800" alt="Download Service Doc">
</p>

## Modifying Existing API Documentation

To modify existing API Documentation we will still need to download the Service Documentation. After we have the documentation we cannot modify it and change that existing Services documentation, but rather create a new Service with that documentation.

### Customizing your Documentation

To customize your API Documentation it is fairly straightforward. You can begin by briefly taking a look at the existing structure and use it as a boiler plate for your own custom documentation. In this example we will be removing the `/_schema`,`/_function`, and `/_proc` endpoints entirely.

First we start by modifying the Service Definition we downloaded. We can simply navigate down to "paths" and begin deleting the endpoints from the documentation. As we go through deleting these endpoints you may also notice the summaries and descriptions can be modified to your liking as well.

### Making a New API

Now we can import this custom documentation to DreamFactory via the [HTTP connector](../using-remote-http-and-soap-connectors/#proxying-a-remote-http-api). We start by specifying the base URL for the API we customized and providing an API Key for the Service.

<p>
<img src="/images/17/custom-api-config.png" width="800" alt="Custom API Config tab">
</p>

Then we move to the `Service Definition` tab, where we simply copy and paste the documentation.

<p>
<img src="/images/17/custom-service-def.png" width="800" alt="Custom API Service Definition tab">
</p>

Once done we can save it and navigate to the `API Docs` to see our custom documentation.

<p>
<img src="/images/17/custom-api-doc.png" width="800" alt="Custom API Docs tab">
</p>