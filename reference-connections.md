---
copyright:
  years: 2016,2018
lastupdated: "2018-06-08"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Connection Configuration
{: #connection-configuration}

Database connections in MongoDB sharded clusters are handled by mongos routers. {{site.data.keyword.composeForMongoDB}} services come with two of these routers with 64MB of memory a piece. HAProxy has been added into the Mongo routers, providing an avenue for secure TLS/SSL connections. The two portals to allow for applications to maintain connectivity should one of the portals become unreachable. Almost all MongoDB drivers handle mulitple connection points but failover at the client side is the responsibility of the application designer. Unless working with a driver which completely handles failover errors you should:
* Work with a driver that accepts multiple endpoints in its connection configuration.
* Ensure your applications calls to read or write to the database react to errors appropriately. Options include:
  + Logging the error and reconnecting.
  + Reconnecting and retrying the operation which caused the error.
  + Queuing the failed operation and scheduling reconnection.

## Encryption in Transit

All {{site.data.keyword.composeForMongoDB}} HAProxy portals have TLS/SSL enabled and support TLS version 1.2. The certificates for the service are self-signed certificates. You may have to save a local copy of the certifcate for your serivce and provide it's location to your applications driver in order to make a secure connection.

## Connection Limits

The two mongos routers have a connection limit of 5000 connections per mongos. Exceeding the connection limit will make your service unresponsive. You can manage connections from your application through your driver's connection pooling feature.

## Proxy Timeouts

The mongos routers manage the life cycle of connections between the server and client. We detail them here as a reference for driver writers and others who have an interest in the low-level activity of {{site.data.keyword.composeForMongoDB}} database connections.

Setting | Value | Applies
----------|-----------|-----------
client | 5m | When the client is expected to acknowledge or send data.
connect | 4s | When a connection is being made between the proxy and the server.
server | 5m | When the server is expected to acknowledge or send data.
tunnel | 1d | When a bidirectional connection is established between a client and a server, and the connection remains inactive in both directions.
client-fin | 1h | When the client is expected to acknowledge or send data while one direction is already shut down.
check | 5s | As a secondary timeout check when a connection is being made between the proxy and the server.
{: caption="Table 1. Mongos router timeouts" caption-side="top"}