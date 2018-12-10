---

copyright:
  years: 2016,2018
lastupdated: "2017-06-17"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Connecting an {{site.data.keyword.cloud_notm}} application

To connect an {{site.data.keyword.cloud}} application to your service, use the service credentials that are created when the service is provisioned.

## Using the 'Hello World' sample app

The [sample app](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs) demonstrates how to use Node.js and your service credentials to connect to a {{site.data.keyword.composeForMongoDB}} service. The application uses a web interface to create, read from, and write to a database.

Download the sample app and follow the instructions in the readme file. Then, in your application details page in {{site.data.keyword.cloud_notm}}, click **View APP** to view the contents of the *examples* table.

## Available credentials

Field Name|Description
----------|-----------
`uri`|The URI to be used when connecting to the service. The URI includes the schema (`mongodb:`), admin user name and password, host name of server, port number to connect to, database name, and `?ssl=true` to enable SSL connections.
`uri_cli`|A `mongo` shell command line that connects to the database instance.
`deployment_id`|An internal identifier for the service as created within Compose.
`db_type`|The type of database that is offered by the service: in this case, `mongodb`.
`name`|The database deployment name.
{: caption="Table 1. Compose for MongoDB credentials" caption-side="top"}
