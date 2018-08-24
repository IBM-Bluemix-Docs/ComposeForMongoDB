---
copyright:
  years: 2016,2018
lastupdated: "2018-06-08"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 連線配置
{: #connection-configuration}

MongoDB 共用叢集中的資料庫連線是由 mongos 路由器處理。{{site.data.keyword.composeForMongoDB}} 服務具有兩個此類路由器，各含有 64 MB 的記憶體。HAProxy 已新增至 Mongo 路由器，提供安全 TLS/SSL 連線的途徑。這兩個入口網站讓應用程式在其中一個入口網站無法聯繫時，仍能維持連線功能。幾乎所有 MongoDB 驅動程式都會處理多個連線點，但用戶端的失效接手是應用程式設計人員的責任。除非使用可完全處理失效接手錯誤的驅動程式，否則您應該執行下列動作：
* 使用可在其連線配置中接受多個端點的驅動程式。
* 確定您應用程式的資料庫讀取或寫入呼叫能適當地回應錯誤。這些選項包括：
  + 記載錯誤並重新連接。
  + 重新連接並重試已導致錯誤的作業。
  + 將失敗的作業放入佇列並排定重新連線。

## 傳輸中加密

所有 {{site.data.keyword.composeForMongoDB}} HAProxy 入口網站都已啟用 TLS/SSL，並支援 TLS 1.2 版。服務的憑證為自簽憑證。您可能需要為服務儲存憑證的本端副本，並提供其位置給應用程式的驅動程式，以進行安全連線。

## 連線限制

兩個 mongos 路由器具有每個 mongos 各 5000 個連線的連線限制。超出連線限制時，您的服務就會無回應。您可以透過驅動程式的連線儲存區特性，管理來自應用程式的連線。

## Proxy 逾時

mongos 路由器會管理伺服器與用戶端之間連線的生命週期。此處會詳細說明它們，作為驅動程式撰寫者的參考資料，以及其他對 {{site.data.keyword.composeForMongoDB}} 資料庫連線低階活動有興趣者的參考資料。

設定 |值|適用時機
----------|-----------|-----------
client | 5m |當用戶端預期要確認或傳送資料時。
connect | 4s |在 Proxy 和伺服器之間建立連線時。
server | 5m |當伺服器預期要確認或傳送資料時。
tunnel | 1d |當在用戶端與伺服器之間建立雙向連線，且連線在雙向都保持非作用中時。
client-fin |1h|當用戶端預期要確認或傳送資料，而某個方向已關閉時。
check | 5s |作為次要的逾時檢查，在 Proxy 和伺服器之間建立連線時。
{: caption="表 1. Mongos 路由器逾時" caption-side="top"}
