---

copyright:
  years: 2016,2018
lastupdated: "2017-11-15"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 關於 {{site.data.keyword.composeForMongoDB}}
{: #about-compose-for-mongodb}

{{site.data.keyword.composeForMongoDB_full}} 使用 MongoDB 的強大編製索引及查詢、聚集及廣泛驅動程式支援，讓 MongoDB 成為許多新創事業和企業的首選 JSON 資料儲存庫。{{site.data.keyword.composeForMongoDB}} 提供簡單的自動調整部署系統。它提供高可用性及備援、自動化及隨需應變持續備份、監視工具、整合至警示系統、效能分析視圖等，全部都是透過全新的簡單使用者介面完成。
{:shortdesc}

**附註：**在 2016 年 9 月 14 日之前佈建且仍然作用中的任何 Compose 服務實例，仍然可以使用，而且可以在 [https://www.compose.com/](https://www.compose.com) 直接存取。在 {{site.data.keyword.cloud}} 帳戶內，可以直接存取及使用從此點往前佈建的任何 Compose 服務實例。

## 建立 {{site.data.keyword.composeForMongoDB}} 服務實例

您可以從 {{site.data.keyword.cloud_notm}} 型錄中的 [{{site.data.keyword.composeForMongoDB}} 頁面](https://console.{DomainName}/catalog/services/compose-for-mongodb/)建立 {{site.data.keyword.composeForMongoDB}} 服務。

選擇服務名稱、地區、組織，以及要在其中佈建服務的空間。您可以使用**選取資料庫版本**欄位，從可用的資料庫版本中進行選擇。

當您佈建 {{site.data.keyword.composeForMongoDB}} 實例時，可以選擇*標準* 或*企業* 方案。使用*企業* 方案，您可以將 {{site.data.keyword.composeForMongoDB}} 實例佈建到可用的 {{site.data.keyword.composeEnterprise}} 叢集。{{site.data.keyword.composeEnterprise}} 提供企業相符性所需的安全和隔離，並使用專用網路來確保已部署之資料庫的效能。如需詳細資料，請參閱 [Compose Enterprise 文件](../ComposeEnterprise/index.html)。

## 管理 {{site.data.keyword.composeForMongoDB}}

您可以從服務儀表板來管理服務。在這裡，您可以找到 {{site.data.keyword.cloud_notm}} Compose 資料庫的相關資訊，以及連接方式。您也可以：
- 管理備份
- 為您的服務配置更多資源
- 使用白名單來限制對資料庫的存取權。如需相關資訊，請參閱[設定](./dashboard-settings.html)。

## 連接至 {{site.data.keyword.composeForMongoDB}}

您可以使用與服務一起建立的認證，或使用服務儀表板的*概觀* 標籤中所提供的連線字串及指令行，來連接至服務。

## 將 {{site.data.keyword.cloud_notm}} 應用程式連接至 {{site.data.keyword.composeForMongoDB}}

若要將 {{site.data.keyword.cloud_notm}} 應用程式連接至服務，請使用與服務一起建立的認證。您可以在[連接 {{site.data.keyword.cloud_notm}} 應用程式](./connecting-bluemix-app.html)中，找到如何將 {{site.data.keyword.cloud_notm}} 應用程式連接至 {{site.data.keyword.composeForMongoDB}} 服務的資訊。

## 從 {{site.data.keyword.cloud_notm}}

 之外連接至 {{site.data.keyword.composeForMongoDB}}如果您想要從 {{site.data.keyword.cloud_notm}} 之外連接至 {{site.data.keyword.composeForMongoDB}}，則可以使用所提供的連線字串或指令行。您可以在[連接外部應用程式](./connecting-external.html)中，找到如何連接的相關資訊。

## 使用 MEAN 堆疊建立 Web 應用程式

您可以使用 {{site.data.keyword.composeForMongoDB}} 作為 MEAN 堆疊的一部分，來建立 Web 應用程式。請遵循[使用 MEAN 堆疊的現代 Web 應用程式](../../tutorials/mean-stack.html)指導教學中的步驟，以建立由 Mongo DB、Express Web 架構、Angular 前端架構，以及 Node.js 運行環境所組成的 Web 應用程式。
