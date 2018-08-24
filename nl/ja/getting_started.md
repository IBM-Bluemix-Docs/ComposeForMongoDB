---

copyright:
  years: 2016,2018
lastupdated: "2018-05-29"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}


# 概説チュートリアル
このチュートリアルでは、[compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs) サンプル・アプリケーションを使用して、Node.js で {{site.data.keyword.composeForMongoDB_full}} サービスに接続する方法を示します。 アプリケーションは、アプリケーションの Web インターフェースを介して提供されるデータを使用して、データベースを作成し、データベースに対して読み取りおよび書き込みを行います。
{: shortdesc}

## 始めに

[{{site.data.keyword.cloud_notm}} アカウント][ibm_cloud_signup_url]{:new_window}があることを確認します。

[Node.js](https://nodejs.org/) および [Git](https://git-scm.com/downloads) もインストールする必要があります。

## ステップ 1: {{site.data.keyword.composeForMongoDB}} サービス・インスタンスを作成する
{: #create-service}

{{site.data.keyword.composeForMongoDB}} サービスは、{{site.data.keyword.cloud_notm}} カタログの [{{site.data.keyword.composeForMongoDB}} ページ](https://console.{DomainName}/catalog/services/compose-for-mongodb/)から作成できます。

サービス名、およびサービスをプロビジョンする地域、組織、スペースを選択します。**「データベースのバージョンの選択 (Select a database version)」**フィールドでは_「最新の推奨バージョン (Latest Preferred Version)」_を選択します。

次に、サービスの料金プランを選択します。 *標準*プランと*エンタープライズ*・プランのどちらかを選択できます。 *エンタープライズ*・プランの場合は、{{site.data.keyword.composeForMongoDB}} インスタンスを有効な {{site.data.keyword.composeEnterprise}} クラスターにプロビジョンできます。 {{site.data.keyword.composeEnterprise}} は、企業コンプライアンスで要求されるセキュリティーと分離を提供し、専用ネットワーキングを使用してデプロイ済みデータベースのパフォーマンスを確保します。 詳しくは、[{{site.data.keyword.composeEnterprise}}](/docs/services/ComposeEnterprise/index.html) 文書を参照してください。

**「作成」**をクリックして、サービスをプロビジョンします。 プロビジョニングは、完了するまでしばらくかかる場合があります。 進行状況を確認するには、サービスの_「管理」_ビューに移動します。

プロビジョニングが完了するまで、アプリケーションをサービスに接続することはできません。
{: .tip}

## ステップ 2: Github から Hello World サンプル・アプリケーションを複製する

以下のコマンドを使用して、端末から Hello World アプリケーションをローカル環境に複製します。

```
git clone https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs.git
```
{: codeblock}

## ステップ 3: アプリケーションの従属項目をインストールする

npm を使用して従属項目をインストールします。

1. 端末から、サンプル・アプリケーションがあるディレクトリーに移動します。
  
  ```
  cd compose-mongodb-helloworld-nodejs
  ```
  {: codeblock}

2. `package.json` ファイルにリストされている従属項目をインストールします。
  
  ```
  npm install
  ```

## ステップ 4: {{site.data.keyword.cloud_notm}} CLI ツールをダウンロードしてインストールする

{{site.data.keyword.cloud_notm}} CLI ツールは、端末またはコマンド・ラインから {{site.data.keyword.cloud_notm}} と通信するために使用するツールです。 詳しくは、[{{site.data.keyword.cloud_notm}} CLI のダウンロードとインストール](https://console.{DomainName}/docs/cli/reference/bluemix_cli/download_cli.html)を参照してください。

## ステップ 5: {{site.data.keyword.cloud_notm}} に接続する

1. コマンド・ライン・ツールで {{site.data.keyword.cloud_notm}} に接続し、プロンプトに従ってログインします。

  ```
  ibmcloud login
  ```

  統合ユーザー ID がある場合は、`ibmcloud login --sso` コマンドを使用して、シングル・サインオン ID でログインします。 詳細については、[統合 ID によるログイン](https://console.{DomainName}/docs/cli/login_federated_id.html#federated_id)を参照してください。
  {: .tip}

2. 正しい {{site.data.keyword.cloud_notm}} 組織とスペースをターゲットとしていることを確認してください。

  ```
  ibmcloud target --cf
  ```

  サービス作成時に使用した値と同じ値を使用して、提供されたオプションから選択します。

## ステップ 6: アプリケーションのマニフェスト・ファイルを更新する
{: #update-manifest}

{{site.data.keyword.cloud_notm}} はマニフェスト・ファイル - `manifest.yml` を使用して、アプリケーションをサービスに関連付けます。 マニフェスト・ファイルを作成するには、以下の手順に従います。

1. エディターで新規ファイルを開き、以下を追加します。

  ```
  ---
  applications:
  - name:    compose-mongodb-helloworld-nodejs
    host:    compose-mongodb-helloworld-nodejs
    memory:  128M
    services:
      - my-compose-for-mongodb-service
  ```

2. `host` 値を固有のものに変更します。 アプリケーションの URL のサブドメインは、`<host>.mybluemix.net` のように、選択したホストによって決まります。
3. `name` 値を変更します。 選択した値は、{{site.data.keyword.cloud_notm}} ダッシュボードでのアプリケーションの表示名になります。
4. `services` 値を更新し、[{{site.data.keyword.composeForMongoDB}} サービス・インスタンスの作成](#create-service)で作成したサービスの名前と一致させます。 

## ステップ 7: アプリケーションを {{site.data.keyword.cloud_notm}} にプッシュする。

ステップ 1 のサービスのプロビジョニングが完了していないと、このステップは失敗します。進行状況を確認するには、サービスの_「管理」_ビューに移動します。
{: .tip}

アプリケーションをプッシュすると、マニフェスト・ファイルで指定されたサービスに自動的にバインドされます。

```
ibmcloud cf push
```

## ステップ 8: アプリケーションが {{site.data.keyword.composeForMongoDB}} サービスに接続していることを確認する

1. {{site.data.keyword.composeForMongoDB}} サービス・ダッシュボードにナビゲートします。
2. ダッシュボード・メニューから_「接続」_を選択します。 アプリケーションが_「接続済みアプリケーション」_に表示されます。

アプリケーションが表示されない場合は、ステップ 7 と 8 を再度実行し、[manifest.yml](#update-manifest) で正しい詳細情報を入力したことを確認します。

## ステップ 9: アプリケーションを使用する

`<host>.mybluemix.net/` にアクセスすると、{{site.data.keyword.composeForMongoDB}} コレクションの内容を表示できます。 単語と定義を追加すると、データベースに追加され、表示されます。 アプリケーションを停止して再始動すると、追加した単語と定義が表示されます。

## ローカルでのアプリケーションの実行

アプリケーションを {{site.data.keyword.cloud_notm}} にプッシュする代わりに、ローカル環境でアプリケーションを実行して {{site.data.keyword.composeForMongoDB}} サービス・インスタンスへの接続をテストできます。 サービスに接続するには、一連のサービス資格情報を作成する必要があります。

1. {{site.data.keyword.cloud_notm}} ダッシュボードから、{{site.data.keyword.composeForMongoDB}} サービス・インスタンスを開きます。
2. メインメニューから_「サービス資格情報 (Service Credentials)」_を選択して、「サービス資格情報 (Service Credentials)」ビューを開きます。
3. **「新規資格情報 (New Credential)」**をクリックします。
4. 資格情報の名前を選択し、**「追加 (Add)」**をクリックします。
5. これで、新しい資格情報がリストされました。 表の該当する行で**「資格情報の表示 (View credentials)」**をクリックして資格情報を表示し、**「コピー (Copy)」**アイコンをクリックして資格情報をコピーします。
6. 任意のエディターで、以下を含む新規ファイルを作成し、示されているように資格情報を挿入します。

  ```
  {
    "services": {
      "compose-for-mongodb": [
        {
          "credentials": ここに資格情報を挿入する
        }
      ]
    }
  }
  ```
  {: codeblock}
7. サンプル・アプリケーションがあるディレクトリーに `vcap-local.json` としてファイルを保存します。

アプリケーションを Github または {{site.data.keyword.cloud_notm}} にプッシュする際に、資格情報が誤って公開されないようにするには、資格情報を含むファイルが関連する無視ファイルにリストされていることを確認してください。 アプリケーション・ディレクトリーで `.cfignore` および `.gitignore` を開くと、`vcap-local.json` が両方にリストされていることがわかります。そのため、アプリケーションを Github または {{site.data.keyword.cloud_notm}} のいずれかにプッシュした場合にアップロードされるファイルには含まれません。
{: .tip}

この時点で、ローカル・サーバーを始動します。

```
npm start
```

これで、アプリケーションが [http://localhost:8080](http://localhost:8080) で実行されます。 {{site.data.keyword.composeForMongoDB}} データベースに単語と定義を追加できます。 アプリケーションを停止して再始動すると、追加した単語が、ページの最新表示時に表示されます。

アプリケーションからサービスに接続するために作成した資格情報については、[使用可能な資格情報](./connecting-bluemix-app.html#available-credentials)を参照してください。

## 次のステップ

[compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs) サンプル・アプリケーションの動作を詳しく知りたい場合は、アプリケーションの README ファイルや `server.js` のコード・コメントにアプリケーションの機能についての情報があるので、それらを参照してください。

{{site.data.keyword.composeForMongoDB}} サービスの検討作業を開始するには、サービス・ダッシュボードに関する以下のトピックを参照してください。

- [ダッシュボードの概要](./dashboard-overview.html)
- [バックアップ](./dashboard-backups.html)
- [設定](./dashboard-settings.html)

[ibm_cloud_signup_url]: https://ibm.biz/compose-for-mongodb-signup
