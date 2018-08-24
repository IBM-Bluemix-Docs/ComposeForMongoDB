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

可部署的版本|偏好的版本
----------|-----------
3.2.10、3.2.11、3.2.18、3.4.10 | 3.2.18
{: caption="表 1. MongoDB 版本" caption-side="top"}

## 偏好的版本

偏好的版本通常是可供 {{site.data.keyword.composeForMongoDB}} 使用的最新版 MongoDB 資料庫。它是型錄頁面上，下拉版本選取器中的預設版本。也是 API 自動佈建的版本（如果呼叫中未指定任何版本的話）。

### 新的偏好版本通訊協定

有新版本可用時，會宣布其版本，並且可供部署。在發行日期之後，有大約 7 天的時間範圍可使用最新的版本，但最新版此時並非偏好的版本。這個時間範圍可讓您部署並測試新版本，同時仍有現行版本可供使用。在 7 天時間範圍結束時，便會將新版本設為偏好的版本，或宣布此變更的新日期。

您可以在 {{site.data.keyword.composeForMongoDB}} [型錄頁面](https://console.{DomainName}/catalog/services/compose-for-mongodb)中找到可用來佈建的版本清單。

若要取得 {{site.data.keyword.composeForMongoDB}} 服務的現行可用版本清單，您可以使用 [GET /2016-07/deployments/:id/versions](https://apidocs.compose.com/v1.0/reference#2016-07-get-deployments-versions) 端點。
