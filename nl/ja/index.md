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

# Compose for MongoDB の概説
{: #getting-started-with-compose-for-mongodb}

{{site.data.keyword.composeForMongoDB_full}} では、JSON データ・ストアとして多くの新規事業や企業に支持されている MongoDB の、強力な索引作成と照会、集約、幅広いドライバーのサポートを利用します。{{site.data.keyword.composeForMongoDB}} は、使いやすい自動スケーリング・デプロイメント・システムを提供します。高可用性と冗長性、自動およびオンデマンドのノンストップ・バックアップ、モニタリング・ツール、アラート・システムへの組み込み、パフォーマンス分析ビュー、その他の機能をすべて、整理されたシンプルなユーザー・インターフェースで提供します。
{:shortdesc}

**注:** 2016 年 9 月 14 日より前にプロビジョンされた、現在もアクティブな Compose サービス・インスタンスは引き続き使用可能で、[https://www.compose.com/](https://www.compose.com) から直接アクセスできます。その時点以降にプロビジョンされた Compose サービス・インスタンスは、{{site.data.keyword.cloud}} アカウント内で直接アクセスして使用されます。

## Compose for MongoDB サービス・インスタンスの作成

[{{site.data.keyword.composeForMongoDB}} インスタンスを作成します](https://console.ng.bluemix.net/catalog/services/compose-for-mongodb/)。

サービスのインスタンスを作成するときは、必ずサービスの名前と資格情報名の両方を選択してください。サービスをバインドしないでおきます。サービスをプロビジョンするときに指定される資格情報を使用して、アプリケーションをサービスに接続できます。*使用可能な資格情報*セクションには、各種の資格情報値がリストされています。

{{site.data.keyword.composeForMongoDB}} インスタンスのプロビジョンでは、*標準*プランと*エンタープライズ*・プランのどちらかを選択できます。*エンタープライズ*・プランの場合は、{{site.data.keyword.composeForMongoDB}} インスタンスを有効な {{site.data.keyword.composeEnterprise}} クラスターにプロビジョンできます。{{site.data.keyword.composeEnterprise}} は、企業コンプライアンスで要求されるセキュリティーと分離を提供し、専用ネットワーキングを使用してデプロイ済みデータベースのパフォーマンスを確保します。詳しくは、[Compose Enterprise 文書](../ComposeEnterprise/index.html)を参照してください。

## Compose for MongoDB の管理

サービス・ダッシュボードからサービスを管理できます。ダッシュボードには {{site.data.keyword.cloud_notm}} Compose データベースとそれへの接続方法に関する情報が示されます。また、以下の操作を行うこともできます。
- バックアップを管理する
- サービスに割り振るリソースを増やす
- ホワイトリストを使用してデータベースへのアクセスを制限する
詳しくは、[設定](./dashboard-settings.html)を参照してください。

## Compose for MongoDB への接続

サービスに接続するには、サービスと一緒に作成された資格情報を使用するか、サービス・ダッシュボードの*「概要」*タブに表示される接続ストリングとコマンド・ラインを使用します。

## {{site.data.keyword.cloud_notm}} アプリケーションを Compose for MongoDB に接続する場合

{{site.data.keyword.cloud_notm}} アプリケーションをサービスに接続するには、サービスと一緒に作成された資格情報を使用します。{{site.data.keyword.cloud_notm}} アプリケーションを {{site.data.keyword.composeForMongoDB}} サービスに接続する方法については、[{{site.data.keyword.cloud_notm}} アプリケーションの接続](./connecting-bluemix-app.html)を参照してください。

## {{site.data.keyword.cloud_notm}} の外部から Compose for MongoDB に接続する場合

{{site.data.keyword.cloud_notm}} の外部から {{site.data.keyword.composeForMongoDB}} に接続する場合は、用意されている接続ストリングやコマンド・ラインを使用できます。接続方法については、[外部アプリケーションの接続](./connecting-external.html)を参照してください。
