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

# 开始使用 Compose for MongoDB
{: #getting-started-with-compose-for-mongodb}

{{site.data.keyword.composeForMongoDB_full}} 使用强大的建立索引和查询、聚集功能，以及 MongoDB 的广泛驱动程序支持，使其成为许多初创公司和企业的首选 JSON 数据存储库。{{site.data.keyword.composeForMongoDB}} 提供一种简单的自动扩展部署系统。它可在一个简洁明了的用户界面中，提供高可用性和冗余、自动和随需应变不停止备份、监视工具、与警报系统的集成、性能分析视图等一切功能。
{:shortdesc}

**注：**在 2016 年 9 月 14 日之前供应的任何仍处于活动状态的 Compose 服务实例仍可通过 [https://www.compose.com/](https://www.compose.com) 使用和直接访问。从此处供应的任何 Compose 服务实例都可在 {{site.data.keyword.cloud}} 帐户中直接访问和使用。

## 创建 Compose for MongoDB 服务实例

[创建 {{site.data.keyword.composeForMongoDB}} 实例](https://console.ng.bluemix.net/catalog/services/compose-for-mongodb/)。

当您创建服务实例时，请确保选择服务的名称和凭证名称。保持服务处于未绑定状态；您可以稍后使用供应服务时提供的凭证，将应用程序连接到您的服务。*可用凭证*一节列出各种凭证值。

供应 {{site.data.keyword.composeForMongoDB}} 实例时，可以选择*标准*或*企业*套餐。使用*企业*套餐，您可以将 {{site.data.keyword.composeForMongoDB}} 实例供应到可用的 {{site.data.keyword.composeEnterprise}} 集群中。{{site.data.keyword.composeEnterprise}} 提供企业合规性所需的安全性和隔离，并使用专用网络来确保已部署的数据库的性能。有关更多详细信息，请参阅 [Compose Enterprise 文档](../ComposeEnterprise/index.html)。

## 管理 Compose for MongoDB

您可以从服务仪表板管理服务。您可以在此找到有关 {{site.data.keyword.cloud_notm}} Compose 数据库以及如何连接到该数据库的信息。您还可以：
- 管理备份
- 为服务分配更多资源
- 使用白名单来限制对数据库的访问。
有关更多信息，请参阅[设置](./dashboard-settings.html)。

## 连接到 Compose for MongoDB

您可以使用随服务一起创建的凭证，或者使用服务仪表板*概述*选项卡中提供的连接字符串和命令行，来连接到服务。

## 将 {{site.data.keyword.cloud_notm}} 应用程序连接到 Compose for MongoDB

要将 {{site.data.keyword.cloud_notm}} 应用程序连接到服务，请使用随服务一起创建的凭证。您可以在[连接 {{site.data.keyword.cloud_notm}} 应用程序](./connecting-bluemix-app.html)中查找有关如何将 {{site.data.keyword.cloud_notm}} 应用程序连接到 {{site.data.keyword.composeForMongoDB}} 服务的信息。

## 从外部 {{site.data.keyword.cloud_notm}} 连接到 Compose for MongoDB

如果要从外部 {{site.data.keyword.cloud_notm}} 连接到 {{site.data.keyword.composeForMongoDB}}，可以使用提供的连接字符串或命令行。您可以在[连接外部应用程序](./connecting-external.html)中找到有关如何连接的信息。
