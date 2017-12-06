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

# 외부 애플리케이션 연결
{: #connecting-external-app}

외부 애플리케이션을 {{site.data.keyword.composeForMongoDB_full}}에 연결하는 두 가지 방법이 있습니다.

- **연결 문자열**은 일부 클라이언트 라이브러리에서 사용될 수 있으며 다른 라이브러리가 연결하는 데 필요한 모든 정보를 포함합니다. 
- **명령행**은 올바른 매개변수와 함께 `mongo`를 호출하는 사전 형식화된 명령입니다.

둘 다 {{site.data.keyword.composeForMongoDB}} 서비스의 *개요* 페이지에서 찾을 수 있습니다.

자세한 애플리케이션 및 드라이버 지원은 특정 언어와 애플리케이션에서 사용 중인 드라이버에 해당하는 문서와 커뮤니티를 확인하십시오. 

여기서 다루어지지 않는 언어를 찾고 있는 경우 [MongoDB.org 드라이버 목록](http://www.mongodb.org/display/DOCS/Drivers)을 확인해 보십시오.
{: tip}

## Go/Golang

다른 예제에서와 같이 이 문서에서는 연결 문자열을 포함하는 `MONGODB_URL`이라는 환경 변수가 있다고 가정합니다. 이 변수를 설정하려면 쉘에서 다음을 실행하십시오.

```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```

mgo 드라이버는 go에 대한 표준 mongo 드라이버이며 다음을 통해 설치할 수 있습니다.

```shell
go get gopkg.in/mgo.v2
```

[http://labix.org/mgo](http://labix.org/mgo)에서 사용 가능한 mgo 문서에서는 가장 일반적인 연결을 다루지만 Compose MongoDB에서는 SSL이 사용 가능하므로 TLS 구성을 작성하여 mgo 전화 걸기 기능에 전달하는 몇 가지 추가 단계가 있습니다.

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
SSL/TLS 연결에 대한 표준 매개변수가 제공된 경우 현재 mgo에 오류가 있으므로 "?ssl=true"를 URI에서 제거하는 행(19)이 필요합니다. 서버의 SSL 공용 인증서에 연결하여 확인하는 것은 본질적으로 동일하지만 인증서를 로드하기 위한(이 경우에는 "servercert.crt" 파일에서) 몇 가지 추가 단계가 필요합니다.

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

## Node.js/Native

**참고:** 이 예제에서는 Compose 연결 문자열이 다음과 같이 환경 변수 `MONGODB_URL`에 설정되었다고 가정합니다.
```javascript
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name?ssl=true"
```
프로덕션에서는 조금 덜 *열악한* 것을 원할 수 있지만 이 코드는 [node-mongodb-native](https://github.com/christkv/node-mongodb-native) 드라이버를 사용합니다. 우수한 모든 Node 패키지와 마찬가지로 [NPM](http://npmjs.org/)을 통해 얻을 수 있습니다.
```shell
npm install mongodb
```
### Node.js(Javascript)를 사용하여 연결

여기서는 서버의 유효성을 검증하지 않고 SSL/TLS 암호화만 사용으로 설정하여 연결합니다.
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

서버의 SSL 공용 인증서(이 예에서는 "servercert.crt" 파일에 저장됨)를 사용하려면 다음과 같이 sslValidate를 true로 전환하는 동안 해당 인증서를 읽고 옵션에 포함시켜야 합니다.

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
예제에 추가 ES6 특성을 원하는 경우 다음을 시도하십시오.
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
### Node.js(Coffeescript)를 사용하여 연결
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
효과가 있을 것입니다!

## Mongoose 및 Node

**참고:** 이 예제에서는 Compose 연결 문자열이 다음과 같이 환경 변수 `MONGODB_URL`에 설정되었다고 가정합니다.
```shell
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name"
```
이 코드에서는 [mongoose](http://mongoosejs.com/) 드라이버를 사용합니다. 우수한 모든 Node 패키지와 마찬가지로 [NPM](http://npmjs.org/)을 통해 얻을 수 있습니다.
```shell
npm install mongoose
```
### Mongoose(Javascript)를 사용하여 연결

기본 기술은 위의 Node.js/직접 예제와 동일합니다. 옵션 맵을 작성하고 필요한 SSL 매개변수를 추가한 후 해당 옵션 맵을 MongoDB에 대한 URL과 함께 `mongoose.connect()` 메소드에 전달합니다. 아래 예제에서는 인증서로 연결하고 다시 한 번 사용 가능한 콜렉션을 나열합니다.
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

**참고:** 이 예제에서는 Compose 연결 문자열이 다음과 같이 환경 변수 `MONGODB_URL`에 설정되었다고 가정합니다.
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
Ruby(Ruby on Rails, Sinatra 등과 같은 프레임워크와 함께)를 사용 중인 경우 `mongo`(2.x) gem을 설치하여 시작할 수 있습니다. 언급할 필요도 없지만 [RubyGems](http://rubygems.org)가 필요합니다. 1.9 이전의 Ruby를 사용 중인 경우 예제의 시작 부분에 `require 'rubygems'`를 추가해야 합니다.

 인증서를 사용하지 않으면 코드가 단순합니다.
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
Logger 행은 드라이버의 디버그 메시지의 알림을 차단합니다(많은 디버그 메시지가 있음). 자세히 보려면 이 행을 주석 처리하십시오.

인증서를 사용하여 연결하려는 경우 코드는 유사하지만 연결할 때 추가 옵션 설정이 필요합니다.
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
인증서를 표시하기 위해 두 번 전달되는 `servercert.crt` 파일 이름도 고유한 권한입니다.

## Python

**참고:** 이 예제에서는 Compose 연결 문자열이 다음과 같이 환경 변수 `MONGODB_URL`에 설정되었다고 가정합니다.
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/database_name"
```
Python을 사용 중인 경우 [PyMongo를 설치](http://api.mongodb.org/python/current/installation.html)하여 시작할 수 있습니다.
```python
import os
import pymongo
import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_cert_reqs=ssl.CERT_NONE)
db = client.get_default_database()
print db.collection_names()

```
서버의 SSL 인증서를 사용하려면 이와 같이 해당 SSL 인증서를 매개변수로 추가하기만 하면 됩니다.
```python
import os
import pymongo
import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_ca_certs="./servercert.crt")
db = client.get_default_database()
print db.collection_names()
```
그런 다음, 계속 진행해야 합니다.

## PHP, MongoDB 및 Compose

**참고:** 이 예제에서는 Compose 연결 문자열이 다음과 같이 환경 변수 `MONGODB_URL`에 설정되었다고 가정합니다.
```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
이는 원래 AppFog의 Larry Hitchon이 게시한 [Gist](https://gist.github.com/coderoshi)로 시작되었습니다. 일부를 개선하고 Compose 중심으로 변경했습니다. 이는 매우 [간단한 드라이버](http://php.net/manual/en/class.mongodb.php)입니다. PECL이 없는 경우 [PECL을 설치](http://pecl.php.net/)해야 합니다.
```shell
    sudo pecl install mongo
```
그런 다음, 코드 샘플을 살펴봅니다.
```php
<!-- PHP Mongo Docs: http://php.net/manual/en/class.mongodb.php -->
<!-- PHP Mongo Docs: http://php.net/manual/en/class.mongodb.php -->
<html>
<body>
<h1>Compose Test</h1>
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

원하는 대로 잘 진행되어야 합니다.



## C#/.NET

이 안내서에서는 프로젝트에 해당하는 MongoDB C#을 이미 다운로드하여 설치했다고 가정합니다. 

이를 수행하지 않은 경우 [http://github.com/mongodb/mongo-csharp-driver/downloads](http://github.com/mongodb/mongo-csharp-driver/downloads)에서 설치 프로그램을 찾을 수 있습니다.

설치가 완료되면 Visual Studio 내에서 **MongoDriver** 및 **MongoBSON** DLL을 참조할 수 있습니다.

### 새 프로젝트 작성

먼저, Visual Studio에서 새 프로젝트를 작성합니다. 이 예제에서는 **ASP.NET MVC 2 웹 애플리케이션**을 사용하도록 선택했습니다. *(필요한 경우 지정된 예제를 쉽게 WebForms에 이식할 수 있습니다.)*
![Visual Studio에서 프로젝트 작성](./images/create-project.jpg)
### 참조 추가

이제 해당 MongoDB 데이터베이스에 연결할 수 있도록 **MongoDriver** 및 **MongoBSON** DLL을 참조해야 합니다. C# 드라이버 설치 프로그램에서 이러한 두 개의 DLL을 참조 창의 .NET 탭에 추가했습니다.

이러한 참조가 추가되면 이제 몇 개의 코드 행만으로 Compose 데이터베이스에 연결하여 조회할 수 있습니다. ![Adding a reference from the .NET list.](./images/csharp-add-reference.jpg)
### 연결 정보 추가

다음으로, 애플리케이션의 web.config 파일에 새 연결 문자열을 추가하여 데이터베이스 정보가 포함된 연결 문자열을 설정합니다. 항목은 다음과 유사해야 합니다.
```csharp
<connectionStrings>
  <add name="ApplicationServices"
    connectionString="data source=.\SQLEXPRESS;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|aspnetdb.mdf;User Instance=true"
    providerName="System.Data.SqlClient" />
  <add name="Compose"
    connectionString="your mongo uri"/>
</connectionStrings>
```
물론 **your mongo uri**은 웹 인터페이스에서 제공되는 실제 Compose URI로 대체되어야 합니다. 다음을 수행하여 이를 가져올 수 있습니다.

1. [Compose](https://www.compose.io)에 로그인하십시오.
2. 연결하려는 데이터베이스의 포트 번호를 클릭하십시오.
3. 제공된 URI를 복사하거나 기록하십시오.

루트 URI만 필요하므로 원하는 데이터베이스를 선택할 수 있습니다.

### 모델 및 제어기 설정

이제 문서를 보유할 모델을 작성합니다. 이 예제에서 사용된 데이터베이스는 기본 정보가 포함된 개발자의 콜렉션이므로 이러한 특성을 저장할 DTO(Data Transformation Object)를 작성하고 모델에 있는 이러한 DTO의 일반 목록을 리턴합니다. 

Models 디렉토리 아래에 **DeveloperDTO.cs** 파일을 위한 DTO 디렉토리를 작성하십시오.
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

**DeveloperModel.cs**를 Models 디렉토리에 추가하십시오. *DTO 디렉토리에 대한 using 절이 포함되었는지 확인하십시오.*
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
이제 DeveloperController를 작성합니다.

이 예제에서는 DeveloperController로 이름을 지정합니다. 시작하기 안내서에서는 이 기능을 다루지 않으므로 "작성, 업데이트 및 삭제 시나리오에 대한 조치 메소드 추가" 상자를 선택 취소할 수 있습니다.

새로 작성된 제어기를 열고 using 절을 추가하십시오. 최소한 다음이 있어야 합니다.

개인용 `MongoDatabase` 오브젝트를 선언하고 생성자에서 인스턴스화하는 강조표시된 행에 주의하십시오.

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
살펴본 바와 같이 데이터베이스 호출을 아래 나열된 별도의 메소드로 분할했습니다. 여기서는 `MongoServer.Create` 메소드를 활용하여 web.config의 연결 문자열로 Compose 서버에 대한 연결을 초기화합니다. 이제 `MongoDatabase` 인스턴스를 가져오기 위해 `GetDatabase`를 호출할 수 있습니다.

이 경우 내 데이터베이스 이름은 **t2**입니다. 

이러한 메소드에 대한 자세한 정보는 [CSharp 드라이버 튜토리얼](http://www.mongodb.org/display/DOCS/CSharp+Driver+Tutorial#CSharpDriverTutorial-Createmethod)을 참조하십시오.
```csharp
static MongoDatabase retreive_mongodb_db()
{
  return MongoServer.Create(
    ConfigurationManager.ConnectionStrings["Compose"].ConnectionString)
    .GetDatabase("t2");
}
```
`DeveloperController`에 `Index` 메소드를 추가하십시오.

이제 연결이 설정되었습니다. 곧 작성될 보기에 데이터베이스 컨텐츠를 전달하는 데 도움을 주는 메소드를 **DeveloperController.cs**에 추가합니다.
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
성공!

이제 데이터베이스에 대한 연결 및 보기에 컨텐츠를 전달하기 위한 메소드가 있습니다. 지금 수행해야 하는 모든 작업은 모델을 하이드레이션하여 보기에 전달하는 것입니다.


### 데이터 보기

이 예제에서는 단순히 개발자를 반복하고 이를 화면에 인쇄합니다. Views 디렉토리에 `Developer` 폴더를 작성하고 `Index.aspx`라는 새 .aspx 파일을 추가하십시오. 아래 코드를 추가하면 설정이 완료됩니다.
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
자세한 정보는 [MongoDB C# 드라이버 API 문서](http://api.mongodb.org/csharp/current/)를 확인하십시오.
