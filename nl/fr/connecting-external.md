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

# Connexion d'une application externe
{: #connecting-external-app}

Deux moyens permettent de connecter une application externe à {{site.data.keyword.composeForMongoDB_full}} :

- Une **chaîne de connexion**, qui peut être utilisée par certaines bibliothèques client et qui contient toutes les informations requises pour la connexion d'autres bibliothèques.
- Une **ligne de commande**, qui est une commande préformatée qui appellera `mongo` avec les paramètres appropriés.

Les deux sont disponibles sur la page *Vue d'ensemble* du service {{site.data.keyword.composeForMongoDB}}.

Pour une prise en charge plus poussée des applications et des pilotes, voir la documentation et les communautés propres à votre langue et au pilote que votre application utilise. 

Pour des langues qui ne figurent pas ici, voir la [Liste des pilotes MongoDB.org](http://www.mongodb.org/display/DOCS/Drivers).
{: tip}

## Go / Golang

Comme dans les autres exemples, ce document intègre une variable d'environnement nommée `MONGODB_URL` à votre chaîne de connexion.  Pour définir cette variable, exécutez la ligne suivante dans le shell :

```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```

Le pilote mgo est le pilote mongo standard pour go, et peut être installé avec la commande :

```shell
go get gopkg.in/mgo.v2
```

La documentation mgo, disponible à l'adresse [http://labix.org/mgo](http://labix.org/mgo), couvre la plupart des connexions standard mais étant donné que SSL est activé pour Compose MongoDB, certaines étapes supplémentaires sont à effectuer pour créer et transmettre la configuration TLS à la fonction Dial de mgo.

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
La ligne qui supprime "?ssl=true" de l'URI (19) est nécessaire car mgo générera une erreur si vous entrez le paramètre standard pour les connexions SSL/TLS. La connexion et la vérification du certificat public SSL s'effectuent pratiquement de la même manière, mais certaines étapes supplémentaires sont requises pour charger le certificat, ici, à partir d'un fichier "servercert.crt" :

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

## Node.js / Natif

**Note rapide :** Dans cet exemple, nous supposons que votre chaîne de connexion Compose est définie dans une variable d'environnement `MONGODB_URL` de la manière suivante :
```javascript
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name?ssl=true"
```
Ce code utilise le pilote [node-mongodb-native](https://github.com/christkv/node-mongodb-native), même si en production vous souhaiterez sans doute quelque chose de moins... *niché*. Comme tous les bons packages Node, vous pouvez l'obtenir via [NPM](http://npmjs.org/).
```shell
npm install mongodb
```
### Connexion avec Node.js (Javascript)

Maintenant, nous nous connectons, en activant simplement le chiffrement SSL/TLS sans valider le serveur :
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

Si nous voulons utiliser le certificat public SSL du serveur (stocké dans un fichier "servercert.crt" dans cet exemple), nous devons le lire et l'inclure dans les options, et définir sslValidate sur true comme suit :

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
Si vous préférez que vos exemples penchent plus dans ES6, essayez ce qui suit :
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
### Connexion avec Node.js (Coffeescript)
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
Cela devrait faire l'affaire.

## Mongoose et Node

**Note rapide :** Dans cet exemple, nous supposons que votre chaîne de connexion Compose est définie dans une variable d'environnement `MONGODB_URL` de la manière suivante :
```shell
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name"
```
Ce code utilise le pilote [mongoose](http://mongoosejs.com/). Comme tous les bons packages Node, vous pouvez l'obtenir via [NPM](http://npmjs.org/).
```shell
npm install mongoose
```
### Connexion avec Mongoose (Javascript)

La technique de base est identique à celle des exemples Node.js/direct ci-dessus. Créez une mappe d'options, ajoutez les paramètres SSL requis puis transmettez cette mappe d'options avec l'adresse URL pour MongoDB à la méthode `mongoose.connect()`. Dans l'exemple ci-dessous, nous établissons la connexion avec un certificat et, de nouveau, listons les collections disponibles :
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

**Note rapide :** Dans cet exemple, nous supposons que votre chaîne de connexion Compose est définie dans une variable d'environnement `MONGODB_URL` de la manière suivante :
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
Si vous utilisez Ruby (ainsi que des infrastructures telles que Ruby on Rails, Sinatra, etc.), vous pouvez commencer par installer le gem `mongo` (2.x). Vous pouvez vous en passer, mais vous aurez besoin de [RubyGems](http://rubygems.org). Si vous utilisez une version antérieure à 1.9 de Ruby, vous devrez ajouter `require 'rubygems'` au début des exemples.

 Sans utiliser un certificat, le code est simplement :
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
La ligne Logger neutralise les messages de débogage du pilote (ils sont nombreux). Mettez-la en commentaire si vous voulez en voir plus.

Pour une connexion avec un certificat, le code est similaire, mais des paramètres d'option supplémentaires sont nécessaires lors de la connexion :
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
Vous noterez que le nom de fichier `servercert.crt` est transmis deux fois pour indiquer que le certificat est également sa propre autorité.

## Python

**Note rapide :** Dans cet exemple, nous supposons que votre chaîne de connexion Compose est définie dans une variable d'environnement `MONGODB_URL` de la manière suivante :
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/database_name"
```
Si vous utilisez Python, vous pouvez commencer par [installer PyMongo](http://api.mongodb.org/python/current/installation.html).
```python
import os
import pymongo
import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_cert_reqs=ssl.CERT_NONE)
db = client.get_default_database()
print db.collection_names()

```
Pour utiliser le certificat SSL du serveur, il suffit de l'ajouter en tant que paramètre comme suit :
```python
import os
import pymongo
import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_ca_certs="./servercert.crt")
db = client.get_default_database()
print db.collection_names()
```
Vous devriez alors être en bonne voie.

## PHP, MongoDB et Compose

**Note rapide :** Dans cet exemple, nous supposons que votre chaîne de connexion Compose est définie dans une variable d'environnement `MONGODB_URL` de la manière suivante :
```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
Tout a commencé par une [synthèse](https://gist.github.com/coderoshi) de Larry Hitchon de chez AppFog. Nous avons apporté certaines améliorations et centré sur Compose. C'est un [pilote relativement simple](http://php.net/manual/en/class.mongodb.php). Si vous ne disposez pas de PECL, vous devez [installer PECL](http://pecl.php.net/).
```shell
    sudo pecl install mongo
```
Et maintenant, l'exemple de code :
```php
<!-- PHP Mongo Docs: http://php.net/manual/en/class.mongodb.php -->
<!-- PHP Mongo Docs: http://php.net/manual/en/class.mongodb.php -->
<html>
<body>
<h1>Test de Compose</h1>
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

Cela devrait vous mener sur la bonne voie.

## C# / .NET

Ce guide suppose que vous avez déjà téléchargé et installé le pilote MongoDB C# pour votre projet. 

Si vous ne l'avez pas encore fait, vous trouverez le programme d'installation à l'adresse : [http://github.com/mongodb/mongo-csharp-driver/downloads](http://github.com/mongodb/mongo-csharp-driver/downloads).

Une fois l'installation terminée, vous serez à même de référencer les DLL **MongoDriver** et **MongoBSON** dans Visual Studio.

### Création d'un nouveau projet

Pour commencer, créons un nouveau projet dans Visual Studio. J'ai choisi d'utiliser l'**application Web ASP.NET MVC 2** dans cet exemple. *(Les exemples fournis peuvent aisément être transférés vers WebForms, si nécessaire.)* ![Création d'un projet dans Visual Studio.](./images/create-project.jpg)
### Ajout de références

Maintenant, nous devons référencer les DLL **MongoDriver** et **MongoBSON** pour pouvoir nous connecter à notre base de données MongoDB. Le programme d'installation du pilote C# a ajouté ces deux DLL à l'onglet .NET de votre fenêtre de référence.

Une fois ces références ajoutées, nous pouvons nous connecter à notre base de données Compose et l'interroger avec seulement quelque lignes de code.
![Ajout d'une référence depuis la liste .NET.](./images/csharp-add-reference.jpg)
### Ajout d'informations de connexion

Ensuite, configurons notre chaîne de connexion avec nos informations de base de données en ajoutant une nouvelle chaîne de connexion dans le fichier web.config de l'application. L'entrée devrait ressembler à la suivante :
```csharp
<connectionStrings>
  <add name="ApplicationServices"
    connectionString="data source=.\SQLEXPRESS;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|aspnetdb.mdf;User Instance=true"
    providerName="System.Data.SqlClient" />
  <add name="Compose"
    connectionString="your mongo uri"/>
</connectionStrings>
```
Bien sûr, **your mongo uri** doit être remplacé par l'URI Compose réel qui vous a été fourni dans l'interface Web. Pour l'obtenir, procédez comme suit :

1. Connectez-vous à [Compose](https://www.compose.io)
2. Cliquez sur le numéro de port de la base de données à laquelle vous voulez vous connecter
3. Copiez ou notez l'identificateur URI fourni.

Etant donné que vous n'avez besoin que de l'URI racine, vous pouvez vous connecter à n'importe quelle base de données.

### Configuration de votre modèle et de votre contrôleur

Maintenant, créons un modèle destiné à contenir nos documents. La base de données utilisée dans cet exemple étant une collection de développeurs avec des informations de base, nous créerons un objet DTO (Data Transformation Object) pour stocker ces propriétés et renvoyer une liste générique de ces DTO dans notre modèle.

Créez un répertoire DTO sous le répertoire Model pour le fichier **DeveloperDTO.cs**.
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

Ajoutez **DeveloperModel.cs** au répertoire Model. *Assurez-vous d'inclure une clause using pour le répertoire DTO.*
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
Créons maintenant notre contrôleur de développeur. 

Dans cet exemple, nommons-le DeveloperController. Vous pouvez désélectionner la case "Add action methods for Create, Update and Delete Scenarios" car nous ne traiterons pas cette fonctionnalité dans le guide d'initiation.

Ouvrez le contrôleur que vous venez de créer et ajoutez vos clauses using. Au minimum, vous devez avoir les lignes ci-dessous.

Notez la ligne mise en évidence où nous déclarons un objet `MongoDatabase` privé et son instanciation dans notre constructeur.

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
Comme vous le voyez, j'ai séparé l'appel de la base de données dans une méthode distincte, répertoriée ci-dessous. Ici, nous utilisons la méthode `MongoServer.Create` pour initialiser une connexion au serveur Compose en nous servant de la chaîne de connexion issue du fichier web.config. Nous pouvons maintenant appeler `GetDatabase` pour obtenir notre instance `MongoDatabase`. 

Dans ce cas, le nom de ma base de données est **t2**. 

Pour plus d'informations sur ces méthodes, voir le [tutoriel du pilote CSharp](http://www.mongodb.org/display/DOCS/CSharp+Driver+Tutorial#CSharpDriverTutorial-Createmethod).
```csharp
static MongoDatabase retreive_mongodb_db()
{
  return MongoServer.Create(
    ConfigurationManager.ConnectionStrings["Compose"].ConnectionString)
    .GetDatabase("t2");
}
```
Ajoutez une méthode `Index` à `DeveloperController`

Maintenant que nos connexions sont configurées, ajoutons à **DeveloperController.cs** une méthode qui nous aidera à distribuer le contenu de la base de données à la vue que nous allons rapidement créer.
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
Bravo !

Nous disposons maintenant d'une connexion à notre base de données et d'une méthode pour distribuer son contenu à notre vue. Tout ce que nous avons à faire est de nourrir notre modèle et de le transmettre à la vue.

### Affichage de données

Dans cet exemple, nous allons simplement itérer nos développeurs et les afficher à l'écran. Créez un dossier `Developer` dans le répertoire Views et ajoutez un nouveau fichier .aspx nommé `Index.aspx`. Ajoutez le code ci-dessous et vous voilà prêt.
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
Pour plus d'informations, voir la [documentation d'API du pilote MongoDB C#](http://api.mongodb.org/csharp/current/).
