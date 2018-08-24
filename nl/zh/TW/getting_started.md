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


# 入門指導教學
本指導教學使用 [compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs) 範例應用程式，以示範如何使用 Node.js，連接至 {{site.data.keyword.composeForMongoDB_full}} 服務。應用程式會使用透過應用程式 Web 介面所提供的資料，建立、讀取及寫入資料庫。
{: shortdesc}

## 開始之前

請確定您具有 [{{site.data.keyword.cloud_notm}} 帳戶][ibm_cloud_signup_url]{:new_window}。

您也將需要安裝 [Node.js](https://nodejs.org/) 及 [Git](https://git-scm.com/downloads)。

## 步驟 1：建立 {{site.data.keyword.composeForMongoDB}} 服務實例
{: #create-service}

您可以從 {{site.data.keyword.cloud_notm}} 型錄中的 [{{site.data.keyword.composeForMongoDB}} 頁面](https://console.{DomainName}/catalog/services/compose-for-mongodb/)建立 {{site.data.keyword.composeForMongoDB}} 服務。

選擇服務名稱，以及要在其中佈建服務的地區、組織和空間，並對**選取資料庫版本**欄位，選擇_最新的偏好版本_。

接著，選擇服務的定價方案。您可以選擇*標準* 或*企業* 方案。使用*企業* 方案，您可以將 {{site.data.keyword.composeForMongoDB}} 實例佈建到可用的 {{site.data.keyword.composeEnterprise}} 叢集。{{site.data.keyword.composeEnterprise}} 提供企業法規遵循所需的安全和隔離，並使用專用網路來確保已部署之資料庫的效能。如需詳細資料，請參閱 [{{site.data.keyword.composeEnterprise}}](/docs/services/ComposeEnterprise/index.html) 文件。

按一下**建立**來佈建您的服務。佈建可能需要一些時間才能完成。您可以移至服務的_管理_ 視圖，來檢查進度。

在佈建完成之前，您無法將應用程式連接至服務。
{: .tip}

## 步驟 2：從 GitHub 複製 Hello World 範例應用程式

使用下列指令，從您的終端機中將 Hello World 應用程式複製到您的本端環境：

```
git clone https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs.git
```
{: codeblock}

## 步驟 3：安裝應用程式相依關係

使用 npm 來安裝相依關係。

1. 從您的終端機中，將目錄切換至範例應用程式所在的位置。
  
  ```
  cd compose-mongodb-helloworld-nodejs
  ```
  {: codeblock}

2. 安裝 `package.json` 檔案中列出的相依關係。
  
  ```
  npm install
  ```

## 步驟 4：下載並安裝 {{site.data.keyword.cloud_notm}} CLI 工具

{{site.data.keyword.cloud_notm}} CLI 工具是您將從終端機或指令行中用來與 {{site.data.keyword.cloud_notm}} 通訊的工具。如需詳細資料，請參閱[下載並安裝 {{site.data.keyword.cloud_notm}} CLI](https://console.{DomainName}/docs/cli/reference/bluemix_cli/download_cli.html)。

## 步驟 5：連接至 {{site.data.keyword.cloud_notm}}

1. 在指令行工具中連接至 {{site.data.keyword.cloud_notm}}，並遵循提示以進行登入。

  ```
ibmcloud login
```

  如果您有聯合使用者 ID，請以 `ibmcloud login --sso` 指令，使用單一登入 ID 進行登入。若要進一步瞭解，請參閱[使用聯合 ID 進行登入](https://console.{DomainName}/docs/cli/login_federated_id.html#federated_id)。
  {: .tip}

2. 確定您將正確的 {{site.data.keyword.cloud_notm}} 組織及空間設為目標。

  ```
  ibmcloud target --cf
  ```

  使用您建立服務時所用的相同值，從提供的選項中進行選擇。

## 步驟 6：更新應用程式的資訊清單檔
{: #update-manifest}

{{site.data.keyword.cloud_notm}} 會使用資訊清單檔 (`manifest.yml`)，使應用程式與服務產生關聯。請遵循下列步驟來建立您的資訊清單檔。

1. 在編輯器中，開啟新檔案並新增下列幾行：

  ```
  ---
  applications:
  - name:    compose-mongodb-helloworld-nodejs
    host:    compose-mongodb-helloworld-nodejs
    memory:  128M
    services:
      - my-compose-for-mongodb-service
  ```

2. 將 `host` 值變更為唯一的值。您選擇的主機將決定應用程式 URL 的子網域：`<host>.mybluemix.net`。
3. 變更 `name` 值。您選擇的值將是應用程式出現在 {{site.data.keyword.cloud_notm}} 儀表板時的名稱。
4. 更新 `services` 值，以符合您在[建立 {{site.data.keyword.composeForMongoDB}} 服務實例](#create-service)中所建立的服務名稱。 

## 步驟 7：將應用程式推送至 {{site.data.keyword.cloud_notm}}

如果服務尚未完成步驟 1 的佈建，則此步驟會失敗。您可以移至服務的_管理_ 視圖來檢查其進度。
{: .tip}

當您推送應用程式時，它會自動連結至資訊清單檔中指定的服務。

```
ibmcloud cf push
```

## 步驟 8：檢查應用程式是否連接至您的 {{site.data.keyword.composeForMongoDB}} 服務

1. 導覽至 {{site.data.keyword.composeForMongoDB}} 服務儀表板。
2. 從儀表板功能表中選取_連線_。您的應用程式應該會列在_已連接的應用程式_ 之下。

如果您的應用程式未列出，請重複步驟 7 和 8，並確定您已在 [manifest.yml](#update-manifest) 中輸入正確的詳細資料。

## 步驟 9：使用應用程式

現在，當您造訪 `<host>.mybluemix.net/` 時，您將能夠檢視 {{site.data.keyword.composeForMongoDB}} 集合的內容。當您新增字組及其定義時，它們會新增至資料庫並顯示。如果您停止並重新啟動應用程式，您將會看到現在已列出您已新增的任何字組及定義。

## 在本端執行應用程式

您可以在本端執行應用程式，以測試 {{site.data.keyword.composeForMongoDB}} 服務實例的連線，而不要將應用程式推送至 {{site.data.keyword.cloud_notm}}。若要連接至服務，您將需要建立一組服務認證。

1. 從您的 {{site.data.keyword.cloud_notm}} 儀表板中，開啟您的 {{site.data.keyword.composeForMongoDB}} 服務實例。
2. 從主功能表中選取_服務認證_，以開啟「服務認證」視圖。
3. 按一下**新建認證**。
4. 為您的認證選擇一個名稱，然後按一下**新增**。
5. 現在會列出您的新認證。按一下表格對應列中的**檢視認證**來檢視認證，然後按一下**複製**圖示來複製您的認證。
6. 在您選擇的編輯器中，利用下列程式碼建立新的檔案，同時插入您的認證，如下所示：

  ```
  {
    "services": {
      "compose-for-mongodb": [
        {
          "credentials": INSERT YOUR CREDENTIALS HERE
        }
      ]
    }
  }
  ```
  {: codeblock}
7. 在範例應用程式所在的目錄中，將檔案儲存為 `vcap-local.json`。

若要在將應用程式推送至 Github 或 {{site.data.keyword.cloud_notm}} 時，避免意外洩露您的認證，您應該確定包含認證的檔案列在相關的忽略檔案中。如果您在應用程式目錄中開啟 `.cfignore` 及 `.gitignore`，將看到 `vcap-local.json` 列在這兩者之中，因此它不會包含在您將應用程式推送至 Github 或 {{site.data.keyword.cloud_notm}} 時所上傳的檔案。
{: .tip}

現在，啟動本端伺服器。

```
npm start
```

應用程式現在執行於 [http://localhost:8080](http://localhost:8080) 上。您可以將字組及定義新增至 {{site.data.keyword.composeForMongoDB}} 資料庫。當您停止並重新啟動應用程式時，若重新整理頁面，即會顯示已新增的任何字組。

如需為了供應用程式連接至服務而建立之認證的相關資訊，請參閱[可用認證](./connecting-bluemix-app.html#available-credentials)。

## 後續步驟

若要深入瞭解 [compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs) 範例應用程式如何運作，您可以閱讀應用程式的 Readme 檔，或 `server.js` 中的程式碼註解，這會提供一些關於應用程式函數的資訊。

若要開始探索您的 {{site.data.keyword.composeForMongoDB}} 服務，請參閱下列關於服務儀表板的主題：

- [儀表板概觀](./dashboard-overview.html)
- [備份](./dashboard-backups.html)
- [設定](./dashboard-settings.html)

[ibm_cloud_signup_url]: https://ibm.biz/compose-for-mongodb-signup
