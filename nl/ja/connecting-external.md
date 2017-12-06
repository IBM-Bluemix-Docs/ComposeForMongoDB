---

copyright:
  years: 2017
lastupdated: "2017-06-17"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 外部アプリケーションの接続
{: #connecting-external-app}

{{site.data.keyword.composeForMongoDB_full}} に外部アプリケーションを接続するには、2 つの方法があります。

- 一部のクライアント・ライブラリーでは**接続ストリング**を使用できます。接続ストリングに、他のライブラリーからの接続に必要な情報をすべて組み込みます。
- **コマンド・ライン**は、形式の決まっているコマンドです。正しいパラメーターを指定して `mongo` を呼び出します。

両方の方法が {{site.data.keyword.composeForMongoDB}} サービスの*「概要」*ページにあります。

アプリケーションとドライバーのサポートについては、使用する言語やアプリケーションのドライバーの資料やコミュニティーで詳しい説明を確認してください。 

ここで取り上げていない言語について調べたい場合は、[MongoDB.org のドライバー・リスト](http://www.mongodb.org/display/DOCS/Drivers)をご覧ください。
{: tip}

## Go / Golang

この資料では、他のすべての例と同じく、`MONGODB_URL` という環境変数で接続ストリングを設定します。この変数を設定するために、シェルで以下のコードを実行してください。

```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```

mgo ドライバーは、go に対応した mongo の標準ドライバーです。以下のコードでインストールできます。

```shell
go get gopkg.in/mgo.v2
```

mgo の資料は [http://labix.org/mgo](http://labix.org/mgo) にあります。その資料では、ほとんどの標準的な接続が取り上げられていますが、Compose MongoDB では SSL が有効になっているので、TLS 構成を作成して mgo の Dial 機能に渡すために追加の手順が必要になります。

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
URI から "?ssl=true" を削除する行 (19) が必要なのは、SSL/TLS 接続の標準パラメーターを使用すると、現時点では mgo でエラーが発生するからです。サーバーの SSL 公開証明書の接続と検証の方法は基本的に同じですが、証明書を (この場合は servercert.crt ファイルから) ロードするために追加の手順が必要になります。

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

## Node.js / ネイティブ

**クイック・メモ:** この例では、Compose の接続ストリングを環境変数 `MONGODB_URL` で以下のように設定したという前提で説明を進めていきます。
```javascript
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name?ssl=true"
```
このコードでは [node-mongodb-native](https://github.com/christkv/node-mongodb-native) ドライバーを使用していますが、実動環境ではもう少し*使いやすい*ドライバーを利用してもかまいません。Node パッケージは、[NPM](http://npmjs.org/) から入手できます。
```shell
npm install mongodb
```
### Node.js (Javascript) による接続

ここでは、サーバーの検証なしで SSL/TLS 暗号化を有効にしただけの状態で接続します。
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

サーバーの SSL 公開証明書 (この例では、servercert.crt ファイルに保管されている証明書) を使用する場合は、その証明書を読み取ってオプションに組み込み、sslValidate を true に設定する必要があります。

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
ES6 のような方法をサンプルに組み込みたい場合は、以下のようにしてみてください。
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
### Node.js (Coffeescript) による接続
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
スムーズな動作になるはずです。

## Mongoose と Node

**クイック・メモ:** この例では、Compose の接続ストリングを環境変数 `MONGODB_URL` で以下のように設定したという前提で説明を進めていきます。
```shell
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name"
```
このコードでは、[mongoose](http://mongoosejs.com/) ドライバーを使用します。Node パッケージは、[NPM](http://npmjs.org/) から入手できます。
```shell
npm install mongoose
```
### Mongoose (Javascript) による接続

基本的な技法は、上記の Node.js/direct のサンプルと同じです。オプション・マップを作成し、SSL 必須のパラメーターを追加し、MongoDB の URL を指定してそのオプション・マップを `mongoose.connect()` メソッドに渡します。以下の例では証明書を使用して接続し、今回も使用可能なコレクションのリストを出力します。
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

**クイック・メモ:** この例では、Compose の接続ストリングを環境変数 `MONGODB_URL` で以下のように設定したという前提で説明を進めていきます。
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
Ruby on Rails や Sinatra などのフレームワークで Ruby を使用している場合は、最初に `mongo` (2.x) gem をインストールします。言うまでもないことですが、[RubyGems](http://rubygems.org) が必要になります。1.9 より前の Ruby を使用している場合は、サンプルの先頭に `require 'rubygems'` を追加してください。

 証明書を使用しない場合のコードはシンプルです。
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
Logger の行によって、ドライバーのデバッグ・メッセージを非表示にしています (実際にはかなりのメッセージがあります)。そのようなメッセージを表示したい場合は、その行をコメント化してください。

証明書を使用して接続する場合のコードも基本的に同じですが、接続時にさらに多くのオプション設定が必要になります。
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
`servercert.crt` というファイル名を 2 回渡しているのは、証明書自体が認証局でもあることを示すためです。

## Python

**クイック・メモ:** この例では、Compose の接続ストリングを環境変数 `MONGODB_URL` で以下のように設定したという前提で説明を進めていきます。
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/database_name"
```
Python を使用する場合は、最初に [PyMongo をインストール](http://api.mongodb.org/python/current/installation.html)します。
```python
import os
import pymongo
import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_cert_reqs=ssl.CERT_NONE)
db = client.get_default_database()
print db.collection_names()

```
サーバーの SSL 証明書を使用する場合は、以下のようなパラメーターを追加します。
```python
import os
import pymongo
import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_ca_certs="./servercert.crt")
db = client.get_default_database()
print db.collection_names()
```
あとは、通常どおりです。

## PHP、MongoDB、Compose

**クイック・メモ:** この例では、Compose の接続ストリングを環境変数 `MONGODB_URL` で以下のように設定したという前提で説明を進めていきます。
```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
元々は、AppFog の Larry Hitchon による [Gist](https://gist.github.com/coderoshi) から始まりました。それを Compose に合わせていくらか改良しています。とても[シンプルなドライバー](http://php.net/manual/en/class.mongodb.php)です。PECL がない場合は、[PECL をインストール](http://pecl.php.net/)してください。
```shell
    sudo pecl install mongo
```
その後、コード・サンプルも入手します。
```php
<!-- PHP Mongo Docs: http://php.net/manual/en/class.mongodb.php -->
<!-- PHP Mongo Docs: http://php.net/manual/en/class.mongodb.php -->
<html>
<body>
<h1>Compose のテスト</h1>
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

あとは、通常どおりに進めます。

## C# / .NET

ここでは、プロジェクトに対応する MongoDB C# ドライバーをすでにダウンロードしてインストールしたという前提で説明を進めます。 

その作業がまだ済んでいない場合は、[http://github.com/mongodb/mongo-csharp-driver/downloads](http://github.com/mongodb/mongo-csharp-driver/downloads) からインストーラーを入手してください。

インストールが完了したら、Visual Studio で **MongoDriver** と **MongoBSON** の DLL を参照できるようになります。

### 新規プロジェクトの作成

最初に、Visual Studio で新規プロジェクトを作成します。このサンプルでは、**ASP.NET MVC 2 Web アプリケーション**を選択しました。*(ここで取り上げるサンプルは、必要であれば WebForms に簡単に移植できます。)*
![Visual Studio でのプロジェクトの作成](./images/create-project.jpg)
### 参照の追加

次に、MongoDB データベースへの接続のために、**MongoDriver** と **MongoBSON** の DLL を参照する必要があります。C# ドライバーのインストーラーによって、その 2 つの DLL が参照ウィンドウの「.NET」タブに追加されています。

参照を追加したら、数行のコードで Compose データベースに接続して照会を実行できます。
![.NET リストからの参照の追加](./images/csharp-add-reference.jpg)
### 接続情報の追加

さらに、データベース情報に基づいて接続ストリングをセットアップします。アプリケーションの web.config ファイルに新しい接続ストリングを追加してください。以下のように入力します。
```csharp
<connectionStrings>
  <add name="ApplicationServices"
    connectionString="data source=.\SQLEXPRESS;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|aspnetdb.mdf;User Instance=true"
    providerName="System.Data.SqlClient" />
  <add name="Compose"
    connectionString="your mongo uri"/>
</connectionStrings>
```
もちろん、**your mongo uri** の部分は、Web インターフェースに表示される実際の Compose URI に置き換えます。以下のようにすれば、その情報を確認できます。

1. [Compose](https://www.compose.io) にログインします。
2. 接続先のデータベースのポート番号をクリックします。
3. 表示される URI をコピーするか、メモします。

必要なのはルート URI だけです。そうすれば、状況に応じてデータベースを選択できます。

### モデルとコントローラーのセットアップ

文書を保管するためのモデルを作成します。このサンプルで使用するデータベースは、開発者の基本情報をまとめたコレクションなので、そうした情報のプロパティーを保管するデータ変換オブジェクトを作成し、そのような DTO の汎用リストをモデルとして返すようにします。

Models ディレクトリーの下に **DeveloperDTO.cs** ファイルを入れる DTO ディレクトリーを作成します。
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

**DeveloperModel.cs** を Models ディレクトリーに追加します。*DTO ディレクトリーの using 節を必ず組み込んでください。*
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
次に、DeveloperController を作成します。

このサンプルでは、DeveloperController という名前にします。「Add action methods for Create, Update and Delete Scenarios (Create、Update、Delete の各シナリオのアクション・メソッドを追加する)」ボックスはチェック・マークを外したままでかまいません (この入門ガイドではその機能に触れません)。

作成したコントローラーを開いて、using 節を追加します。最低でも以下のようにしてください。

強調表示した行では、プライベートの `MongoDatabase` オブジェクトを宣言し、コンストラクターでインスタンス化しています。

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
ここでは、データベース呼び出しを別のメソッドに切り離しました (下記のコードを参照)。上記のコードでは、`MongoServer.Create` メソッドを使用し、web.config にある接続ストリングで Compose サーバーへの接続を初期化しています。次に、`GetDatabase` を呼び出して、`MongoDatabase` インスタンスを取得します。

ここでのデータベース名は **t2** です。 

このようなメソッドの詳細については、[CSharp ドライバーのチュートリアル](http://www.mongodb.org/display/DOCS/CSharp+Driver+Tutorial#CSharpDriverTutorial-Createmethod)を参照してください。
```csharp
static MongoDatabase retreive_mongodb_db()
{
  return MongoServer.Create(
    ConfigurationManager.ConnectionStrings["Compose"].ConnectionString)
    .GetDatabase("t2");
}
```
`Index` メソッドを `DeveloperController` に追加します。

こうして接続をセットアップできたので、これから作成するビューにデータベース・コンテンツを送信するためのメソッドを **DeveloperController.cs** に追加します。
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
これで完了です。

データベースへの接続も、コンテンツをビューに送信するためのメソッドも用意できました。あとは、モデルにデータを取り込んでビューに渡すだけです。

### データの表示

このサンプルでは、開発者の情報を反復的に取得して画面に出力します。Views ディレクトリーに `Developer` フォルダーを作成し、新しい .aspx ファイル (`Index.aspx`) を追加します。以下のコードを追加すれば、すべて完了です。
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
詳細については、[MongoDB C# Driver API の資料](http://api.mongodb.org/csharp/current/)で確認してください。
