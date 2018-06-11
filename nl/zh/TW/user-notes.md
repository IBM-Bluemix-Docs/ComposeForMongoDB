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

# {{site.data.keyword.composeForMongoDB}} 的使用者注意事項
{: #user-notes-compose-for-mongodb}

這些注意事項涵蓋 {{site.data.keyword.composeForMongoDB}} 使用者已引發的各種主題。

## Admin 資料庫

如果您將資料儲存至 Admin 資料庫，則執行效果不佳，而且會有資料流失的風險。

與其他 MongoDB 安裝類似，{{site.data.keyword.composeForMongoDB}} 具有 Admin 資料庫。Admin 資料庫會保留資料庫內使用者及角色的相關資訊，以及各種追蹤集合的相關資訊。在簡單 MongoDB 安裝上，這會與其他資料一起儲存在資料庫中。 

在 {{site.data.keyword.composeForMongoDB}} 上，是使用 MongoDB 共用架構的更具復原力架構。這包含在 Shard 之間共用管理資料。建立包含 Admin 資料庫的另一個資料庫叢集，即可達成此目的。

此叢集 Admin 資料庫不是為了進行調整而建置，並且會因其中所儲存的任何資料數量而快速降低效能。因此，您必須確定應用程式不會將資料寫入 Admin 資料庫。

## 連線字串

用戶端的失效接手是應用程式設計人員的責任。除非使用可完全處理失效接手錯誤的驅動程式，否則您應該執行下列動作：

* 使用可在其連線配置中接受多個端點的驅動程式。
* 確定您的應用程式讀取或寫入資料庫呼叫適當地回應錯誤。這些選項包括：
  + 記載錯誤並重新連接。
  + 重新連接並重試已導致錯誤的作業。
  + 將失敗的作業放入佇列並排定重新連線。






