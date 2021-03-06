---
copyright:
  years: 2016,2018
lastupdated: "2018-06-11"

keywords: mongodb, compose

subcollection: ComposeForMongoDB

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Architecture 
{: #architecture}

## Replication

An {{site.data.keyword.composeForMongoDB_full}} service comes in a three data node replica-set, which is configured as a single-shard. You select a region where the service is deployed, the three data nodes are spread over the region's availability zones, and then your data replicated across all three. MongoDB pushes all write operations on to the primary data node and then propagate them (via the oplog) to the secondary data node on the shard. If the primary node fails, the secondary node is promoted to primary. If any one data member becomes unreachable, your MongoDB shard continues to operate normally.

## Disk Encryption

All {{site.data.keyword.composeForMongoDB}} services all have encryption at rest. Your data resides on servers that have volume-level encryption enabled. 

Because {{site.data.keyword.composeForMongoDB}} is a managed service, it is possible for {{site.data.keyword.cloud}} Compose operators to see data. In addition to the disk encryption, we recommend that if you are storing personal information that you encrypt it before you store it in the database or by using extensions or features to enable encryption on the database itself. While these methods might impact usability or performance, it is good practice to ensure that personal information is protected with encryption.

## Auto-scaling

Resources are scaled automatically based on the total disk storage use of the MongoDB databases. Memory is based on a ratio of provisioned disk at 1:4. These resources are bundled as units.

Auto-scaling is designed to respond to the short-to-medium term trends of your database. Every hour, your service is checked and if it is running short on disk space, then more units are allocated to the deployment. 

Auto-scaling does not scale down deployments where disk/memory usage has shrunk. The resources provisioned to your databases will remain for your future needs, or until you scale down your deployment manually.
{: tip}

## The Admin database

If you store data in the Admin database, your deployment will not perform well and you risk data loss.

Like other MongoDB installations, {{site.data.keyword.composeForMongoDB}} has an Admin database. The Admin database holds information about users and roles within the database along with various tracking collections. On a simple MongoDB installation, this information is stored along with the other data in the database. 

On {{site.data.keyword.composeForMongoDB}}, a more resilient architecture uses MongoDB's sharded architecture. The administration data is shared between shards, which it achieves by creating another database cluster that contains the Admin database.

The cluster Admin database is not built for scale and degrades rapidly when any quantity of data is stored in it. Therefore, it is imperative that you ensure applications do not write data to the Admin database.
