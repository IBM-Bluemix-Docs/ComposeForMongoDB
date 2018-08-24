---

Copyright:
  years: 2017,2018
lastupdated: "2018-05-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# サービス概要

{{site.data.keyword.cloud}} Compose データベースに関する情報は_「概要」_ページに表示されます。 概要には、必要不可欠な識別情報と現在のリソース使用率が含まれます。 ツールで使用する接続ストリングのセクションもあります。接続ストリングを使用してツールからデータベースに接続することもできます。

## デプロイメントの詳細

_「デプロイメントの詳細 (Deployment Details)」_パネルには、サービスの詳細が表示されます。

![デプロイメントの詳細](./images/mongodb-deployment-details.png "「デプロイメントの詳細」パネルが表示されている画面")

### タイプ

サービスによって提供されるデータベースのタイプ、およびサービスが使用するデータベースのバージョン。 より新しいバージョンのデータベースが利用可能な場合は、通知が表示され、サービス・ダッシュボードの[「バージョンのアップグレード」](/docs/services/ComposeForMongoDB/dashboard-settings.html#upgrade-version)セクションへのリンクが示されます。

### ID

サービスの内部 ID。

### 使用法

データベース・サイズとご使用のサービス・プランで利用可能なストレージ容量。

## 現行ジョブ

サービスに関する管理上の変更 (スケーリングや手動バックアップなど) を行うと、ジョブが開始されます。 ジョブの実行中に、_「概要 (Overview)」_ページに_「現行ジョブ (Current Jobs)」_パネルが表示され、ジョブ名と進行状況表示バーが示されます。 ジョブが完了すると、_「現行ジョブ (Current Jobs)」_パネルは_「概要 (Overview)」_ページに表示されなくなります。

## 接続ストリング

_「接続ストリング」_パネルの一連のタブで、サービスで使用できる接続ストリングを確認できます。 接続ストリングを使用してサービスに接続する方法については、[外部アプリケーションの接続](./connecting-external.html)を参照してください。

### HTTPS

**HTTPS** 接続ストリングは、いくつかのクライアント・ライブラリーで使用でき、他のライブラリーが接続するために必要なすべての情報を含んでいます。

### 接続ストリング

接続ストリングをアプリケーションで直接使用して {{site.data.keyword.composeForMongoDB}} に接続することも、接続ストリングを環境変数として設定することもできます。 変数を設定する場合は、シェルで以下のコードを実行してください。

```
export MONGODB_URL="<CONNECTION STRING>"
```

### コマンド・ライン

**コマンド・ライン**は、形式の決まっているコマンドです。正しいパラメーターを指定して `mongo` を呼び出します。 この機能を使用するには、ローカル・システムに mongo ツールをインストールしておく必要があります。 その方法については、[外部アプリケーションの接続](./connecting-external.html)を参照してください。

### SSL 証明書

Compose {{site.data.keyword.cloud_notm}} サービスでは、データベース接続で使用できる SSL 証明書を提供しています。


## インスタンス管理 API

{{site.data.keyword.cloud_notm}} Compose API で {{site.data.keyword.composeForMongoDB}} サービスを管理できます。

### ファウンデーション・エンドポイント

ファウンデーション・エンドポイントは、サービスが存在する地域とサービス・インスタンス ID で構成されます。 これは、すべてのエンドポイントの先頭に配置されます。

### デプロイメント ID

デプロイメント ID はほとんどの呼び出しで必要であり、特定のデプロイメント・インスタンスを識別します。

### 参照

{{site.data.keyword.cloud_notm}} Compose サービス全体にわたる {{site.data.keyword.cloud_notm}} Compose API の使用についての詳しい資料やリファレンスは、[ {{site.data.keyword.cloud_notm}} Compose API](https://www.compose.com/articles/the-ibm-cloud-compose-api/) を参照してください。
