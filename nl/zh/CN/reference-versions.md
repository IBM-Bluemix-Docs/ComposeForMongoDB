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

# 版本

可部署版本|首选版本
----------|-----------
3.2.10、3.2.11、3.2.18、3.4.10|3.2.18
{: caption="表 1. MongoDB 版本" caption-side="top"}

## 首选版本

首选版本通常是可用于 {{site.data.keyword.composeForMongoDB}} 的 MongoDB 数据库的最新版本。这是目录页面中下拉版本选择器中的缺省版本，也是未在调用中指定版本时，由 API 自动供应的版本。

### 新的首选版本协议

有新版本可用时，会公告将发布新版本，并且该版本可供部署。在发布后大约 7 天时间内，最新版本可用，但并不作为首选版本。在此时段内，您可部署和测试新版本，同时当前版本仍然可用。在 7 天时段结束时，新版本会设置为首选版本，或者公告此更改的新日期。

可用于供应的版本列表位于 {{site.data.keyword.composeForMongoDB}} [目录页面](https://console.{DomainName}/catalog/services/compose-for-mongodb)上。

要获取 {{site.data.keyword.composeForMongoDB}} 服务可用版本的当前列表，可以使用 [GET /2016-07/deployments/:id/versions](https://apidocs.compose.com/v1.0/reference#2016-07-get-deployments-versions) 端点。
