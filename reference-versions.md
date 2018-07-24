---
copyright:
  years: 2016,2018
lastupdated: "2018-06-05"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Versions

Deployable Versions| Preferred Version
----------|-----------
3.2.10, 3.2.11, 3.2.18, 3.4.10 | 3.2.18
{: caption="Table 1. MongoDB versions" caption-side="top"}

## Preferred Version

The preferred version is typically the newest version of the MongoDB database that is available for {{site.data.keyword.composeForMongoDB}}. It is the version that is the default in the drop-down version selector on the catalog page. It is also the version that is automatically provisioned by API if no version is specified in the call.

### New Preferred Version Protocol

When a new version is made available, its release is announced and it is available for deployment. Following release there is an approximately 7-day window where the newest version is available, but it is not the preferred version. This window gives you a chance to deploy and test the new version, while the current version is still available. At the end of the 7-day window the new version is set as the preferred version, or a new date for this change is announced.

The list of versions available to provision is on the {{site.data.keyword.composeForMongoDB}} [catalog page](https://console.{DomainName}/catalog/services/compose-for-mongodb).

To get a current list of available versions for your {{site.data.keyword.composeForMongoDB}} service you can use the 
[GET /2016-07/deployments/:id/versions](https://apidocs.compose.com/v1.0/reference#2016-07-get-deployments-versions) endpoint.
