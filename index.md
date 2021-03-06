---

copyright:
  years: 2016,2020
lastupdated: "2020-04-13"

keywords: mongodb, compose

subcollection: ComposeForMongoDB

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:important: .important}


# About {{site.data.keyword.composeForMongoDB}}
{: #about}

{{site.data.keyword.composeForMongoDB_full}} uses the powerful indexing and querying, aggregation, and wide driver support of MongoDB that makes it the go-to JSON data store for many startups and enterprises. {{site.data.keyword.composeForMongoDB}} offers an easy, auto-scaling deployment system. It delivers high availability and redundancy, automated and on-demand no-stop backups, monitoring tools, integration into alert systems, performance analysis views, and more, in a clean, simple user interface.
{:shortdesc}

The latest and greatest MongoDB service is [{{site.data.keyword.databases-for-mongodb_full}}](/docs/databases-for-mongodb?topic=databases-for-mongodb-getting-started)! You can still create and use {{site.data.keyword.composeForMongoDB}} deployments, but please consider moving to {{site.data.keyword.databases-for-mongodb}}. If you already have a {{site.data.keyword.composeForMongoDB}} deployment, consider testing out [Migrating to Databases for MongoDB](/docs/ComposeForMongoDB?topic=ComposeForMongoDB-migrating).
{: .important}

**Note:** Compose service instances that were provisioned before 14 September 2016 that are still active can still be directly accessed at [https://www.compose.com](https://www.compose.com). Any new Compose service instance that is provisioned is directly accessed and used within your {{site.data.keyword.cloud}} account.

## Creating a {{site.data.keyword.composeForMongoDB}} service instance

You can create a {{site.data.keyword.composeForMongoDB}} service from the [{{site.data.keyword.composeForMongoDB}} page](https://{DomainName}/catalog/compose-for-mongodb) in the {{site.data.keyword.cloud_notm}} catalog.

Choose a service name, and a region, organization, and space to provision the service in. You can use the **Select a database version** field to choose from the available database versions.

When you provision your {{site.data.keyword.composeForMongoDB}} instance, you can choose the *Standard* or *Enterprise* plans. With the *Enterprise* plan, you can provision your {{site.data.keyword.composeForMongoDB}} instance into an available {{site.data.keyword.composeEnterprise}} cluster. {{site.data.keyword.composeEnterprise}} provides the security and isolation that is required by enterprise compliance and uses dedicated networking to ensure the performance of the deployed databases. See the [{{site.data.keyword.composeEnterprise}} documentation](/docs/ComposeEnterprise?topic=compose-enterprise-about) for more details.

## Managing {{site.data.keyword.composeForMongoDB}}

You can manage your service from the service dashboard. Here you can find information about your {{site.data.keyword.cloud_notm}} Compose database and how to connect to it. You can also:
- Manage your backups
- Allocate more resources for your service
- Use whitelists to restrict access to your databases. 

For more information, see [Settings](/docs/ComposeForMongoDB?topic=ComposeForMongoDB-dashboard-settings).

{{site.data.keyword.composeForMongoDB}} relies on Cloud Foundry roles to manage access to the service. Only users with the Developer role can see or use the service dashboard. For more information on Cloud Foundry roles, see the [Cloud Foundry access](/docs/iam?topic=iam-cfaccess#cfaccess) page and the [Managing Cloud Foundry access](/docs/iam?topic=iam-mngcf#mngcf) page.
{: tip}

## Connecting to {{site.data.keyword.composeForMongoDB}}

You can connect to your service by using the credentials that are created along with the service, or with the connection strings and command line that are provided in the *Overview* tab of your service dashboard.

## Connecting an {{site.data.keyword.cloud_notm}} application to {{site.data.keyword.composeForMongoDB}}

To connect an {{site.data.keyword.cloud_notm}} application to your service, use the credentials that are created along with the service. You can find information on how to connect an {{site.data.keyword.cloud_notm}} application to a {{site.data.keyword.composeForMongoDB}} service in [Connecting an {{site.data.keyword.cloud_notm}} Application](/docs/ComposeForMongoDB?topic=ComposeForMongoDB-ibmcloud-cf-app).

## Connecting to {{site.data.keyword.composeForMongoDB}} from outside {{site.data.keyword.cloud_notm}}

If you want to connect to {{site.data.keyword.composeForMongoDB}} from outside {{site.data.keyword.cloud_notm}}, you can use the provided connection strings or command line. You can find information on how to connect in [Connecting an external application](docs/ComposeForMongoDB?topic=ComposeForMongoDB-external-app).

## Creating a web application by using MEAN stack

You can use {{site.data.keyword.composeForMongoDB}} as part of a MEAN stack to create a web application. Follow the steps in the [Modern Web Application using MEAN stack](/docs/tutorials?topic=solution-tutorials-mean-stack) tutorial to create a web application composed of a Mongo DB, Express web framework, Angular front-end framework, and a Node.js runtime.
