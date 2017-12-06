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

# Externe Anwendung verbinden
{: #connecting-external-app}

Es gibt zwei Möglichkeiten, eine externe Anwendung mit {{site.data.keyword.composeForMongoDB_full}} zu verbinden:

- Eine **Verbindungszeichenfolge** kann von bestimmten Clientbibliotheken verwendet werden und enthält alle Informationen, die andere Bibliotheken zum Herstellen einer Verbindung benötigen.
- Die **Befehlszeile** ist ein vorformatierter Befehl, der `mongo` mit den korrekten Parametern aufruft. 

Beides finden Sie auf der Seite *Übersicht* Ihres {{site.data.keyword.composeForMongoDB}}-Service.

Wenn Sie eine umfassende Anwendungs- und Treiberunterstützung benötigen, informieren Sie sich in der entsprechenden Dokumentation und den Communitys Ihrer Sprache sowie über den Treiber, den Ihre Anwendung verwendet. 

Wenn Sie Sprachen suchen, die hier nicht abgedeckt sind, suchen Sie in der [MongoDB.org-Treiberliste](http://www.mongodb.org/display/DOCS/Drivers).
{: tip}

## Go / Golang

Wie in den anderen Beispielen wird in diesem Dokument vorausgesetzt, dass eine Umgebungsvariable namens `MONGODB_URL` bei Ihrer Verbindungszeichenfolge vorhanden ist. Führen Sie den folgenden Befehl in der Shell aus, um diese Variable festzulegen:

```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```

Der Treiber mgo ist der mongo-Standardtreiber für go und kann wie folgt installiert werden:

```shell
go get gopkg.in/mgo.v2
```

Die Dokumentation zu mgo, die unter [http://labix.org/mgo](http://labix.org/mgo) verfügbar ist, deckt die meisten üblichen Verbindungen ab, doch weil SSL für Compose MongoDB aktiviert ist, müssen einige Zusatzschritte ausgeführt werden, um die TLS-Konfiguration zu erstellen und an die Wählfunktion von mgo zu übergeben.

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
Die Zeile, mit der "?ssl=true" aus dem URI (19) entfernt wird, ist erforderlich, weil mgo derzeit angesichts des Standardparameters für SSL/TLS-Verbindungen einen "error if"-Fehler zurückgibt. Gehen Sie wie folgt vor, um eine Verbindung herzustellen und sicherzustellen, dass das öffentliche SSL-Zertifikat grundsätzlich dasselbe ist, jedoch bestimmte Zusatzschritte zum Laden des Zertifikats benötigt, in diesem Fall aus der Datei "servercert.crt":
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

**Kurzhinweis:** Im vorliegenden Beispiel wird vorausgesetzt, dass Ihre Compose-Verbindungszeichenfolge wie folgt in einer Umgebungsvariablen namens `MONGODB_URL` festgelegt wird:
```javascript
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name?ssl=true"
```
Dieser Code verwendet den Treiber [node-mongodb-native](https://github.com/christkv/node-mongodb-native). Für die Produktion empfiehlt sich jedoch ein etwas weniger *verschachtelter* Code. Wie alle guten Node-Pakete können Sie diesen über [NPM](http://npmjs.org/) abrufen.
```shell
npm install mongodb
```
### Verbindung mit Node.js herstellen (JavaScript)

Hier erfolgt die Verbindung einfach durch Aktivieren der SSL/TLS-Verschlüsselung, ohne den Server zu validieren:
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

Wenn Sie das öffentliche SSL-Zertifikat des Servers (im vorliegenden Beispiel in der Datei "servercert.crt" gespeichert) verwenden wollen, müssen Sie es lesen und in die Optionen einbinden. Dabei muss "sslValidate" wie folgt auf "true" gesetzt werden:

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
Versuchen Sie Folgendes, wenn Ihre Beispiele mehr ES6 enthalten sollen:
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
### Verbindung mit Node.js herstellen (Coffeescript)
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
Nun sollte es funktionieren!

## Mongoose und Node

**Kurzhinweis:** Im vorliegenden Beispiel wird vorausgesetzt, dass Ihre Compose-Verbindungszeichenfolge wie folgt in einer Umgebungsvariablen namens `MONGODB_URL` festgelegt wird:
```shell
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name"
```
Dieser Code verwendet den Treiber [mongoose](http://mongoosejs.com/). Wie alle guten Node-Pakete können Sie diesen über [NPM](http://npmjs.org/) abrufen.
```shell
npm install mongoose
```
### Verbindung mit Mongoose herstellen (JavaScript)

Das grundlegende Verfahren ist dasselbe wie oben bei den Beispielen mit Node.js/direct. Erstellen Sie eine Optionskarte, fügen Sie die erforderlichen SSL-Parameter hinzu und übergeben Sie diese Optionskarte mit der URL für MongoDB an die Methode `mongoose.connect()`. Im nachstehenden Beispiel wird eine Verbindung zu einem Zertifikat hergestellt und es werden wieder die verfügbaren Datensammlungen aufgeführt:
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

**Kurzhinweis:** Im vorliegenden Beispiel wird vorausgesetzt, dass Ihre Compose-Verbindungszeichenfolge wie folgt in einer Umgebungsvariablen namens `MONGODB_URL` festgelegt wird:
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
Wenn Sie Ruby (neben Frameworks wie Ruby on Rails, Sinatra, etc.) verwenden, können Sie zu Beginn das Gem `mongo` (2.x) installieren. Selbstverständlich benötigen Sie dazu [RubyGems](http://rubygems.org). Wenn Sie eine frühere Ruby-Version als 1.9 verwenden, müssen Sie am Anfang der Beispiele `require 'rubygems'` hinzufügen.

 Ohne Verwendung eines Zertifikats lautet der Code einfach:
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
In der Zeile "Logger" werden die (zahlreichen) Debugnachrichten des Treibers stumm geschaltet. Setzen Sie in dieser Zeile Kommentarzeichen, wenn Sie mehr sehen wollen.

Der Code zum Herstellen einer Verbindung zu einem Zertifikat ist ähnlich, erfordert jedoch beim Verbinden mehr Optionseinstellungen:
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
Beachten Sie, dass der Dateiname `servercert.crt` zweimal übergeben wird, um festzulegen, dass das Zertifikat auch seine eigene Berechtigung ist.

## Python

**Kurzhinweis:** Im vorliegenden Beispiel wird vorausgesetzt, dass Ihre Compose-Verbindungszeichenfolge wie folgt in einer Umgebungsvariablen namens `MONGODB_URL` festgelegt wird:
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/database_name"
```
Wenn Sie Python verwenden, können Sie zu Beginn [PyMongo installieren](http://api.mongodb.org/python/current/installation.html).
```python
import os
import pymongo
import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_cert_reqs=ssl.CERT_NONE)
db = client.get_default_database()
print db.collection_names()

```
Wenn Sie das SSL-Zertifikat des Servers verwenden wollen, müssen Sie es einfach als Parameter hinzufügen:
```python
import os
import pymongo
import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_ca_certs="./servercert.crt")
db = client.get_default_database()
print db.collection_names()
```
Und dann dürfte alles funktionieren.

## PHP, MongoDB und Compose

**Kurzhinweis:** Im vorliegenden Beispiel wird vorausgesetzt, dass Ihre Compose-Verbindungszeichenfolge wie folgt in einer Umgebungsvariablen namens `MONGODB_URL` festgelegt wird:
```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
Dies hat ursprünglich als [Gist](https://gist.github.com/coderoshi) von Larry Hitchon von AppFog angefangen. Wir haben einige Verbesserungen vorgenommen und ihn auf Compose zentriert. Es ist ein ziemlich [unkomplizierter Treiber](http://php.net/manual/en/class.mongodb.php). Wenn Sie nicht über PECL verfügen, sollten Sie [PECL installieren](http://pecl.php.net/).
```shell
    sudo pecl install mongo
```
Und hier das Codebeispiel:
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

Und dann dürfte alles funktionieren.

## C# / .NET

In diesem Handbuch wird vorausgesetzt, dass Sie den MongoDB C#-Treiber für Ihr Projekt bereits heruntergeladen und installiert haben. 

Wenn nicht, finden Sie das Installationsprogramm hier:[http://github.com/mongodb/mongo-csharp-driver/downloads](http://github.com/mongodb/mongo-csharp-driver/downloads).

Sobald die Installation abgeschlossen ist, können Sie auf die DLLs **MongoDriver** und **MongoBSON** in Visual Studio verweisen.

### Neues Projekt erstellen

Erstellen Sie zunächst ein neues Projekt in Visual Studio. Im vorliegenden Beispiel wurde **ASP.NET MVC 2 Web Application** ausgewählt. *(Die angegebenen Beispiele können bei Bedarf ohne großen Aufwand auf WebForms portiert werden.)*
![Projekt in Visual Studio erstellen.](./images/create-project.jpg)
### Verweise hinzufügen

Nun müssen Sie auf die DLLs **MongoDriver** und **MongoBSON** verweisen, damit Sie eine Verbindung zu Ihrer MongoDB-Datenbank herstellen können. Das Installationsprogramm des C#-Treibers hat diese beiden DLLs zur Registerkarte '.NET' Ihres Verweisfensters hinzugefügt.

Nachdem diese Verweise hinzugefügt wurden, können Sie mit einigen wenigen Codezeilen eine Verbindung zu Ihrer Compose-Datenbank herstellen und diese abfragen.
![Verweis aus der Liste '.NET' hinzufügen.](./images/csharp-add-reference.jpg)
### Verbindungsinformationen hinzufügen

Geben Sie als Nächstes Datenbankinformationen in Ihre Verbindungszeichenfolge ein, indem Sie eine neue Verbindungszeichenfolge zu der Datei 'web.config' Ihrer Anwendung hinzufügen. Die Eingabe sollte der folgenden ähneln:
```csharp
<connectionStrings>
  <add name="ApplicationServices"
    connectionString="data source=.\SQLEXPRESS;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|aspnetdb.mdf;User Instance=true"
    providerName="System.Data.SqlClient" />
  <add name="Compose"
    connectionString="your mongo uri"/>
</connectionStrings>
```
Natürlich sollte **your mongo uri** durch den tatsächlichen Compose-URI ersetzt werden, der Ihnen in der Webschnittstelle bereitgestellt wird. Führen Sie dazu die folgenden Schritte aus:

1. Melden Sie sich bei [Compose](https://www.compose.io) an.
2. Klicken Sie auf die Portnummer der Datenbank, zu der Sie eine Verbindung herstellen wollen
3. Kopieren oder notieren Sie den bereitgestellten URI.

Sie benötigen nur den Root-URI, daher können Sie eine beliebige Datenbank auswählen.

### Modell und Controller einrichten

Erstellen Sie nun ein Modell, in dem Ihre Dokumente enthalten sein sollen. Die im vorliegenden Beispiel verwendete Datenbank ist eine Gruppe von Entwicklern mit Basisinformationen. Erstellen Sie daher eine Datentransformation (DTO), um diese Eigenschaften zu speichern und eine generische Liste dieser DTOs in Ihr Modell zurückzugeben.

Erstellen Sie im Verzeichnis 'DTO' ein Verzeichnis namens 'Model' für Ihre Datei **DeveloperDTO.cs**.
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

Fügen Sie Ihre Datei **DeveloperModel.cs** zum Verzeichnis 'Models' hinzu. *Achten Sie darauf, eine using-Klausel für das Verzeichnis 'DTO' einzubinden.*
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
Erstellen Sie nun den Developer Controller.

Im vorliegenden Beispiel wird er als DeveloperController bezeichnet. Sie können das Kontrollkästchen zum Hinzufügen von Aktionsmethoden für Erstellungs-, Aktualisierungs- und Löschszenarios inaktivieren, da diese Funktion in der Einführung nicht behandelt wird.

Öffnen Sie Ihren neu erstellten Controller und führen Sie Ihre using-Klauseln hinzu. Sie sollten mindestens die folgenden Klauseln hinzufügen.

Beachten Sie die hervorgehobene Zeile, in der ein privates Objekt `MongoDatabase` deklariert und in Ihrem Konstruktor instanziiert wird.

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
Sie sehen, dass der Datenbankaufruf in eine separate Methode aufgeteilt wurde, die nachstehend aufgeführt ist. Hier wird die Methode `MongoServer.Create` verwendet, um mithilfe Ihrer Verbindungszeichenfolge aus Ihrer Datei 'web.config' eine Verbindung zu Ihrem Compose-Server zu initialisieren. Nun können Sie `GetDatabase` aufrufen, um Ihre Instanz von `MongoDatabase` abzurufen.
In diesem Fall ist der Datenbankname **t2**. 

Weitere Informationen zu diesen Methoden finden Sie im [Lernprogramm zum CSharp-Treiber](http://www.mongodb.org/display/DOCS/CSharp+Driver+Tutorial#CSharpDriverTutorial-Createmethod).
```csharp
static MongoDatabase retreive_mongodb_db()
{
  return MongoServer.Create(
    ConfigurationManager.ConnectionStrings["Compose"].ConnectionString)
    .GetDatabase("t2");
}
```
Fügen Sie eine Methode namens `Index` zu `DeveloperController` hinzu.

Nun Sind Ihre Verbindungen eingerichtet und Sie können eine Methode zu **DeveloperController.cs** hinzufügen, die die Bereitstellung des Datenbankinhalts an Ihre bald zu erstellende Ansicht unterstützt.
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
Erfolgreich!

Nun verfügen Sie über eine Verbindung zu Ihrer Datenbank und über eine Methode zum Bereitstellen des Inhalts an Ihre Ansicht. Nun müssen Sie noch das Modell aktivieren und an die Ansicht übergeben.

### Daten anzeigen

In diesem Beispiel iterieren Sie einfach Ihre Entwickler und geben sie am Bildschirm aus. Erstellen Sie im Verzeichnis 'Views' einen Ordner namens `Developer` und fügen Sie eine neue .aspx-Datei namens `Index.aspx` hinzu. Fügen Sie den Code unten ein. Dann sind Sie fertig.
```asp
<%%@ Page Title="" Language="C#" MasterPageFile="~/Views/Shared/Site.Master" Inherits="System.Web.Mvc.ViewPage<mongodb_csharp.Models.DeveloperModel>" %>

<asp:Content ID="Content1" ContentPlaceHolderID="TitleContent" runat="server">
  Index
</asp:Content>

<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">

  <h2>Entwickler</h2>

  <ul>
    <%% foreach(var developer in Model.developers) { %>
      <li><%%= developer.name %> works for <%%= developer.company %> and is proficient in <%%= developer.languages %></li>
    <%% } %>
  </ul>
</asp:Content>
```
Weitere Informationen finden Sie in der [Dokumentation zur MongoDB C#-Treiber-API](http://api.mongodb.org/csharp/current/).
