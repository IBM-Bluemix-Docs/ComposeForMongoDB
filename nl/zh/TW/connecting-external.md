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

# 連接外部應用程式
{: #connecting-external-app}

將外部應用程式連接至 {{site.data.keyword.composeForMongoDB_full}} 的方式有兩種：

- **連線字串**可由某些用戶端程式庫使用，且包含其他程式庫進行連接時所需的一切資訊。
- **指令行**是一個預先格式化的指令，它會使用正確的參數來呼叫 `mongo`。

您將在 {{site.data.keyword.composeForMongoDB}} 服務的*概觀* 頁面上找到這兩者。

如需深度應用程式及驅動程式支援，請針對您的特定語言及應用程式所使用的驅動程式，查閱適當的文件和社群。 

如果您要尋找未在這裡涵蓋的語言，請嘗試使用 [MongoDB.org 驅動程式清單](http://www.mongodb.org/display/DOCS/Drivers)。
{: tip}

## Go / Golang

如同其他範例一般，本文件採用稱為 `MONGODB_URL` 的環境變數搭配您的連線字串。若要設定此變數，請在 Shell 中執行下列指令：

```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```

mgo 驅動程式是 go 的標準 mongo 驅動程式，可藉由下列指令安裝：

```shell
go get gopkg.in/mgo.v2
```

mgo 文件（可在 [http://labix.org/mgo](http://labix.org/mgo) 取得）涵蓋大部分的一般連線，但在 Compose MongoDB 已啟用 SSL 時，有一些額外的步驟，用來建立 TLS 配置並將它傳遞至 mgo Dial 函數。

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
如果為 SSL/TLS 連線提供標準參數，則需要從 URI (19) 移除 "?ssl=true" 的這一行，因為 mgo 目前發生錯誤。若要連接並驗證伺服器的「SSL 公用憑證」本質上是否相同，但在此情況下，需要一些額外步驟，才能從 "servercert.crt" 檔案中載入憑證：


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

## Node.js / 原生

**快速附註：**在此範例中，我們假設您的 Compose 連線字串設定在環境變數 `MONGODB_URL` 中，如下所示：
```javascript
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name?ssl=true"
```
此程式碼使用 [node-mongodb-native](https://github.com/christkv/node-mongodb-native) 驅動程式，但在正式作業中，您可能想要更簡略些... *nesty*。與所有良好的 Node 套件一樣，您可以透過 [NPM](http://npmjs.org/) 來取得它。
```shell
npm install mongodb
```
### 使用 Node.js 連接 (Javascript)

在這裡連接，只需啟用 SSL/TLS 加密，而不需驗證伺服器：
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

如果我們想要使用伺服器的 SSL 公用憑證（在此範例中，儲存在 "servercert.crt" 檔案中），則我們需要讀取它並將其包含在選項中，同時將 sslValidate 翻轉為 true，如下所示：

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
如果您偏好在您的範例中加入更多 ES6 特性，請嘗試：
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
### 使用 Node.js 連接 (Coffeescript)
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
這應該會奏效！



## Mongose 及 Node

**快速附註：**在此範例中，我們假設您的 Compose 連線字串設定在環境變數 `MONGODB_URL` 中，如下所示：
```shell
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name"
```
此程式碼使用 [mongoose](http://mongoosejs.com/) 驅動程式。與所有良好的 Node 套件一樣，您可以透過 [NPM](http://npmjs.org/) 來取得它。
```shell
npm install mongoose
```
### 使用 Mongoose 連接 (Javascript)

基本技術與上述 Node.js/direct 範例相同。建立選項對映、在其中新增必要 SSL 參數，然後將具有 MongoDB 之 URL 的選項對映傳遞至 `mongoose.connect()` 方法。在下列範例中，我們使用憑證來連接，並再次列出可用的集合：
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

**快速附註：**在此範例中，我們假設您的 Compose 連線字串設定在環境變數 `MONGODB_URL` 中，如下所示：
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
如果您是使用 Ruby（以及如 Ruby on Rails、Sinatra 等架構），則可以藉由安裝 `mongo` (2.x) gem 來開始。它應該不用說，但您將需要 [RubyGems](http://rubygems.org)。如果您使用的是 1.9 版之前的 Ruby，則需要在範例的開頭新增 `require 'rubygems'`。



 若沒有使用憑證，程式碼只是：
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
「日誌程式」行會忽略驅動程式的除錯訊息（有許多除錯訊息）。如果想要看到更多，請將其註銷。



若要使用憑證進行連接，程式碼類似，但在連接時需要更多的選項設定：
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
請注意，會傳遞 `servercert.crt` 檔名兩次，以表示憑證也是其本身的權限。



## Python

**快速附註：**在此範例中，我們假設您的 Compose 連線字串設定在環境變數 `MONGODB_URL` 中，如下所示：
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/database_name"
```
如果您是使用 Python，則可以藉由[安裝 PyMongo](http://api.mongodb.org/python/current/installation.html) 來開始。
```python
import os
import pymongo
import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_cert_reqs=ssl.CERT_NONE)
db = client.get_default_database()
print db.collection_names()

```
若要使用伺服器的 SSL 憑證，只需將它新增為參數即可，如下所示：
```python
import os
import pymongo
import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_ca_certs="./servercert.crt")
db = client.get_default_database()
print db.collection_names()
```
然後，應該一切順利。



## PHP、MongoDB 及 Compose

**快速附註：**在此範例中，我們假設您的 Compose 連線字串設定在環境變數 `MONGODB_URL` 中，如下所示：
```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
這一開始是 [Gist](https://gist.github.com/coderoshi)（來自 AppFog 的 Larry Hitchon）。我們進行了一些改良，讓它以 Compose 為中心。它是相當[直接明確的驅動程式](http://php.net/manual/en/class.mongodb.php)。如果您沒有 PECL，則應該[安裝 PECL](http://pecl.php.net/)。
```shell
    sudo pecl install mongo
```
然後，至程式碼範例：
```php
<!-- PHP Mongo Docs: http://php.net/manual/en/class.mongodb.php -->
<!-- PHP Mongo Docs: http://php.net/manual/en/class.mongodb.php -->
<html>
<body>
<h1>Compose 測試</h1>
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

這應該一切順利。

## C# / .NET

本手冊假設您已針對您的專案下載並安裝 MongoDB C# 驅動程式。 

如果您尚未這麼做，則可以在下列網址找到安裝程式：[http://github.com/mongodb/mongo-csharp-driver/downloads](http://github.com/mongodb/mongo-csharp-driver/downloads)。

安裝完成之後，您就能夠參照 Visual Studio 內的 **MongoDriver** 及 **MongoBSON** DLL。

### 建立新專案

若要開始，請讓我們在 Visual Studio 中建立新專案。在此範例中，我選擇使用 **ASP.NET MVC 2 Web 應用程式**。*（必要的話，這些給定的範例可以輕鬆地移植到 WebForms。）*
![在 Visual Studio 中建立專案。](./images/create-project.jpg)
### 新增參照

現在，我們需要參照 **MongoDriver** 及 **MongoBSON** DLL，讓我們可以連接至 MongoDB 資料庫。C# 驅動程式安裝程式已將這兩個 DLL 新增至參照視窗的 .NET 標籤。

在新增這些參照後，現在只需幾行程式碼，就可以連接並查詢我們的 Compose 資料庫。
![從 .NET 清單中新增參照。](./images/csharp-add-reference.jpg)
### 新增連線資訊

接下來，讓我們在應用程式的 web.config 檔案中新增連線字串，以利用資料庫資訊設定連線字串。項目應該如下所示：
```csharp
<connectionStrings>
  <add name="ApplicationServices"
    connectionString="data source=.\SQLEXPRESS;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|aspnetdb.mdf;User Instance=true"
    providerName="System.Data.SqlClient" />
  <add name="Compose"
    connectionString="your mongo uri"/>
</connectionStrings>
```
當然，**your mongo uri** 應該取代為 Web 介面中提供給您的實際 Compose URI。您可以執行下列動作來達成此目的：



1. 登入 [Compose](https://www.compose.io)
2. 按一下您要連接之資料庫的埠號
3. 複製或記下所提供的 URI。

您只需要根 URI，就可以選擇任何您想要的資料庫。

### 設定您的模型和控制器

現在，讓我們建立模型來保留文件。此範例中使用的資料庫是具有基本資訊之開發人員的集合，所以我們將建立「資料轉換物件 (DTO)」來儲存這些內容，並在我們的模型中傳回這些 DTO 的一般清單。

在 **DeveloperDTO.cs** 檔案的 Model 目錄下建立 DTO 目錄。
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

將 **DeveloperModel.cs** 新增至 Models 目錄。*請確定您針對 DTO 目錄包含 using 子句。*
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
現在讓我們建立 DeveloperController。

 

在此範例中，讓我們將其命名為 DeveloperController。您可以取消勾選「新增用於建立、更新及刪除實務範例的動作方法」的方框，因為我們不會在「入門」手冊中涵蓋此功能。

開啟新建立的控制器，並新增 using 子句。您至少應該要有下列項目。

請注意強調顯示的行，我們會在其中宣告專用的 `MongoDatabase` 物件，並以建構子將其實例化。

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
如您所見，我已將資料庫呼叫細分為個別方法，列示於下。在這裡，我們利用 `MongoServer.Create` 方法，使用來自 web.config 的連線字串，以起始設定與 Compose 伺服器的連線。現在，我們可以呼叫 `GetDatabase` 來取得我們的 `MongoDatabase` 實例。

 

在此情況下，我的資料庫名稱為 **t2**。 

如需這些方法的相關資訊，請查看 [CSharp 驅動程式指導教學](http://www.mongodb.org/display/DOCS/CSharp+Driver+Tutorial#CSharpDriverTutorial-Createmethod)。
```csharp
static MongoDatabase retreive_mongodb_db()
{
  return MongoServer.Create(
    ConfigurationManager.ConnectionStrings["Compose"].ConnectionString)
    .GetDatabase("t2");
}
```
將 `Index` 方法新增至我們的 `DeveloperController`

現在我們的連線已完成設定，讓我們將方法新增至 **DeveloperController.cs**，以協助將資料庫內容遞送至我們不久將建立的視圖。
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



我們現在具有與資料庫的連線，以及將內容遞送至視圖的方法。我們現在只需混合我們的模型，並將它傳遞到視圖。

### 檢視資料

在此範例中，我們只需重述我們的開發人員，並將他們列印到畫面。在 Views 目錄中建立 `Developer` 資料夾，並新增一個稱為 `Index.aspx` 的 .aspx 檔案。請在下方新增程式碼，並完成一切設定。
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
如需相關資訊，請查閱 [MongoDB C# 驅動程式 API 文件](http://api.mongodb.org/csharp/current/)。
