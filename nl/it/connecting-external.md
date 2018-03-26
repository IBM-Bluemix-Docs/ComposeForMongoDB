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

# Connessione a un'applicazione esterna
{: #connecting-external-app}

Esistono due modi per collegare un'applicazione esterna a {{site.data.keyword.composeForMongoDB_full}}:

- Può essere utilizzata una **Stringa di connessione** in alcune librerie client che contiene tutte le informazioni necessarie per il collegamento di altre librerie.
- La **Riga di comando** è un comando pre-formattato che richiamerà `mongo` con i parametri corretti.

Troverai entrambe nella pagina *Overview* del tuo servizio {{site.data.keyword.composeForMongoDB}}.

Per informazioni approfondite sull'applicazione e il supporto del driver, controlla la documentazione appropriata e le comunità per il tuo linguaggio specifico e il driver che sta utilizzando la tua applicazione. 

Se stai cercando linguaggi non coperti qui, prova in [MongoDB.org Driver List](http://www.mongodb.org/display/DOCS/Drivers).
{: tip}

## Go / Golang

Come negli altri esempi, questo documento presuppone una variabile di ambiente denominata `MONGODB_URL` con la tua stringa di connessione.  Per impostare questa variabile, esegui quanto segue nella shell:

```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```

Il driver mgo è il driver mongo standard per go e può essere installato immettendo:

```shell
go get gopkg.in/mgo.v2
```

La documentazione mgo, disponibile all'indirizzo [http://labix.org/mgo](http://labix.org/mgo), copre molte connessioni tipiche ma come Compose MongoDB ha SSL abilitato, devono essere eseguiti alcuni ulteriori passi per creare e trasmettere la configurazione TLS alla funzione mgo Dial.

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
La riga che rimuove "?ssl=true" dall'URI (19) è obbligatoria perché mgo al momento riporta un errore se viene fornito il parametro standard per le connessioni SSL/TLS. Per collegare e verificare il certificato pubblico SSL del server è essenzialmente uguale, ma sono necessari ulteriori passi per caricare il certificato, in questo caso da un file "servercert.crt":

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

## Node.js / Nativo

**Nota rapida:** in questo esempio, stiamo assumendo che la tua stringa di connessione Compose sia impostata in una variabile di ambiente `MONGODB_URL`, come questa:
```javascript
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name?ssl=true"
```
Questo codice utilizza il driver [node-mongodb-native](https://github.com/christkv/node-mongodb-native), anche se in produzione potresti voler qualcosa leggermente meno... *sgradevole*. Come tutti i pacchetti Node validi, puoi ottenerlo tramite [NPM](http://npmjs.org/).
```shell
npm install mongodb
```
### Connessione a Node.js (Javascript)

Qui ci colleghiamo, soltanto stabilendo la codifica SSL/TLS senza convalidare il server:
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

Se vogliamo utilizzare il certificato pubblico SSL del server (in questo esempio archiviato in un file "servercert.crt") dobbiamo leggerlo e includerlo nelle opzioni, mentre cambiamo sslValidate con true in questo modo:

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
Se preferisci utilizzare una caratteristica ES6 con i tuoi esempi prova:
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
### Connessione a Node.js (Coffeescript)
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
Questo è il trucco!

## Mongoose e Node

**Nota rapida:** in questo esempio, stiamo assumendo che la tua stringa di connessione Compose sia impostata in una variabile di ambiente `MONGODB_URL`, come questa:
```shell
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name"
```
Questo codice utilizza il driver [mongoose](http://mongoosejs.com/). Come tutti i pacchetti Node validi, puoi ottenerlo tramite [NPM](http://npmjs.org/).
```shell
npm install mongoose
```
### Connessione a Mongoose (Javascript)

La tecnica di base è la stessa dei precedenti esempi Node.js/direct. Crea una mappa di opzioni, aggiungila ai parametri SSL richiesti e trasmettila con l'URL per MongoDB al metodo `mongoose.connect()`. Nel seguente esempio, ci colleghiamo a un certificato e, nuovamente, elenchiamo le raccolte disponibili:
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

// Se la connessione riscontra un errore
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

// Apriamo la connessione
mongoose.connect(process.env.MONGODB_URL, options);
```

## Ruby

**Nota rapida:** in questo esempio, stiamo assumendo che la tua stringa di connessione Compose sia impostata in una variabile di ambiente `MONGODB_URL`, come questa:
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
Se stai utilizzando Ruby (insieme a framework come Ruby on Rails, Sinatra, ecc.), puoi iniziare installando `mongo` (2.x) gem. Dovrebbe riuscire senza problemi, ma avrai bisogno di [RubyGems](http://rubygems.org). Se stai utilizzando un pre-1.9 Ruby, dovrai aggiungere `require 'rubygems'` all'inizio degli esempi.

 Senza utilizzare un certificato, il codice è semplicemente:
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
La riga Logger silenzia i messaggi di debug del driver (ce ne sono molti). Commentala se vuoi visualizzare altro.

Per collegarti a un certificato, il codice è simile, ma sono necessarie ulteriori impostazioni dell'opzione durante la connessione:
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
Tieni presente che il nome del file `servercert.crt` che viene trasmesso due volte per denotare il certificato ne è anche l'autorità.

## Python

**Nota rapida:** in questo esempio, stiamo assumendo che la tua stringa di connessione Compose sia impostata in una variabile di ambiente `MONGODB_URL`, come questa:
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/database_name"
```
Se stai utilizzando Python, puoi iniziare con [installing PyMongo](http://api.mongodb.org/python/current/installation.html).
```python
import os
import pymongo
import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_cert_reqs=ssl.CERT_NONE)
db = client.get_default_database()
print db.collection_names()

```
Per utilizzare il certificato SSL del server, è semplicemente una questione di aggiungerlo come un parametro in questo modo:
```python
import os
import pymongo
import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_ca_certs="./servercert.crt")
db = client.get_default_database()
print db.collection_names()
```
E quindi, dovresti essere sul percorso corretto.

## PHP, MongoDB e Compose

**Nota rapida:** in questo esempio, stiamo assumendo che la tua stringa di connessione Compose sia impostata in una variabile di ambiente `MONGODB_URL`, come questa:
```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
Questo è stato originariamente iniziato come un [Gist](https://gist.github.com/coderoshi) da Larry Hitchon di AppFog. Abbiamo apportato alcuni miglioramenti e lo abbiamo reso incentrato su Compose. È un valido [straightforward driver](http://php.net/manual/en/class.mongodb.php). Se non disponi di PECL, dovresti [installare PECL](http://pecl.php.net/).
```shell
    sudo pecl install mongo
```
E quindi, nel codice di esempio:
```php
<!-- PHP Mongo Docs: http://php.net/manual/en/class.mongodb.php -->
<!-- PHP Mongo Docs: http://php.net/manual/en/class.mongodb.php -->
<html>
<body>
<h1>Test di Compose</h1>
<?php
  try {
    // collegati a Compose presumendo che la tua variabile di ambiente MONGODB_URL
    // contenga la stringa di connessione
    $connection_url = getenv("MONGODB_URL");

     // crea l'oggetto di connessione mongo
    $m = new MongoClient($connection_url, array("ssl" => true));

    // estrai il nome del DB dal percorso di connessione
    $url = parse_url($connection_url);
    $db_name = preg_replace('/\/(.*)/', '$1', $url['path']);

    // utilizza il database a cui siamo collegati
    $db = $m->selectDB($db_name);

    echo "<h2>Collections</h2>";
    echo "<ul>";

    // stampa l'elenco delle raccolte
    $cursor = $db->listCollections();
    $collection_name = "";
    foreach( $cursor as $doc ) {
      echo "<li>" .  $doc->getName() . "</li>";
      $collection_name = $doc->getName();
    }
    echo "</ul>";

    // stampa l'ultima raccolta
    if ( $collection_name != "" ) {
      $collection = $db->selectCollection($collection_name);
      echo "<h2>Documents in ${collection_name}</h2>";

      // stampa solo i primi 5 documenti
      $cursor = $collection->find();
      $cursor->limit(5);
      echo $cursor->count() . ' document(s) found. <br/>';
      foreach( $cursor as $doc ) {
        echo "<pre>";
        var_dump($doc);
        echo "</pre>";
      }
    }

    // scollegati dal server
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

Dovresti essere sul percorso corretto.

## C# / .NET

Questa guida presuppone che tu abbia già scaricato e installato il driver MongoDB C# per il tuo progetto. 

Se non lo hai ancora fatto, puoi trovare il programma di installazione all'indirizzo: [http://github.com/mongodb/mongo-csharp-driver/downloads](http://github.com/mongodb/mongo-csharp-driver/downloads).

Dopo aver completato l'installazione, potrai fare riferimento ai DDL **MongoDriver** e **MongoBSON** in Visual Studio.

### Crea un nuovo progetto

Per iniziare, creiamo un nuovo progetto in Visual Studio. In questo esempio, ho scelto di utilizzare **ASP.NET MVC 2 Web Application**. *(Gli esempi forniti possono essere facilmente portati nei formati web se necessario.)*
![Creazione di un progetto in Visual Studio.](./images/create-project.jpg)
### Aggiungi i riferimenti

Ora dovrai fare riferimento ai DLL **MongoDriver** e **MongoBSON** in modo che da collegarci al nostro database MongoDB. Il programma di installazione del driver C# ha aggiunto questi due DLL alla scheda .NET della tua finestra di riferimento.

Con questi due riferimenti aggiunti, ora possiamo collegarci ed eseguire query nel nostro database Compose con poche righe di codice.
![Aggiunta di un riferimento dall'elenco .NET.](./images/csharp-add-reference.jpg)
### Aggiungi le informazioni di collegamento

Successivamente, configuriamo la nostra stringa di connessione con le nostre informazioni di collegamento aggiungendo una nuova stringa di connessione nel nostro file web.config dell'applicazione. La voce dovrebbe essere simile a quanto segue:
```csharp
<connectionStrings>
  <add name="ApplicationServices"
    connectionString="data source=.\SQLEXPRESS;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|aspnetdb.mdf;User Instance=true"
    providerName="System.Data.SqlClient" />
  <add name="Compose"
    connectionString="your mongo uri"/>
</connectionStrings>
```
Naturalmente, **your mongo uri** deve essere sostituito con l'URI Compose corrente che hai fornito nell'interfaccia web. Puoi richiamarlo nel seguente modo:

1. Accedi a [Compose](https://www.compose.io)
2. Fai clic sul numero di porta del database a cui vuoi collegarti
3. Copia o prendi nota dell'URI fornito.

Avrai bisogno solo dell'URI root, per cui puoi scegliere qualsiasi database vuoi.

### Configurazione del modello e del controller

Ora, creiamo un modello per ospitare i nostri documenti. Il database utilizzato in questo esempio è una raccolta di sviluppatori con informazioni di base, per cui creeremo un DTO (Data Transformation Object) per archiviare queste proprietà e restituire un elenco generico di questi DTO nel nostro modello.

Crea una directory DTO nella directory del modello per il nostro file **DeveloperDTO.cs**.
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

Aggiungi il nostro **DeveloperModel.cs** alla directory dei modelli. *Assicurati di includere una clausola di utilizzo per la directory DTO.*
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
Ora creiamo il nostro DeveloperController. 

In questo esempio, lo denominiamo DeveloperController. Puoi deselezionare la casella "Add action methods for Create, Update and Delete Scenarios" perché non intendiamo coprire questa funzionalità nella guida introduttiva.

Apri il controller appena creato e aggiungi le tue clausole di utilizzo. Come minimo, devi avere la seguente.

Nota la riga evidenziata quando abbiamo dichiarato un oggetto `MongoDatabase` privato e lo abbiamo istanziato nel nostro constructor.

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
Come puoi vedere, ho spezzato la chiamata al database in un metodo separato, che viene elencato di seguito. Qui, abbiamo utilizzato il metodo `MongoServer.Create` per inizializzare una connessione al nostro server Compose utilizzando la nostra stringa di connessione dal nostro web.config. Ora possiamo richiamare `GetDatabase` per ottenere la nostra istanza `MongoDatabase`. 

In questo caso, il nome del mio database è **t2**. 

Per ulteriori informazioni su questi metodi, dai un'occhiata a [CSharp Driver Tutorial](http://www.mongodb.org/display/DOCS/CSharp+Driver+Tutorial#CSharpDriverTutorial-Createmethod).
```csharp
static MongoDatabase retreive_mongodb_db()
{
  return MongoServer.Create(
    ConfigurationManager.ConnectionStrings["Compose"].ConnectionString)
    .GetDatabase("t2");
}
```
Aggiungi un metodo `Index` al nostro `DeveloperController`

Ora che le nostre connessioni sono configurate, aggiungiamo un metodo al nostro **DeveloperController.cs** che ci assisterà nella distribuzione del contenuto del database alla nostra vista che verrà presto creata.
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
Riuscito!

Ora abbiamo una connessione al nostro database e un metodo per distribuire il contenuto nella nostra vista. Tutto quello che dobbiamo fare ora è di idratare il nostro modello e trasmetterlo alla vista.

### Visualizzazione dei dati

In questo esempio, interagiamo semplicemente con i nostri sviluppatori e stampiamo i dati nella scheda. Crea una cartella `Developer` nella directory delle viste e aggiungi un nuovo file .aspx denominato `Index.aspx`. Aggiungi il seguente codice e con questo è tutto impostato.
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
Per ulteriori informazioni, controlla [MongoDB C# Driver API documentation](http://api.mongodb.org/csharp/current/).
