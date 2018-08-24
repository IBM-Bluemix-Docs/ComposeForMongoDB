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

# Conexión de una aplicación externa
{: #connecting-external-app}

Hay dos formas de conectar una aplicación externa a {{site.data.keyword.composeForMongoDB_full}}:

- Algunas bibliotecas de cliente pueden utilizar una **serie de conexión**, que contiene toda la información necesaria para que se conecten otras bibliotecas.
- La **línea de mandatos** es un mandato preformateado que invocará `mongo` con los parámetros correctos.

Encontrará ambas en la página *Visión general* del servicio {{site.data.keyword.composeForMongoDB}}.

Para obtener soporte completo para la aplicación y el controlador, consulte la documentación adecuada y las comunidades correspondientes a su idioma específico y para el controlador que utiliza la aplicación. 

Si busca idiomas que no quedan cubiertos aquí, consulte la [Lista de controladores de MongoDB.org](http://www.mongodb.org/display/DOCS/Drivers).
{: tip}

## Go / Golang

Al igual que en el resto de ejemplos, en este documento se supone que hay una variable de entorno denominada `MONGODB_URL` con la serie de conexión.  Para establecer esta variable, ejecute el siguiente mandato en el shell:

```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```

El controlador mgo es el controlador estándar de mongo para go, y se instala del siguiente modo:

```shell
go get gopkg.in/mgo.v2
```

La documentación de mgo, que está disponible en [http://labix.org/mgo](http://labix.org/mgo), cubre más conexiones típicas, pero como Compose MongoDB está habilitado para SSL, hay algunos pasos adicionales que se deben seguir para crear y pasar la configuración de TLS a la función Dial de mgo.

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
La línea que elimina "?ssl=true" del URI (19) es necesaria porque mgo actualmente dará un error si se le pasa el parámetro estándar para conexiones SSL/TLS. Para conectarse y verificar, el certificado público SSL del servidor público es básicamente el mismo, pero se requieren algunos pasos adicionales para cargar el certificado, en este caso desde un archivo "servercert.crt":

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

**Nota rápida:** En este ejemplo, se da por supuesto que la serie de conexión de Compose está establecida en una variable de entorno `MONGODB_URL`, como este:
```javascript
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name?ssl=true"
```
En este código se utiliza el controlador [node-mongodb-native](https://github.com/christkv/node-mongodb-native), aunque en producción es posible que desee utilizar uno menos... *antipático*. Al igual que todos los paquetes de Node, puede obtenerlo a través de [NPM](http://npmjs.org/).
```shell
npm install mongodb
```
### Conexión con Node.js (Javascript)

Aquí estableceremos la conexión, simplemente habilitando el cifrado SSL/TLS sin validar el servidor:
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

Si queremos utilizar el certificado público SSL del servidor (almacenado en un archivo "servercert.crt" en este ejemplo), debemos leerlo e incluirlo en las opciones, cambiando sslValidate por true como en este ejemplo:

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
Si prefiere utilizar ES6 en los ejemplos, intente lo siguiente:
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
### Conexión con Node.js (Coffeescript)
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
Esto debería funcionar correctamente.

## Mongoose y Node

**Nota rápida:** En este ejemplo, se da por supuesto que la serie de conexión de Compose está establecida en una variable de entorno `MONGODB_URL`, como este:
```shell
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name"
```
Este código utiliza el controlador [mongoose](http://mongoosejs.com/). Al igual que todos los paquetes de Node, puede obtenerlo a través de [NPM](http://npmjs.org/).
```shell
npm install mongoose
```
### Conexión con Mongoose (Javascript)

La técnica básica es la misma que la de los ejemplos de Node.js/direct anteriores. Cree una correlación de opciones, añada los parámetros SSL necesarios y pase esta correlación de opciones con el URL de MongoDB al método `mongoose.connect()`. En el siguiente ejemplo, nos conectaremos con un certificado y, de nuevo, obtendremos una lista de las colecciones disponibles:
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

// Si la conexión emite un error
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

// Vamos a abrir la conexión
mongoose.connect(process.env.MONGODB_URL, options);
```

## Ruby

**Nota rápida:** En este ejemplo, se da por supuesto que la serie de conexión de Compose está establecida en una variable de entorno `MONGODB_URL`, como este:
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
Si utiliza Ruby (con infraestructuras como Ruby on Rails, Sinatra, etc.),puede comenzar instalando el gem `mongo` (2.x). Obviamente, necesitará [RubyGems](http://rubygems.org). Si utiliza un entorno Ruby anterior al 1.9, deberá añadir `require 'rubygems'` al principio de los ejemplos.

 Sin utilizando un certificado, el código es simplemente:
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
La línea Logger anula los mensajes de depuración del controlador (hay muchos). Comente la línea si desea verlos.

Para conectarse con un certificado, el código es parecido, pero se necesitan más valores de opciones para conectar:
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
Observe que el nombre de archivo `servercert.crt` se pasa dos veces para indicar que el certificado también es su propia autoridad.

## Python

**Nota rápida:** En este ejemplo, se da por supuesto que la serie de conexión de Compose está establecida en una variable de entorno `MONGODB_URL`, como este:
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/database_name"
```
Si utiliza Python, puede empezar [instalando PyMongo](http://api.mongodb.org/python/current/installation.html).
```python
import os
import pymongo
import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_cert_reqs=ssl.CERT_NONE)
db = client.get_default_database()
print db.collection_names()

```
Para utilizar el certificado SSL del servidor, solo tiene que añadir como un parámetro como este:
```python
import os
import pymongo
import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_ca_certs="./servercert.crt")
db = client.get_default_database()
print db.collection_names()
```
Esto debería ser suficiente.

## PHP, MongoDB y Compose

**Nota rápida:** En este ejemplo, se da por supuesto que la serie de conexión de Compose está establecida en una variable de entorno `MONGODB_URL`, como este:
```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
Esto comenzó como un [Gist](https://gist.github.com/coderoshi) de Larry Hitchon de AppFog. Hemos incorporado algunas mejoras y lo hemos centrado en Compose. En un [controlador muy sencillo](http://php.net/manual/en/class.mongodb.php). Si no tiene PECL, debe [instalar PECL](http://pecl.php.net/).
```shell
    sudo pecl install mongo
```
Este es el código de ejemplo:
```php
<!-- PHP Mongo Docs: http://php.net/manual/en/class.mongodb.php -->
<!-- PHP Mongo Docs: http://php.net/manual/en/class.mongodb.php -->
<html>
<body>
<h1>Prueba de Compose</h1>
<?php
  try {
    // conectar con Compose suponiendo que la variable de entorno MONGODB_URL
    // contiene la serie de conexión
    $connection_url = getenv("MONGODB_URL");

     // crear el objeto de conexión de mongo
    $m = new MongoClient($connection_url, array("ssl" => true));

    // extraer el nombre de la BD de la vía de acceso de conexión
    $url = parse_url($connection_url);
    $db_name = preg_replace('/\/(.*)/', '$1', $url['path']);

    // utilizar la base de datos a la que nos hemos conectado
    $db = $m->selectDB($db_name);

    echo "<h2>Collections</h2>";
    echo "<ul>";

    // mostrar la lista de colecciones
    $cursor = $db->listCollections();
    $collection_name = "";
    foreach( $cursor as $doc ) {
      echo "<li>" .  $doc->getName() . "</li>";
      $collection_name = $doc->getName();
    }
    echo "</ul>";

    // mostrar la última colección
    if ( $collection_name != "" ) {
      $collection = $db->selectCollection($collection_name);
      echo "<h2>Documents in ${collection_name}</h2>";

      // mostrar únicamente los 5 primeros documentos
      $cursor = $collection->find();
      $cursor->limit(5);
      echo $cursor->count() . ' document(s) found. <br/>';
      foreach( $cursor as $doc ) {
        echo "<pre>";
        var_dump($doc);
        echo "</pre>";
      }
    }

    // desconectar del servidor
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

Esto debería funcionar perfectamente.

## C# / .NET

En esta guía se da por supuesto que ya ha descargado e instalado el controlador MongoDB C# para el proyecto. 

Si no lo ha hecho, encontrará el instalador en: [http://github.com/mongodb/mongo-csharp-driver/downloads](http://github.com/mongodb/mongo-csharp-driver/downloads).

Una vez finalizada la instalación, podrá hacer referencia a las DLL **MongoDriver** y **MongoBSON** dentro de Visual Studio.

### Creación de un proyecto nuevo

Para empezar, vamos a crear un nuevo proyecto en Visual Studio. En este ejemplo utilizaremos **ASP.NET MVC 2 Web Application**. *(Los ejemplos mostrados se pueden transferir a WebForms si es necesario.)*
![Creación de un proyecto en Visual Studio.](./images/create-project.jpg)
### Adición de referencias

Ahora tendremos que hacer referencia a las DLL **MongoDriver** y **MongoBSON** para poder conectar con nuestra base de datos MongoDB. El instalador del controlador C# ha añadido estas dos DLL al separador .NET de la ventana de referencia.

Con estas referencias añadidos, ahora podemos conectar y consultar nuestra base de datos de Compose con unas pocas líneas de código. ![Adición de una referencia de la lista de .NET.](./images/csharp-add-reference.jpg)
### Adición de información sobre la conexión

A continuación, vamos a configurar la serie de conexión con la información de nuestra base de datos añadiendo una nueva serie de conexión en el archivo web.config de nuestra aplicación. La entrada se debe parecer a esta:
```csharp
<connectionStrings>
  <add name="ApplicationServices"
    connectionString="data source=.\SQLEXPRESS;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|aspnetdb.mdf;User Instance=true"
    providerName="System.Data.SqlClient" />
  <add name="Compose"
    connectionString="your mongo uri"/>
</connectionStrings>
```
Por supuesto, debe sustituir **your mongo uri** por el URI real de Compose que se le ha suministrado en la interfaz web. Para obtenerlo, haga lo siguiente:

1. Inicie una sesión en [Compose](https://www.compose.io)
2. Pulse en el número de puerto de la base de datos con la que desea conectar
3. Copie o anote el URI que se proporciona.

Sólo se necesita el URI raíz, de modo que puede elegir la base de datos que desee.

### Configuración del modelo y del controlador

Ahora vamos a crear un modelo que contendrá nuestros documentos. La base de datos utilizada en este ejemplo es una colección de desarrolladores con información básica, por lo que crearemos un objeto de transformación de datos (DTO) para almacenar estas propiedades y devolver una lista genérica de estos DTO en nuestro modelo.

Cree un directorio DTO bajo el directorio Model para el archivo **DeveloperDTO.cs**.
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

Añada **DeveloperModel.cs** al directorio Models. *Asegúrese de incluir una cláusula using correspondiente al directorio DTO.*
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
Ahora vamos a crear nuestro DeveloperController. 

En este ejemplo lo vamos a llamar DeveloperController. Puede eliminar la marca del recuadro "Añadir métodos de acción para crear, actualizar y suprimir escenarios", ya que no vamos a tratar esta función en la guía de iniciación.

Abra el controlador recién creado y añada sus cláusulas using. Como mínimo, debería tener lo siguiente.

Observe la línea resaltada donde declaramos un objeto `MongoDatabase` privado y creamos una instancia en nuestro constructor.

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
Como puede observar, hemos dividido la llamada a la base de datos en un método separado, que se muestra debajo. Aquí utilizamos el método `MongoServer.Create` para iniciar una conexión con nuestro servidor Compose utilizando la serie de conexión de web.config. Realizamos una llamada a `GetDatabase` para obtener la instancia de `MongoDatabase`. 

En este caso, el nombre de la base de datos es **t2**. 

Para obtener más información sobre estos métodos, eche un vistazo a la [guía de aprendizaje del controlador CSharp](http://www.mongodb.org/display/DOCS/CSharp+Driver+Tutorial#CSharpDriverTutorial-Createmethod).
```csharp
static MongoDatabase retreive_mongodb_db()
{
  return MongoServer.Create(
    ConfigurationManager.ConnectionStrings["Compose"].ConnectionString)
    .GetDatabase("t2");
}
```
Añadiremos un método `Index` a `DeveloperController`

Ahora que nuestras conexiones están configuradas, vamos a añadir un método a **DeveloperController.cs** que ayudará a distribuir el contenido de la base de datos a la vista que vamos a crear.
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
Correcto.

Ahora tenemos una conexión con nuestra base de datos y un método para distribuir el contenido a la vista. Todo lo que tenemos que hacer es incorporar información a nuestro modelo y pasarlo a la vista.

### Visualización de datos

En este ejemplo, simplemente haremos una iteración sobre los desarrolladores y los mostraremos en la pantalla. Cree una carpeta `Developer` en el directorio Views y añada un nuevo archivo .aspx llamado `Index.aspx`. Añada el código siguiente y habrá terminado.
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
Para obtener más información, consulte la [documentación de la API del controlador MongoDB C#](http://api.mongodb.org/csharp/current/).
