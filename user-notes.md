---

copyright:
  years: 2017,2018
lastupdated: "2017-06-17"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# User Notes for {{site.data.keyword.composeForMongoDB}}
{: #user-notes-compose-for-mongodb}

These notes cover various topics that have been raised by {{site.data.keyword.composeForMongoDB}} users.

## The Admin Database

If you store data in the Admin database, it will not perform well and you risk data loss.

Like other MongoDB installations, {{site.data.keyword.composeForMongoDB}} has an Admin database. The Admin database holds information about users and roles within the database along with various tracking collections. On a simple MongoDB installation, this is stored along with the other data in the database. 

On {{site.data.keyword.composeForMongoDB}}, a more resilient architecture using MongoDB's sharded architecture. This involves sharing administration data between shards. That is achieved by creating another database cluster which contains the Admin database.

This cluster Admin database is not built for scale and will rapidly degrade with any quantity of data being stored in it. It is therefore imperative that you ensure applications do not write data to the Admin database.

## Connection Strings

Failover at the client side is the responsibility of the application designer. Unless working with a driver which completely handles failover errors you should:

* Work with a driver that accepts multiple endpoints in its connection configuration.
* Ensure your applications calls to read or write to the database react to errors appropriately. Options include:
  + Logging the error and reconnecting.
  + Reconnecting and retrying the operation which caused the error.
  + Queuing the failed operation and scheduling reconnection.





