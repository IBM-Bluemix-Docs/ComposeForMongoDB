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

# 連接 {{site.data.keyword.cloud_notm}} 應用程式

若要將 {{site.data.keyword.cloud}} 應用程式連接至您的服務，請使用佈建服務時所建立的服務認證。範例應用程式會示範如何使用 Node.js 利用所提供的認證連接至 {{site.data.keyword.composeForMongoDB_full}} 服務，以及如何建立資料庫、從資料庫讀取，和寫入資料庫。

## 使用 'Hello World' 範例應用程式來連接

[compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs) 範例應用程式會示範如何使用 Node.js 利用所提供的認證連接至 {{site.data.keyword.composeForMongoDB}} 服務。應用程式會建立、讀取及寫入資料庫。

請下載範例應用程式，並遵循 Readme 檔中的指示。然後，在 {{site.data.keyword.cloud_notm}} 的應用程式詳細資料頁面中，按一下**檢視應用程式**來檢視*範例* 表格的內容。

## 可用的認證

欄位名稱|說明
----------|-----------
`uri`|要在連接至服務時使用的 URI。`uri` 包括綱目 (`mongodb:`)、管理使用者名稱和密碼、伺服器的主機名稱、要連接至的埠號、資料庫名稱及 `?ssl=true`，以啟用 SSL 連線。
`uri_cli`|連接至資料庫實例的 `mongo` Shell 指令行。
`ca_certificate_base64`|用來確認應用程式將連接至適當伺服器的自簽憑證。憑證是 base64 編碼。您必須先將金鑰解碼，才能使用金鑰（如範例應用程式中所示）。
`deployment_id`|Compose 內所建立之服務的內部 ID。
`db_type`|服務所提供的資料庫類型；在此情況下，為 `mongodb`。
`name`|資料庫部署名稱。
{: caption="表 1. Compose for MongoDB 認證" caption-side="top"}
