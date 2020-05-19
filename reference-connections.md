---
copyright:
  years: 2016,2018
lastupdated: "2018-12-06"

keywords: mongodb, compose

subcollection: ComposeForMongoDB

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Connection Architecture
{: #connection-architecture}

Database connections in MongoDB sharded clusters are handled by mongos routers. {{site.data.keyword.composeForMongoDB}} services come with two of these routers with 64 MB of memory each. HAProxy provides an avenue for secure TLS/SSL connections. Two portals allow for applications to maintain connectivity if one of the portals becomes unreachable.

Almost all MongoDB drivers handle multiple connection points but failover at the client side is the responsibility of the application designer. Unless you are working with a driver that completely handles failover errors, you should:
* Work with a driver that accepts multiple endpoints in its connection configuration.
* Ensure your applications' calls to read or write to the database react to errors appropriately. Options include:
  + Logging the error and reconnecting.
  + Reconnecting and retrying the operation that caused the error.
  + Queuing the failed operation and scheduling reconnection.

## Encryption in Transit

All {{site.data.keyword.composeForMongoDB}} HAProxy portals are TLS/SSL-enabled, and support TLS version 1.2. All deployments are issued [Let's Encrypt certificates](/docs/ComposeForMongoDB?topic=ComposeForMongoDB-le-certificates), which are part of most environment's certificate stores. Some older deployments have self-signed certificates. For these deployments you may need a local copy of the certificate for your service and to provide its location to your applications driver to make a secure connection.

## Connection Limits

The two mongos routers have a limit of 5000 connections per mongos. Exceeding the connection limit will make your service unresponsive. You can manage connections from your application through your driver's connection pooling feature.

## Proxy Timeouts

The mongos routers manage the lifecycle of connections between the server and client. We detail them here as a reference for driver writers and others who have an interest in the low-level activity of {{site.data.keyword.composeForMongoDB}} database connections.

Setting | Value | Applies
----------|-----------|-----------
`client` | 5 m | When the client is expected to acknowledge or send data.
`connect` | 4 s | When a connection is being made between the proxy and the server.
`server` | 5 m | When the server is expected to acknowledge or send data.
`tunnel` | 1 d | When a bidirectional connection is established between a client and a server, and the connection remains inactive in both directions.
`client-fin` | 1 h | When the client is expected to acknowledge or send data while one direction is already shut down.
`check` | 5 s | As a secondary timeout check when a connection is being made between the proxy and the server.
{: caption="Table 1. Mongos router timeouts" caption-side="top"}