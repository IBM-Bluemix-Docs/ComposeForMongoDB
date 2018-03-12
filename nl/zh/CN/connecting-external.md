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

# 连接外部应用程序
{: #connecting-external-app}

有两种方法可将外部应用程序连接到 {{site.data.keyword.composeForMongoDB_full}}：

- **连接字符串**可由某些客户机库使用，并包含其他库连接所需的所有信息。
- **命令行**是一个预先格式化的命令，它将使用正确的参数来调用 `mongo`。

您将在 {{site.data.keyword.composeForMongoDB}} 服务的*概述*页面上找到这两项。

要获取深度应用程序和驱动程序支持，请查看您特定语言的相应文档和社区，以及应用程序正在使用的驱动程序。 

如果要查找此处未显示的语言，请尝试 [MongoDB.org 驱动程序列表](http://www.mongodb.org/display/DOCS/Drivers)。
{: tip}

## Go / Golang

如在其他示例中那样，本文档假设具有连接字符串的环境变量称为 `MONGODB_URL`。要设置此变量，请在 shell 中执行以下命令：

```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```

mgo 驱动程序是适用于 go 的标准 mongo 驱动程序，可以通过以下方式进行安装：

```shell
go get gopkg.in/mgo.v2
```

mgo 文档在 [http://labix.org/mgo](http://labix.org/mgo) 处提供，其涵盖大部分典型的连接，但是由于 Compose MongoDB 已启用 SSL，因此要创建 TLS 配置并将其传递给 mgo Dial 函数，还需要一些额外的步骤。

```go
package main

import (
	"crypto/tls"
	"fmt"
	"net"
	"os"
	"strings"

	"gopkg.in/mgo.v2"
)

func main() {
	uri := os.Getenv("MONGODB_URL")
	if uri == "" {
		fmt.Println("No connection string provided - set MONGODB_URL")
		os.Exit(1)
	}
	uri = strings.TrimSuffix(uri, "?ssl=true")

	tlsConfig := &tls.Config{}
	tlsConfig.InsecureSkipVerify = true

	dialInfo, err := mgo.ParseURL(uri)

	if err != nil {
		fmt.Println("Failed to parse URI: ", err)
		os.Exit(1)
	}

	dialInfo.DialServer = func(addr *mgo.ServerAddr) (net.Conn, error) {
		conn, err := tls.Dial("tcp", addr.String(), tlsConfig)
		return conn, err
	}

	session, err := mgo.DialWithInfo(dialInfo)
	if err != nil {
		fmt.Println("Failed to connect: ", err)
		os.Exit(1)
	}

	defer session.Close()

	dbnames, err := session.DB("").CollectionNames()
	if err != nil {
		fmt.Println("Couldn't query for collections names: ", err)
		os.Exit(1)
	}

	fmt.Println(dbnames)

}

```
需要从 URI (19) 除去“?ssl=true”的行，因为在为 SSL/TLS 连接提供标准参数时，mgo 目前会发生错误。要连接并验证服务器的 SSL 公用证书本质上是相同的，但需要执行一些额外的步骤，以从文件“servercert.crt”（此案例）装入证书：



```go
package main

import (
	"crypto/tls"
	"crypto/x509"
	"io/ioutil"

	"fmt"
	"net"
	"os"
	"strings"

	"gopkg.in/mgo.v2"
)

func main() {
	uri := os.Getenv("MONGODB_URL")
	if uri == "" {
		fmt.Println("No connection string provided - set MONGODB_URL")
		os.Exit(1)
	}
	uri = strings.TrimSuffix(uri, "?ssl=true")

	roots := x509.NewCertPool()

	if ca, err := ioutil.ReadFile("servercert.crt"); err == nil {
		roots.AppendCertsFromPEM(ca)
	}

	tlsConfig := &tls.Config{}
	tlsConfig.RootCAs = roots

	dialInfo, err := mgo.ParseURL(uri)

	if err != nil {
		fmt.Println("Failed to parse URI: ", err)
		os.Exit(1)
	}

	dialInfo.DialServer = func(addr *mgo.ServerAddr) (net.Conn, error) {
		conn, err := tls.Dial("tcp", addr.String(), tlsConfig)
		return conn, err
	}

	session, err := mgo.DialWithInfo(dialInfo)
	if err != nil {
		fmt.Println("Failed to connect: ", err)
		os.Exit(1)
	}

	defer session.Close()

	dbnames, err := session.DB("").CollectionNames()
	if err != nil {
		fmt.Println("Couldn't query for collections names: ", err)
		os.Exit(1)
	}

	fmt.Println(dbnames)

}

```

## Node.js / Native

**快速注释：**在此示例中，假定您的 Compose 连接字符串在环境变量 `MONGODB_URL` 中设置，如下所示：
```javascript
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name?ssl=true"
```
此代码使用 [node-mongodb-native](https://github.com/christkv/node-mongodb-native) 驱动程序，但在生产环境中，您可能需要一些 less... *nesty*。与所有正常的 Node 软件包一样，您可以通过 [NPM](http://npmjs.org/) 来获取它。
```shell
npm install mongodb
```
### 使用 Node.js (Javascript) 进行连接

以下连接只是启用了 SSL/TLS 加密而未验证服务器：
```javascript
var MongoClient = require('mongodb').MongoClient;
var assert = require('assert');

var options = {
    mongos: {
        ssl: true,
        sslValidate: false,
    }
}

MongoClient.connect(process.env.MONGODB_URL, options, function(err, db) {
    assert.equal(null, err);
    db.listCollections({}).toArray(function(err, collections) {
        assert.equal(null, err);
        collections.forEach(function(collection) {
            console.log(collection);
        });
        db.close();
        process.exit(0);
    })
});
```

如果要使用服务器的 SSL 公用证书（在本示例中存储在文件“servercert.crt”中），那么我们需要读取该证书并将其包含在选项中，而将 sslValidate 转换为 true，如下所示：

```javascript
var MongoClient = require('mongodb').MongoClient;
var assert = require('assert');
var fs = require('fs');

var ca = [fs.readFileSync(__dirname + "/servercert.crt")];

var options = {
    mongos: {
        ssl: true,
        sslValidate: true,
        sslCA:ca,
    }
}

MongoClient.connect(process.env.MONGODB_URL, options, function(err, db) {
    assert.equal(null, err);
    db.listCollections({}).toArray(function(err, collections) {
        assert.equal(null, err);
        collections.forEach(function(collection) {
            console.log(collection);
        });
        db.close();
        process.exit(0);
    })
});
```
如果您喜欢使用更具 ES6 风格的示例，请尝试：
```javascript
const MongoClient = require('mongodb').MongoClient;
import assert from 'assert';
import fs from 'fs';

const ca = [fs.readFileSync(`${__dirname}/servercert.crt`)];

const options = {
    mongos: {
        ssl: true,
        sslValidate: false,
        sslCA:ca,
    }
};

MongoClient.connect(process.env.MONGODB_URL, options, (err, db) => {
    assert.equal(null, err);
    db.listCollections({}).toArray((err, collections) => {
        assert.equal(null, err);
        collections.forEach((collection) => {
            console.log(collection);
        });
        db.close();
        process.exit(0);
    })
});
```
### 使用 Node.js (Coffeescript) 进行连接
```coffeescript

mongodb = require 'mongodb'
url = require 'url'
log = console.log

connection_uri = url.parse(process.env.COMPOSE_URL)
db_name = connection_uri.pathname.replace(/^\//, '')

mongodb.Db.connect process.env.COMPOSE_URL, (error, client)->
  throw error if error

  client.collectionNames (error, names)->
    throw error if error

    # output all collection names
    log "Collections"
    log "==========="
    last_collection = null
    for col_data in names
      col_name = col_data.name.replace("#{db_name}.", '')
      log col_name
      last_collection = col_name

    collection = new mongodb.Collection(client, last_collection)
    log "\nDocuments in #{last_collection}"
    documents = collection.find({}, limit : 5)

    # output a count of all documents found
    documents.count (error, count)->
      log "  #{count} documents(s) found"
      log "===================="

      # output the first 5 documents
      documents.toArray (error, docs)->
        throw error if error

        for doc in docs then log doc

        # close the connection
        client.close()
```
这样应该就可以了！



## Mongoose 和 Node

**快速注释：**在此示例中，假定您的 Compose 连接字符串在环境变量 `MONGODB_URL` 中设置，如下所示：
```shell
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name"
```
此代码使用 [mongoose](http://mongoosejs.com/) 驱动程序。与所有正常的 Node 软件包一样，您可以通过 [NPM](http://npmjs.org/) 来获取它。
```shell
npm install mongoose
```
### 使用 Mongoose (Javascript) 进行连接

基本方法与上面的 Node.js/direct 示例相同。创建选项映射、加入到必要的 SSL 参数，然后使用适用于 MongoDB 的 URL 将该选项映射传递到 `mongoose.connect()` 方法。在以下示例中，我们使用证书进行连接，然后再次列出可用集合：
```javascript
var mongoose = require('mongoose');
var assert = require('assert');
var fs = require('fs');

var ca = [ fs.readFileSync(__dirname + "/servercert.crt") ];

var options = {
    mongos: {
      ssl: true,
      sslValidate: true,
      sslCA: ca
    }
}

// If the connection throws an error
mongoose.connection.on('error',function (err) {
  console.log('Mongoose default connection error: ' + err);
});

mongoose.connection.on('open', function (err) {
    assert.equal(null, err);
    mongoose.connection.db.listCollections().toArray(function(err, collections) {
        assert.equal(null, err);
        collections.forEach(function(collection) {
            console.log(collection);
        });
        mongoose.connection.db.close();
        process.exit(0);
    })
});

// Let's open that connection
mongoose.connect(process.env.MONGODB_URL, options);
```

## Ruby

**快速注释：**在此示例中，假定您的 Compose 连接字符串在环境变量 `MONGODB_URL` 中设置，如下所示：
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
如果您使用的 Ruby（与诸如 Ruby on Rails、Sinatra 等框架配合使用），那么可以通过安装 `mongo` (2.x) gem 来启动。它应该是理所当然的，但您需要 [RubyGems](http://rubygems.org)。如果您使用的是 1.9 之前的 Ruby，那么需要在示例的开头添加 `require 'rubygem'`。



 在不使用证书的情况下，代码很简单：
```ruby
require 'mongo'

Mongo::Logger.logger.level = ::Logger::FATAL

def client
  @client ||= Mongo::Client.new(ENV['MONGODB_URL'],
                    ssl:true,
                    ssl_verify: false)
end

db = client.database

collections = db.collection_names
puts collections # ["coll1", "coll2", ...]
```
Logger 行将禁用驱动程序的调试消息（有很多）。如果您想要看到更多消息，请将其注释掉。



要使用证书进行连接，代码类似，但连接时需要更多选项设置：
```ruby
require 'mongo'

def client
    @client ||= Mongo::Client.new(ENV['MONGODB_URL'],
                ssl: true,
                ssl_verify: true,
                ssl_cert: './servercert.crt',
                ssl_cacert: './servercert.crt')
end

db = client.database

collections = db.collection_names
puts collections # ["coll1", "coll2", ...]
```
请注意，`servercert.crt` 文件名被传递两次以表示该证书也是它自己的认证中心。



## Python

**快速注释：**在此示例中，假定您的 Compose 连接字符串在环境变量 `MONGODB_URL` 中设置，如下所示：
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/database_name"
```
如果您使用的是 Python，那么可以通过[安装 PyMongo](http://api.mongodb.org/python/current/installation.html) 来启动。
```python
import os
import pymongo
import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_cert_reqs=ssl.CERT_NONE)
db = client.get_default_database()
print db.collection_names()

```
要使用服务器的 SSL 证书，只需作为参数添加该证书即可，如下所示：
```python
import os
import pymongo
import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_ca_certs="./servercert.crt")
db = client.get_default_database()
print db.collection_names()
```
然后，您应该就可顺利进行了。



## PHP、MongoDB 和 Compose

**快速注释：**在此示例中，假定您的 Compose 连接字符串在环境变量 `MONGODB_URL` 中设置，如下所示：
```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
最初，这是来自 AppFog 的 Larry Hitchon 的 [Gist](https://gist.github.com/coderoshi)。我们做了一些改进，将其以 Compose 为中心。这是一个非常[简单的驱动程序](http://php.net/manual/en/class.mongodb.php)。如果您没有 PECL，那么应该[安装 PECL](http://pecl.php.net/)。
```shell
    sudo pecl install mongo
```
然后，对于代码样本：
```php
<!-- PHP Mongo Docs: http://php.net/manual/en/class.mongodb.php -->
<!-- PHP Mongo Docs: http://php.net/manual/en/class.mongodb.php -->
<html>
<body>
<h1>Compose 测试</h1>
<?php
  try {
    // connect to Compose assuming your MONGODB_URL environment
    // variable contains the connection string
    $connection_url = getenv("MONGODB_URL");

     // create the mongo connection object
    $m = new MongoClient($connection_url, array("ssl" => true));

    // extract the DB name from the connection path
    $url = parse_url($connection_url);
    $db_name = preg_replace('/\/(.*)/', '$1', $url['path']);

    // use the database we connected to
    $db = $m->selectDB($db_name);

    echo "<h2>Collections</h2>";
    echo "<ul>";

    // print out list of collections
    $cursor = $db->listCollections();
    $collection_name = "";
    foreach( $cursor as $doc ) {
      echo "<li>" .  $doc->getName() . "</li>";
      $collection_name = $doc->getName();
    }
    echo "</ul>";

    // print out last collection
    if ( $collection_name != "" ) {
      $collection = $db->selectCollection($collection_name);
      echo "<h2>Documents in ${collection_name}</h2>";

      // only print out the first 5 docs
      $cursor = $collection->find();
      $cursor->limit(5);
      echo $cursor->count() . ' document(s) found. <br/>';
      foreach( $cursor as $doc ) {
        echo "<pre>";
        var_dump($doc);
        echo "</pre>";
      }
    }

    // disconnect from server
    $m->close();
  } catch ( MongoConnectionException $e ) {
    die('Error connecting to MongoDB server');
  } catch ( MongoException $e ) {
    die('Mongo Error: ' . $e->getMessage());
  } catch ( Exception $e ) {
    die('Error: ' . $e->getMessage());
  }
?>
</body>
</html>
```

然后，您应该就可顺利进行了。

## C# / .NET

本指南假设您已下载并安装适用于您项目的 MongoDB C# 驱动程序。 

如果尚未执行此操作，那么可以在以下网址找到安装程序：[http://github.com/mongodb/mongo-csharp-driver/downloads](http://github.com/mongodb/mongo-csharp-driver/downloads)。

安装完成后，您将能够在 Visual Studio 中引用 **MongoDriver** 和 **MongoBSON** DLL。

### 创建新项目

要开始进行，让我们先在 Visual Studio 中创建新项目。在此示例中，我选择使用 **ASP.NET MVC 2 Web 应用程序**。*（如有必要，可以将提供的示例轻松移植到 WebForms。*
![在 Visual Studio 中创建项目。](./images/create-project.jpg)
### 添加引用

现在，我们需要引用 **MongoDriver** 和 **MongoBSON** DLL，以便可以连接到 MongoDB 数据库。C# 驱动程序安装程序已将这两个 DLL 添加到引用窗口的 .NET 选项卡。

在已添加这些引用的情况下，现在我们只要使用几行代码，就可以连接和查询 Compose 数据库。
![从 .NET 列表中添加引用。](./images/csharp-add-reference.jpg)
### 添加连接信息

接下来，让我们通过在应用程序的 web.config 文件中添加新的连接字符串，使用我们的数据库信息来设置连接字符串。该条目应该类似于以下内容：
```csharp
<connectionStrings>
  <add name="ApplicationServices"
    connectionString="data source=.\SQLEXPRESS;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|aspnetdb.mdf;User Instance=true"
    providerName="System.Data.SqlClient" />
  <add name="Compose"
    connectionString="your mongo uri"/>
</connectionStrings>
```
当然，**您的 mongo uri** 应该替换为在 Web 界面中提供给您的实际 Compose URI。您可以通过执行以下操作来实现此目标：



1. 登录到 [Compose](https://www.compose.io)
2. 单击要连接到的数据库的端口号
3. 复制或记下提供的 URI。

您将只需要根 URI，因此可以选择您想要的任何数据库。

### 设置模型和控制器

现在，让我们创建一个模型来保存我们的文档。本示例中使用的数据库是具有基本信息的开发者的集合，因此我们将创建“数据转换对象”(DTO) 来存储这些属性并在模型中返回这些 DTE 的通用列表。

在 Model 目录下为 **DeveloperDTO.cs** 文件创建 DTO 目录。
```csharp
namespace mongodb_csharp.Models.DTO
{
  public class DeveloperDTO
  {
    public BsonObjectId id { get; set; }
    public string name { get; set; }
    public string languages { get; set; }
    public string company { get; set; }
  }
}
```

将 **DeveloperModel.cs** 添加到 Model 目录。*确保为 DTO 目录包含 using 子句。*
```csharp
using System.Collections.Generic;
using mongodb_csharp.Models.DTO;

namespace mongodb_csharp.Models
{
  public class DeveloperModel
  {
    public IList<DeveloperDTO> developers { get; set; }
  }
}
```
现在，让我们创建 DeveloperController。

 

在此示例中，让我们将其命名为 DeveloperController。您可以取消选中“为创建、更新和删除方案添加操作方法”框，因为我们不会在“入门指南”中涵盖此功能。

打开新创建的控制器并添加 using 子句。至少应该具有以下内容。

请注意突出显示的行，其中我们声明了一个私有 `MongoDatabase` 对象，并在构造函数中对其进行实例化。

```csharp
using System.Linq;
using System.Web.Mvc;
using System.Configuration;
using MongoDB.Driver;
using mongodb_csharp.Models;
using cimpose_csharp.Models.DTO;

namespace mongodb_csharp.Controllers
{
    public class DeveloperController : Controller
    {
        readonly MongoDatabase mongo_db;

        public DeveloperController()
        {
            mongo_db = retreive_mongodb_db();
        }

        public ActionResult Index()
        {
            var model = new DeveloperModel();
            var developers_collection = mongo_db.GetCollection("developers").FindAll().AsEnumerable();

            model.developers = (from developer in developers_collection
                                select new DeveloperDTO
                                           {
                                               id = developer["_id"].AsObjectId,
                                               name = developer["name"].AsString,
                                               languages = developer["languages"].AsBsonArray.ToString(),
                                               company = developer["company"].AsString
                                           }).ToList();

            return View(model);
        }

        static MongoDatabase retreive_mongodb_db()
        {
            return MongoServer.Create(
                ConfigurationManager.ConnectionStrings["Compose"].ConnectionString)
                .GetDatabase("t2");
        }
    }
}
```
如您所见，我将数据库调用分成了单独的方法，如下所列。在这里，我们利用 `MongoServer.Create` 方法，通过 web.config 中的连接字符串来初始化与 Compose 服务器的连接。现在，我们可以调用 `GetDatabase` 以获取 `MongoDatabase` 实例。

 

在此情况下，我的数据库名称为 **t2**。 

有关这些方法的更多信息，请参阅 [CSharp 驱动程序教程](http://www.mongodb.org/display/DOCS/CSharp+Driver+Tutorial#CSharpDriverTutorial-Createmethod)。
```csharp
static MongoDatabase retreive_mongodb_db()
{
  return MongoServer.Create(
    ConfigurationManager.ConnectionStrings["Compose"].ConnectionString)
    .GetDatabase("t2");
}
```
向 `DeveloperController` 添加 `Index` 方法

现在，我们设置了连接，让我们向 **DeveloperController.cs** 添加一种方法，以帮助将数据库内容交付到即将创建的视图。
```csharp
public ActionResult Index()
{
  var model = new DeveloperModel();
  var developers_collection = mongo_db.GetCollection("developers").FindAll().AsEnumerable();

  model.developers = (from developer in developers_collection
                        select new DeveloperDTO
                        {
                          id = developer["_id"].AsObjectId,
                          name = developer["name"].AsString,
                          languages = developer["languages"].AsBsonArray.ToString(),
                          company = developer["company"].AsString
                        }).ToList();

  return View(model);
}
```
成功！



现在，我们已具有数据库的连接和将内容交付到视图的方法。我们现在需要做的就是混合我们的模型并将其传递到视图中。

### 查看数据

在此示例中，我们仅对开发人员进行迭代并将其打印到屏幕。在 Views 目录中创建 `Developer` 文件夹，并添加名为 `Index.aspx` 的新 .aspx 文件。添加下面的代码，您已准备就绪。
```asp
<%%@ Page Title="" Language="C#" MasterPageFile="~/Views/Shared/Site.Master" Inherits="System.Web.Mvc.ViewPage<mongodb_csharp.Models.DeveloperModel>" %>

<asp:Content ID="Content1" ContentPlaceHolderID="TitleContent" runat="server">
  Index
</asp:Content>

<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">

  <h2>Developers</h2>

  <ul>
    <%% foreach(var developer in Model.developers) { %>
      <li><%%= developer.name %> works for <%%= developer.company %> and is proficient in <%%= developer.languages %></li>
    <%% } %>
  </ul>
</asp:Content>
```
有关更多信息，请参阅 [MongoDB C# Driver API 文档](http://api.mongodb.org/csharp/current/)。
