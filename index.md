---

copyright:
  years: 2016,2017
lastupdated: "2017-10-23"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Getting started with Compose for MongoDB
{: #getting-started-with-compose-for-mongodb}

{{site.data.keyword.composeForMongoDB_full}} uses the powerful indexing and querying, aggregation, and wide driver support of MongoDB that has made it the go-to JSON data store for many startups and enterprises. {{site.data.keyword.composeForMongoDB}} offers an easy, auto-scaling deployment system. It delivers high availability and redundancy, automated and on-demand no-stop backups, monitoring tools, integration into alert systems, performance analysis views, and much more, all in a clean, simple user interface.
{:shortdesc}

**Note:** Any Compose service instances that were provisioned before 14 September 2016 that are still active can still be used and directly accessed at [https://www.compose.com/](https://www.compose.com). Any Compose service instance that is provisioned from this point forward is directly accessed and used within your {{site.data.keyword.Bluemix_notm}} account.

## Creating a Compose for MongoDB service instance

[Create a {{site.data.keyword.composeForMongoDB}} instance](https://console.ng.bluemix.net/catalog/services/compose-for-mongodb/).

When you create an instance of the service, ensure that you choose both a name for your service and a credential name. Leave the service unbound; you can connect an application to your service later by using the credentials that are provided when the service is provisioned.  The various credential values are listed in the *Available credentials* section.

When you provision your {{site.data.keyword.composeForMongoDB}} instance you can choose the *Standard* or *Enterprise* plans. With the *Enterprise* plan, you can provision your {{site.data.keyword.composeForMongoDB}} instance into an available {{site.data.keyword.composeEnterprise}} cluster. {{site.data.keyword.composeEnterprise}} provides the security and isolation required by enterprise compliance and uses dedicated networking to ensure the performance of the deployed databases. See the [Compose Enterprise documentation](../ComposeEnterprise/index.html) for more details.

## Managing Compose for MongoDB

You can manage your service from the service dashboard. Here you can find information about your {{site.data.keyword.Bluemix_notm}} Compose database and how to connect to it. You can also:
- manage your backups
- allocate more resources for your service
- use whitelists to restrict access to your databases. 
For more information, see [Settings](./dashboard-settings.html).

## Connecting to Compose for MongoDB

You can connect to your service using the credentials that are created along with the service, or with the connection strings and command line that are provided in the *Overview* tab of your service dashboard.

## Connecting a {{site.data.keyword.Bluemix_notm}} application to Compose for MongoDB

To connect a {{site.data.keyword.Bluemix_notm}} application to your service, use the credentials that are created along with the service. You can find information on how to connect a {{site.data.keyword.Bluemix_notm}} application to a {{site.data.keyword.composeForMongoDB}} service in [Connecting a {{site.data.keyword.Bluemix_notm}} Application](./connecting-bluemix-app.html).

## Connecting to Compose for MongoDB from outside {{site.data.keyword.Bluemix_notm}}

If you want to connect to {{site.data.keyword.composeForMongoDB}} from outside {{site.data.keyword.Bluemix_notm}}, you can use the provided connection strings or command line. You can find information on how to connect in [Connecting an external application](./connecting-external.html).
