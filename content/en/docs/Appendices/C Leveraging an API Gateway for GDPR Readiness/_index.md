---
title: "Appendix C: Leveraging an API Gateway for GDPR Readiness"
linkTitle: "C. Leveraging an API Gateway for GDPR Readiness"
weight: 3
---

## Executive Overview

API platforms are recognized as the engine driving digital transformation, enabling the
externalization of IT assets across enterprise and customers boundaries. By adopting this new
architecture, enterprises can transform the way they do business with unprecedented time to
value and entirely new engagement models to monetize IT as a business.

What is less promoted, however, is the power of full-lifecycle API platforms in addressing
regulatory compliance requirements. There is a common thread in IT’s modernization and
regulatory compliance agendas, being the repackaging of data systems as a shared resource that
is able to support a myriad of new consumption models. The cornerstone of this repackaging is
implementing Data Gateways to enable and manage secure and monitorable access to enterprise
data systems.

This paper outlines how to leverage an API platform to retrofit existing infrastructure for
"GDPR readiness", essentially as a byproduct of implementing a modern architecture for digital
transformation.

## General Data Protection Regulation (GDPR)

While having a simple stated goal of returning control of any European citizen’s private data
back to the consumer- the implications spread far past the EU and touch every organization that
handles consumer data directly or through a partner. The penalties for non-compliance are
severe, potentially 4% of global revenue - per incident! So as you might guess, companies are
paying close attention to the regulation and scrambling to ensure compliance by the rapidly
approaching start date of May 25, 2018.

## PII (Personally Identifiable Information)

PII is the specific data that GDPR regulates. PII is data that can be used to distinguish an
identity and includes social security numbers, date and place of birth, mother’s maiden name,
biometric records, etc. PII also includes logged behavioral data such as data collected from a
user’s web session.

## Data Protection Officer (DPO)

A data protection officer is a new leadership role required by the General Data Protection
regulation for companies that process/store large amounts of personal data as well as any public
authority (national, state, or local government body). DPO’s are responsible for overseeing the
organization’s data protection strategy and implementation to ensure compliance with GDPR
requirements.

## API Automation Platform

As the name suggests, API Automation platforms automate the creation of APIs and provide a
gateway for secure access to data endpoints. A full lifecycle platform combines API
management, API generation, and API orchestration into a single runtime. Also referred to as
Data Gateways, they provide discovery, access, and control mechanisms surrounding enterprise
data that needs to be shared with external consumers. Ideally, a Data Gateway is non-disruptive
to existing infrastructure - meaning that it can be retrofitted versus "rip and replace" approach.

A full lifecycle data gateway automates several key functions:

1. Facilitating the creation of bidirectional data APIs for CRUD operations
2. Providing a secure gateway to control access, track, log and report on data usage.
3. Discovering key aspects of data to generate catalogs and inventory
4. Orchestrating APIs to chain operations within and between data systems.
5. Packaging and containerizing data access for portability

The remainder of the document looks at specific requirements in GDPR and illustrates how the
DreamFactory API platform can help you bake in GDPR readiness into your infrastructure.
DreamFactory’s API platform is unique in that it is the only "plug and play" platform,
automatically generating a data gateway for any data resource.

<p>
<img src="/images/appendix/enabling-privacy-by-design.png" width="600">
</p>

## Right to be forgotten

This is a primary requirement and allows consumers to demand that their private data be deleted.
To be able to do this in a timely manner an organization needs to know where all instances of
this data exist in their internal systems as well as the partner ecosystems (data supply chain).
Capabilities of DreamFactory's Data Gateway relevant to "right to be forgotten" include:

1. Auto-generation of APIs for any data resource, SQL, NoSQL, cloud storage, and
commercial software systems such as Salesforce.com. Regardless of how or where you
structure your data, DreamFactory provides a cons   istent and reusable interface for
on-demand CRUD operations, including record deletion.
2. Data Cataloging. Dreamfactory automatically discovers and catalogs all of the assets of a
data resource including metadata, stored procedures, and data relationships providing you
with a holistic view of your data assets from a single pane of glass.
3. Data Mesh. DreamFactory allows you to create virtual data relationships between
databases and perform operations on all of them in a single API call.
4. Role based access control. This allows your data supply chain to securely share PII with
their partners and determine which operations can be performed on it.
5. Pre/Post processing of API calls allows you to notify other systems of change
6. Provide access to everything via REST to ensure standards based integration with
analysis and workflow systems.

Moreover, DreamFactory is non-disruptive to existing infrastructure and easily bolts-on to
retrofit existing systems for compliance.

## Data Portability

Consumers have the right to move their data between vendors, and a vendor is obligated to
provide this data to them in a timely manner. An example would be a customer can demand that
their PII from one online banking vendor be transferred to another.

DreamFactory normalizes the interface to data. Regardless of how (SQL, NoSQL, storage) or
where (cloud, on-prem) it persists you can expose it to data consumers as a single reusable API.
This enables external systems to connect to the data using the same interface regardless of its
underlying structure or location.

One important capability of DreamFactory’s gateways is that it handles all of the firewall
plumbing required to access on-prem systems in cloud portals, providing a secure access
mechanism across the data supply chain.
Governance

As with any business critical regulation, enterprises need to gauge and track compliance. GDPR
places increased emphasis on governance as the penalties can be so severe as to jeopardize the
viability of a company.

DreamFactory bakes compliance readiness into the gateway between your data and all of the
consumers of your data, with:

1. Role based access control at various levels of granularity (e.g. app, record)
2. Logging of all API calls accessing data records
3. Limiting of API Calls to preemptively protect against attacks
4. Reporting on all data activity

<p>
<img src="/images/appendix/gateway.png" width="600">
</p>

## What databases are we talking about?
The DreamFactory platform automatically creates RESTified Data Gateways for the following
databases:

<p>
<img src="/images/appendix/databases.png" width="600">
</p>

As of the publication date of this paper, a SAP Hana integration is in late beta, while GraphQL
support is additonally offered.

A full list of DreamFactory’s database connections [can be seen here](https://www.dreamfactory.com/hub/categories/databases/). The scope of platform
capabilities, including SOAP to REST, EXCEL to REST, as well as integration with SMS, push
notifications, Apple/Google technologies and more [can be viewed here](https://www.dreamfactory.com/hub/categories/all/).

## Summary

Progressive organizations are re-architecting their infrastructure with API platforms to get ahead
of the competition. By taking this approach, enterprises have been able to share their data assets
safely with any data consumer they need to support - whether to turbo charge new mobile and
web app ecosystems, integrate cross-enterprise data, or create new business opportunities with
partners & customers.

Now, with GDPR, there is an emerging and mission critical consumer of enterprise data that an
API platform can support: the Data Protection Officer.

The DreamFactory Data Gateway is unique in that it is a unified runtime that automatically
enables, controls, and monitors access for any enterprise data system.

To see how DreamFactory can help your organization, [please request a demo](https://www.dreamfactory.com/demo) to see how your
digital transformation initiatives can be both automated and readied for GDPR regulations.