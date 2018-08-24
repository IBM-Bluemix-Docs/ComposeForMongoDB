---
copyright:
  years: 2016,2018
lastupdated: "2018-06-11"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 架構 
{: #architecture}

## 抄寫

{{site.data.keyword.composeForMongoDB_full}} 服務具有三資料節點的一個抄本集，其配置為單一 Shard。您選取部署服務的地區，三個資料節點會分散在地區的可用性區域，然後您的資料會在全部三個節點之間抄寫。MongoDB 將所有寫入作業推送至主要資料節點，然後將它們（透過作業日誌）傳播至 Shard 上的次要資料節點。如果主要節點失敗，次要節點會提升為主要節點。如果任何一個資料成員變成無法聯繫，您的 MongoDB Shard 會繼續正常運作。

## 磁碟加密

所有 {{site.data.keyword.composeForMongoDB}} 服務都有靜態加密。您的資料位於已啟用磁區層次加密的伺服器上。 

由於 {{site.data.keyword.composeForMongoDB}} 是一項受管理的服務，因此 {{site.data.keyword.cloud}} Compose 操作員能夠看到資料。除了磁碟加密外，我們建議您，如果要儲存個人資訊，請先將資訊加密，再將它儲存在資料庫中，或使用延伸或特性對資料庫本身啟用加密。這些方法固然可能影響可用性或效能，確定個人資訊受到加密保護仍是個很好的作法。

## 自動調整

資源會根據 MongoDB 資料庫的磁碟儲存空間總用量而自動調整。記憶體是以 1:4 的佈建磁碟比例為基礎。這些資源組合成為單位。

自動調整的設計是要回應資料庫的中短期趨勢。每小時都會檢查您的服務，如果它的磁碟空間不足，便會將更多的單位配置給該部署。 

自動調整對於磁碟/記憶體用量收縮的部署，不會將其縮減。針對您的資料庫佈建的資源將會保留以供您未來需要，或是直到您手動縮減部署為止。
{: .tip}

## Admin 資料庫

如果您將資料儲存至 Admin 資料庫，則執行效果不佳，而且會有資料流失的風險。

與其他 MongoDB 安裝類似，{{site.data.keyword.composeForMongoDB}} 具有 Admin 資料庫。Admin 資料庫會保留資料庫內使用者及角色的相關資訊，以及各種追蹤集合的相關資訊。在簡單 MongoDB 安裝上，這會與其他資料一起儲存在資料庫中。 

在 {{site.data.keyword.composeForMongoDB}} 上，是使用 MongoDB 共用架構的更具復原力架構。這包含在 Shard 之間共用管理資料。建立包含 Admin 資料庫的另一個資料庫叢集，即可達成此目的。

此叢集 Admin 資料庫不是為了進行調整而建置，並且會因其中所儲存的任何資料數量而快速降低效能。因此，您必須確定應用程式不會將資料寫入 Admin 資料庫。
