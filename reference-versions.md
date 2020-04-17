---
copyright:
  years: 2016,2018
lastupdated: "2018-12-05"

keywords: mongodb, compose

subcollection: compose-for-mongodb

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Versions

You can find the list of available versions on the {{site.data.keyword.composeForMongoDB}} [catalog page](https://{DomainName}/catalog/compose-for-mongodb) or from the [GET /2016-07/deployments/:id/versions](https://apidocs.compose.com/reference#2016-07-get-deployments-versions) API endpoint.

## Preferred Version

The preferred version is typically the newest version of the MongoDB database that is available for {{site.data.keyword.composeForMongoDB}}. It's the default in the drop-down version selector on the catalog page, and the version that is automatically provisioned by API if no version is specified in the call.

### New Preferred Version Protocol

When a new version is made available, its release is announced and it is available for deployment. Following release there is an approximately 7-day window where the newest version is available, but it is not the preferred version. This window gives you a chance to deploy and test the new version, while the current version is still available. At the end of the 7-day window the new version is set as the preferred version, or a new date for this change is announced.
