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

# {{site.data.keyword.composeForMongoDB}} 的用户注释
{: #user-notes-compose-for-mongodb}

这些注释涵盖 {{site.data.keyword.composeForMongoDB}} 用户所提出的各种主题。

## Admin 数据库

如果将数据存储在 Admin 数据库中，那么将无法正常执行，并且数据存在丢失风险。

与其他 MongoDB 安装一样，{{site.data.keyword.composeForMongoDB}} 具有一个 Admin 数据库。Admin 数据库包含有关数据库中用户和角色以及各种跟踪集合的信息。在简单 MongoDB 安装中，此数据库与其他数据一起存储在数据库中。 

在 {{site.data.keyword.composeForMongoDB}} 上，使用 MongoDB 分片体系结构可实现更具弹性的体系结构。这包括在分片之间共享管理数据。这是通过创建包含 Admin 数据库的另一数据库集群来实现的。

此集群 Admin 数据库并非针对扩展而构建的，而且会随其中存储的数据量的增加而快速降级。因此，必须确保应用程序不将数据写入 Admin 数据库。

## 连接字符串

客户机端故障转移由应用程序设计人员负责。除非使用可完全处理故障转移错误的驱动程序，否则您应该：

* 使用在其连接配置中接受多个端点的驱动程序。
* 确保应用程序的数据库读写调用针对错误做出正确反应。选项包括：
  + 记录错误并重新连接。
  + 重新连接并重试导致错误的操作。
  + 使失败操作排队并安排重新连接。






