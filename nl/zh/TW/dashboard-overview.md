---

Copyright:
  Years: 2017
lastupdated: "2017-10-23"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 服務概觀

_概觀_ 頁面顯示 {{site.data.keyword.cloud}} Compose 資料庫的相關資訊。概觀包括基本識別資訊及現行資源使用。您也會找到一個區段，以尋找可與工具搭配使用的連線字串，或利用工具來連接至您的資料庫。

## 部署詳細資料

_部署詳細資料_ 畫面顯示服務的詳細資料。

![部署詳細資料](./images/mongodb-deployment-details.png "「部署詳細資料」畫面的視圖")

### 類型

服務所提供的資料庫類型，以及服務使用的資料庫版本。

### 名稱

服務的內部 ID。

### 用量

服務方案所提供的資料庫大小及儲存空間數量。


## 連線字串

您可以在_連線字串_ 畫面的一組標籤中，找到服務可用的連線字串。您可以在[連接外部應用程式](./connecting-external.html)中，瞭解如何使用連線字串來連接至您的服務。

### HTTPS

**HTTPS** 連線字串可由某些用戶端程式庫使用，且包含其他程式庫進行連接時所需的一切資訊。

### 連線字串

您可以直接在應用程式中使用連線字串來連接至 {{site.data.keyword.composeForMongoDB}}，或將它設為環境變數。若要這樣做，請在 Shell 中執行下列指令：

```
export MONGODB_URL="<CONNECTION STRING>"
```

### 指令行

**指令行**是一個預先格式化的指令，它會使用正確的參數來呼叫 `mongo`。若要使用它，您需要在本端系統上安裝 mongo 工具。您可以在[連接外部應用程式](./connecting-external.html)中，進一步瞭解如何執行此作業。

### SSL 憑證

您的 Compose {{site.data.keyword.cloud_notm}} 服務提供您可用來連接至資料庫的 SSL 憑證。
