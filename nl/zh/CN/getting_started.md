---

copyright:
  years: 2016,2018
lastupdated: "2018-02-16"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}


# 入门教程
本教程使用 [compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs) 样本应用程序来演示如何使用 Node.js，通过提供的凭证连接到 {{site.data.keyword.composeForMongoDB_full}} 服务。应用程序可使用通过应用程序的 Web 界面提供的数据来对数据库执行创建、读取和写入操作。
{: shortdesc}

## 开始之前

确保您有 [{{site.data.keyword.cloud_notm}} 帐户][ibm_cloud_signup_url]{:new_window}。

您还需要安装 [Node.js](https://nodejs.org/) 和 [Git](https://git-scm.com/downloads)。

## 步骤 1：创建 {{site.data.keyword.composeForMongoDB}} 服务实例
{: #create-service}

您可以在 {{site.data.keyword.cloud_notm}}“目录”的 [{{site.data.keyword.composeForMongoDB}} 页面](https://console.{DomainName}/catalog/services/compose-for-mongodb/)中创建 {{site.data.keyword.composeForMongoDB}} 服务。

选择服务名称以及要在其中供应该服务的区域、组织和空间，然后对于**选择数据库版本**字段，选择_最新首选版本_。

接下来，选择服务的价格套餐。可以选择*标准*或*企业*套餐。使用*企业*套餐，您可以将 {{site.data.keyword.composeForMongoDB}} 实例供应到可用的 {{site.data.keyword.composeEnterprise}} 集群中。{{site.data.keyword.composeEnterprise}} 提供企业合规性所需的安全性和隔离，并使用专用网络来确保已部署的数据库的性能。有关更多详细信息，请参阅 [Compose Enterprise](../ComposeEnterprise/index.html) 文档。

## 步骤 2：从 Github 克隆 Hello World 样本应用程序

使用以下命令通过终端将 Hello World 应用程序克隆到本地环境：

```
git clone https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs.git
```

## 步骤 3：安装应用程序依赖项

使用 npm 来安装依赖项。

1. 在终端中，将目录切换到样本应用程序所在的位置。
  
  ```
  cd compose-mongodb-helloworld-nodejs
  ```

2. 安装 `package.json` 文件中列出的依赖项。
  
  ```
  npm install
  ```

## 步骤 4：创建服务凭证

在将应用程序推送到 {{site.data.keyword.cloud_notm}} 之前，可以在本地运行应用程序，以测试其与 {{site.data.keyword.composeForMongoDB}} 服务实例的连接。要连接到该服务，您需要创建一组服务凭证。

1. 在 {{site.data.keyword.cloud_notm}}“仪表板”中，打开 {{site.data.keyword.composeForMongoDB}} 服务实例。
2. 从主菜单中选择_服务凭证_以打开“服务凭证”视图。
3. 单击**新建凭证**。
4. 选择凭证的名称，然后单击**添加**。
5. 现在将列出您的新凭证。单击表中相应行中的**查看凭证**以查看凭证，然后单击**复制**图标以复制凭证。
6. 在您选择的编辑器中，创建包含以下内容的新文件，然后插入您的凭证，如下所示：

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
6. 在样本应用程序所在的目录中将该文件另存为 `vcap-local.json`。

为了避免在将应用程序推送到 Github 或 {{site.data.keyword.cloud_notm}} 时意外公开凭证，应该确保包含凭证的文件列在相关的忽略文件中。如果打开应用程序目录中的 `.cfignore` 和 `.gitignore`，您将看到这两项中均列出 `vcap-local.json`，因此在将应用程序推送到 Github 或 {{site.data.keyword.cloud_notm}} 时上传的文件中不会包含此文件。
{: .tip}

## 步骤 5：本地运行应用程序

```
npm start
```

现在，应用程序会在 [http://localhost:8080](http://localhost:8080) 上运行。您可以向 {{site.data.keyword.composeForMongoDB}} 数据库添加字词和定义。停止并重新启动应用程序时，刷新页面后会显示已添加的所有字词。

下一阶段是将应用程序连接到服务实例，然后将应用程序部署到 {{site.data.keyword.cloud_notm}}。

## 步骤 6：下载并安装 {{site.data.keyword.cloud_notm}} CLI 工具

{{site.data.keyword.cloud_notm}} CLI 工具是将用于通过终端或命令行与 {{site.data.keyword.cloud_notm}} 进行通信的工具。有关详细信息，请参阅[下载并安装 {{site.data.keyword.cloud_notm}} CLI](https://console.{DomainName}/docs/cli/reference/bluemix_cli/download_cli.html)。

## 步骤 7：连接到 {{site.data.keyword.cloud_notm}}

1. 在命令行工具中连接到 {{site.data.keyword.cloud_notm}} 并按照提示进行登录。

  ```
  bx login
  ```

  如果您有联合用户标识，请使用 `bx login --sso` 命令以使用您的单点登录标识登录。要了解更多信息，请参阅[使用联合标识登录](https://console.{DomainName}/docs/cli/login_federated_id.html#federated_id)。
  {: .tip}

2. 确保设定的目标是正确的 {{site.data.keyword.cloud_notm}} 组织和空间。

  ```
  bx target --cf
  ```

  从提供的选项中进行选择，并使用在创建服务时所用的相同值。

## 步骤 8：更新应用程序的清单文件
{: #update-manifest}

{{site.data.keyword.cloud_notm}} 使用清单文件 `manifest.yml` 将应用程序与服务相关联。执行以下步骤创建清单文件。

1. 在编辑器中，打开一个新文件并添加以下内容：

  ```
  ---
  applications:
  - name:    compose-mongodb-helloworld-nodejs
    host:    compose-mongodb-helloworld-nodejs
    memory:  128M
    services:
      - my-compose-for-mongodb-service
  ```

2. 将 `host` 值更改为唯一的值。您选择的主机将确定应用程序 URL 的子域：`<host>.mybluemix.net`。
3. 更改 `name` 值。您选择的值将是显示在 {{site.data.keyword.cloud_notm}} 仪表板中的应用程序的名称。
4. 更新 `services` 值，以匹配在[创建 {{site.data.keyword.composeForMongoDB}} 服务实例](#create-service)中所创建服务的名称。 

## 步骤 9：将应用程序推送到 {{site.data.keyword.cloud_notm}}。

推送应用程序时，它将自动绑定到清单文件中指定的服务。

```
bx cf push
```

## 步骤 10：检查应用程序是否连接到 {{site.data.keyword.composeForMongoDB}} 服务

1. 浏览至 {{site.data.keyword.composeForMongoDB}} 服务仪表板。
2. 从仪表板菜单中选择_连接_。您的应用程序应该列在_已连接应用程序_下。

如果未列出您的应用程序，请重复步骤 7 和 8，确保在 [manifest.yml](#update-manifest) 中输入了正确的详细信息。

## 步骤 11：使用应用程序

现在，访问 `<host>.mybluemix.net/` 时，您将能够查看 {{site.data.keyword.composeForMongoDB}} 集合的内容。添加字词及其定义时，这些内容将添加到数据库并显示。如果停止并重新启动应用程序，您将看到已添加的所有字词和定义现在均已列出。


## 后续步骤

要了解有关 [compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs) 样本应用程序工作方式的更多信息，可以阅读应用程序自述文件或阅读 `server.js` 中的代码注释，其中提供了有关应用程序功能的一些信息。

要开始探索 {{site.data.keyword.composeForMongoDB}} 服务，请查看有关服务仪表板的以下主题：

- [仪表板概述](./dashboard-overview.html)
- [备份](./dashboard-backups.html)
- [设置](./dashboard-settings.html)

有关为应用程序连接到服务而创建的凭证的信息，请参阅[可用凭证](./connecting-bluemix-app.html#available-credentials)。

[ibm_cloud_signup_url]: https://ibm.biz/compose-for-mongodb-signup
