---

Copyright:
  Years: 2017
lastupdated: "2017-06-17"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Conectando um aplicativo externo
{: #connecting-external-app}

Há duas maneiras de conectar um aplicativo externo ao {{site.data.keyword.composeForMongoDB_full}}:

- Uma **Sequência de conexões** pode ser usada por algumas bibliotecas do cliente e contém todas as informações necessárias para outras bibliotecas se conectarem.
- **Linha de comandos** é um comando pré-formatado que chamará `mongo` com os parâmetros corretos.

Você localizará ambos na página *Visão geral* de seu serviço {{site.data.keyword.composeForMongoDB}}.

Para suporte detalhado a aplicativos e drivers, verifique a documentação apropriada e as comunidades para a sua linguagem específica e o driver que o seu aplicativo está usando. 

Se você está procurando linguagens que não são cobertas aqui, tente a [Lista de drivers do MongoDB.org](http://www.mongodb.org/display/DOCS/Drivers).
{: tip}

## Go / Golang

Como nos outros exemplos, este documento assume uma variável ambiental chamada `MONGODB_URL` com sua sequência de conexões. Para configurar essa variável, execute o seguinte no shell:

```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```

O driver mgo é o driver mongo padrão para Go e pode ser driver instalado por:

```shell
go get gopkg.in/mgo.v2
```

A documentação do mgo, que está disponível em [http://labix.org/mgo](http://labix.org/mgo), cobre a maioria das conexões típicas, mas como o Compose MongoDB tem o SSL ativado, há algumas etapas extras para criar e passar a configuração de TLS para a função Dial do mgo.

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
	uri := os.Getenv("MONGODB_URL") 	if uri == "" {
		fmt.Println("No connection string provided - set MONGODB_URL") 		os.Exit(1) 	}
	uri = strings.TrimSuffix(uri, "?ssl=true")

	tlsConfig := &tls.Config{}
	tlsConfig.InsecureSkipVerify = true

	dialInfo, err := mgo.ParseURL(uri)

	if err != nil {
		fmt.Println("Failed to parse URI: ", err) 		os.Exit(1) 	}

	dialInfo.DialServer = func(addr *mgo.ServerAddr) (net.Conn, error) {
		conn, err := tls.Dial("tcp", addr.String(), tlsConfig) 		return conn, err 	} 	session, err := mgo.DialWithInfo(dialInfo) 	if err!= nil {
		fmt.Println("Failed to connect: ", err) 		os.Exit(1) 	}

	defer session.Close()

	dbnames, err := session.DB("").CollectionNames() 	if err!= nil {
		fmt.Println("Couldn't query for collections names: ", err) 		os.Exit(1) 	}

	fmt.Println(dbnames)

}

```
A linha que remove "?ssl=true" do URI (19) é necessária, pois o mgo tem atualmente um erro se considerado o parâmetro padrão para conexões SSL/TLS. Para conectar e verificar se o Certificado público SSL do servidor é essencialmente o mesmo, mas precisa de algumas etapas adicionais para carregar o certificado, neste caso de um arquivo "servercert.crt":

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
	uri := os.Getenv("MONGODB_URL") 	if uri == "" {
		fmt.Println("No connection string provided - set MONGODB_URL") 		os.Exit(1) 	}
	uri = strings.TrimSuffix(uri, "?ssl=true")

	roots := x509.NewCertPool()

	if ca, err := ioutil.ReadFile("servercert.crt"); err == nil {
		roots.AppendCertsFromPEM(ca)
	}

	tlsConfig := &tls.Config{}
	tlsConfig.RootCAs = roots

	dialInfo, err := mgo.ParseURL(uri)

	if err != nil {
		fmt.Println("Failed to parse URI: ", err) 		os.Exit(1) 	}

	dialInfo.DialServer = func(addr *mgo.ServerAddr) (net.Conn, error) {
		conn, err := tls.Dial("tcp", addr.String(), tlsConfig) 		return conn, err 	} 	session, err := mgo.DialWithInfo(dialInfo) 	if err!= nil {
		fmt.Println("Failed to connect: ", err) 		os.Exit(1) 	}

	defer session.Close()

	dbnames, err := session.DB("").CollectionNames() 	if err!= nil {
		fmt.Println("Couldn't query for collections names: ", err) 		os.Exit(1) 	}

	fmt.Println(dbnames)

}

```

## Node.js / Nativo

**Nota rápida:** neste exemplo, estamos assumindo que a sequência de conexões do Compose está configurada em uma variável de ambiente `MONGODB_URL`, como esta:
```javascript
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name?ssl=true"
```
Esse código usa o driver [node-mongodb-native](https://github.com/christkv/node-mongodb-native), embora na produção você talvez deseje algo um pouco menos... *aninhado*. Como todos os bons pacotes do Node, é possível obtê-lo por meio do [NPM](http://npmjs.org/).
```shell
npm install mongodb
```
### Conectando ao Node.js (Javascript)

Aqui nos conectamos, somente ativando a criptografia SSL/TLS sem validar o servidor:
```javascript
var MongoClient = require('mongodb').MongoClient;
var assert = require('assert');

var options = {
    mongos: {
        ssl: true,
        sslValidate: false,
    }
} MongoClient.connect(process.env.MONGODB_URL, options, function(err, db) {
    assert.equal(null, err); db.listCollections({}).toArray(function(err, collections) {
        assert.equal(null, err); collections.forEach(function(collection) {
            console.log(collection); }); db.close(); process.exit(0); })
});
```

Se desejamos usar o certificado público do servidor SSL (armazenado em um arquivo "servercert.crt" neste exemplo), precisamos ler e incluí-lo nas opções, enquanto invertemos sslValidate para true desta forma:

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
} MongoClient.connect(process.env.MONGODB_URL, options, function(err, db) {
    assert.equal(null, err); db.listCollections({}).toArray(function(err, collections) {
        assert.equal(null, err); collections.forEach(function(collection) {
            console.log(collection); }); db.close(); process.exit(0); })
});
```
Se você preferir um tipo mais ES6 para seus exemplos, tente:
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
            console.log(collection); }); db.close(); process.exit(0); })
});
```
### Conectando-se ao Node.js (Coffeescript)
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
Isso deve ser suficiente!

## Mongoose e Node

**Nota rápida:** neste exemplo, estamos assumindo que a sequência de conexões do Compose está configurada em uma variável de ambiente `MONGODB_URL`, como esta:
```shell
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name"
```
Esse código usa o driver [mongoose](http://mongoosejs.com/). Como todos os bons pacotes do Node, é possível obtê-lo por meio do [NPM](http://npmjs.org/).
```shell
npm install mongoose
```
### Conectando-se ao Mongoose (Javascript)

A técnica básica é a mesma que os exemplos Node.js/diretos acima. Crie um mapa de opções, inclua nos parâmetros SSL require e, em seguida, passe esse mapa de opções com a URL para MongoDB para o método `mongoose.connect()`. No exemplo abaixo, nós conectamos a um certificado e, novamente, listamos as coleções disponíveis:
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
        assert.equal(null, err); collections.forEach(function(collection) {
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

**Nota rápida:** neste exemplo, estamos assumindo que a sequência de conexões do Compose está configurada em uma variável de ambiente `MONGODB_URL`, como esta:
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
Se você estiver usando o Ruby (juntamente com estruturas como Ruby on Rails, Sinatra, etc.), será possível começar instalando o `mongo` (2.x) gem. Isso é evidente, mas você precisará do [RubyGems](http://rubygems.org). Se você estiver usando um Ruby pré-1.9, será necessário incluir `require 'rubygems'` no início dos exemplos.

 Sem usar um certificado, o código é simplesmente:
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
A linha Logger silencia as mensagens de depuração do driver (há muitas delas). Comente-a se você desejar ver mais.

Para se conectar a um certificado, o código é semelhante, mas mais configurações de opção são necessárias ao conectar-se:
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
Observe que o nome do arquivo `servercert.crt` está sendo passado duas vezes para denotar que o certificado é também a sua própria autoridade.

## Python

**Nota rápida:** neste exemplo, estamos assumindo que a sequência de conexões do Compose está configurada em uma variável de ambiente `MONGODB_URL`, como esta:
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/database_name"
```
Se você estiver usando Python, será possível começar [instalando o PyMongo](http://api.mongodb.org/python/current/installation.html).
```python
import os import pymongo import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_cert_reqs=ssl.CERT_NONE)
db = client.get_default_database()
print db.collection_names()

```
Para usar o certificado SSL do servidor, é simplesmente uma questão de incluí-lo como um parâmetro como este:
```python
import os import pymongo import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_ca_certs="./servercert.crt")
db = client.get_default_database()
print db.collection_names()
```
E, então, você estará a caminho.

## PHP, MongoDB e Compose

**Nota rápida:** neste exemplo, estamos assumindo que a sequência de conexões do Compose está configurada em uma variável de ambiente `MONGODB_URL`, como esta:
```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
Isso iniciou originalmente como um [Gist](https://gist.github.com/coderoshi) de Larry Hitchon de AppFog. Fizemos algumas melhorias e ele tornou-se centralizado no Compose. É um [driver bastante simples](http://php.net/manual/en/class.mongodb.php). Se você não tiver o PECL, será necessário [instalar o PECL](http://pecl.php.net/).
```shell
    sudo pecl install mongo
```
E, então, para a amostra de código:
```php
<!-- PHP Mongo Docs: http://php.net/manual/en/class.mongodb.php -->
<!-- PHP Mongo Docs: http://php.net/manual/en/class.mongodb.php -->
<html>
<body>
<h1>Teste do Compose</h1>
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
      echo "<h2>Documentos em ${collection_name}</h2>";

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

Isso deve fazer você progredir em seu caminho.

## C# / .NET

Este guia supõe que você já tenha transferido por download e instalado o driver MongoDB C# para seu projeto. 

Se você não tiver feito isso, será possível localizar o instalador em: [http://github.com/mongodb/mongo-csharp-driver/downloads](http://github.com/mongodb/mongo-csharp-driver/downloads).

Quando a instalação for concluída, você será capaz de referenciar os DLLs **MongoDriver** e **MongoBSON** no Visual Studio.

### Criar um novo projeto

Para começar, vamos criar um novo projeto no Visual Studio. Escolhi usar **aplicativo da web ASP.NET MVC 2** neste exemplo. *(Os exemplos fornecidos podem ser facilmente transportados para WebForms se necessário.)*
![Creating a project in Visual Studio.](./images/create-project.jpg)
### Incluir referências

Agora precisaremos referenciar os DLLs **MongoDriver** e **MongoBSON** para que possamos conectar ao nosso banco de dados MongoDB. O instalador do driver C# incluiu esses dois DLLs na guia .NET de sua janela de referência.

Com essas referências incluídas, agora somos capazes de conectar e consultar o nosso banco de dados do Compose com apenas algumas linhas de código.
![Adding a reference from the .NET list.](./images/csharp-add-reference.jpg)
### Incluir informações de conexão

Em seguida, vamos configurar nossa sequência de conexões com as informações do banco de dados, incluindo uma nova sequência de conexões no arquivo web.config do nosso aplicativo. A entrada deve ser semelhante a esta:
```csharp
<connectionStrings>
  <add name="ApplicationServices"
    connectionString="data source=.\SQLEXPRESS;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|aspnetdb.mdf;User Instance=true"
    providerName="System.Data.SqlClient" />
  <add name="Compose"
    connectionString="your mongo uri"/>
</connectionStrings>
```
Obviamente, **your mongo uri** deve ser substituído pelo URI real do Compose que é fornecido para você na interface da web. É possível conseguir isso executando o seguinte:

1. Efetue login no [Compose](https://www.compose.io)
2. Clique no número da porta do banco de dados ao qual você deseja se conectar
3. Copie ou anote o URI fornecido.

Você precisará apenas do URI raiz, portanto será possível escolher qualquer banco de dados que desejar.

### Configurando seu Modelo e Controlador

Agora, vamos criar um modelo para reter nossos documentos. O banco de dados usado neste exemplo é uma coleção de desenvolvedores com informações básicas, então vamos criar um Data Transformation Object (DTO) para armazenar essas propriedades e retornar uma lista genérica desses DTOs em nosso modelo.

Crie um diretório DTO sob o diretório Model para nosso arquivo **DeveloperDTO.cs**.
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

Inclua o **DeveloperModel.cs** no diretório Models. *Certifique-se de incluir uma cláusula using para o diretório DTO.*
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
Agora vamos criar o nosso DeveloperController.

Neste exemplo, vamos nomeá-lo DeveloperController. É possível desmarcar a caixa para "Incluir métodos de ação para Criar, Atualizar e Excluir Cenários", pois não cobriremos essa funcionalidade no guia Introdução.

Abra seu controlador recém-criado e inclua as cláusulas using. No mínimo, é necessário ter o seguinte.

Observe a linha destacada em que estamos declarando um objeto `MongoDatabase` privado e instanciando-a em nosso construtor.

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
            var model = new DeveloperModel(); var developers_collection = mongo_db.GetCollection("developers").FindAll().AsEnumerable();

            model.developers = (from developer in developers_collection select new DeveloperDTO {
                                               id = developer["_id"].AsObjectId, name = developer["name"].AsString, languages = developer["languages"].AsBsonArray.ToString(), company = developer["company"].AsString }).ToList();

            return View(model);
        }

        static MongoDatabase retreive_mongodb_db()
        {
            return MongoServer.Create( ConfigurationManager.ConnectionStrings["Compose"].ConnectionString)
                .GetDatabase("t2"); }
    }
}
```
Como é possível ver, a chamada de banco de dados foi dividida em um método separado, que é listado abaixo. Aqui, utilizamos o método `MongoServer.Create` para inicializar uma conexão com o servidor Compose usando nossa sequência de conexões do web.config. Agora podemos chamar `GetDatabase` para obter a instância `MongoDatabase`.

Nesse caso, meu nome do banco de dados é **t2**. 

Para obter mais informações sobre esses métodos, dê uma olhada no [Tutorial do driver CSharp](http://www.mongodb.org/display/DOCS/CSharp+Driver+Tutorial#CSharpDriverTutorial-Createmethod).
```csharp
static MongoDatabase retreive_mongodb_db()
{
  return MongoServer.Create( ConfigurationManager.ConnectionStrings["Compose"].ConnectionString)
    .GetDatabase("t2"); }
```
Inclua um método `Index` em nosso `DeveloperController`

Agora que nossas conexões estão configuradas, vamos incluir um método no **DeveloperController.cs** que ajudará a entregar o conteúdo do banco de dados para a nossa visualização a ser criada em breve.
```csharp
public ActionResult Index()
{
  var model = new DeveloperModel(); var developers_collection = mongo_db.GetCollection("developers").FindAll().AsEnumerable();

  model.developers = (from developer in developers_collection select new DeveloperDTO {
                          id = developer["_id"].AsObjectId, name = developer["name"].AsString, languages = developer["languages"].AsBsonArray.ToString(), company = developer["company"].AsString }).ToList();

  return View(model);
}
```
Êxito!

Agora temos uma conexão com o banco de dados e um método para entregar o conteúdo para a visualização. Agora basta hidratar o nosso modelo e passá-lo para a visualização.

### Visualizando dados

Neste exemplo, nós simplesmente iteramos sobre nossos desenvolvedores e os imprimimos na tela. Criar uma pasta `Developer` no diretório Views e inclua um novo arquivo .aspx chamado `Index.aspx`. Inclua o código abaixo e tudo estará pronto.
```asp
<%%@ Page Title="" Language="C#" MasterPageFile="~/Views/Shared/Site.Master" Inherits="System.Web.Mvc.ViewPage<mongodb_csharp.Models.DeveloperModel>" %>

<asp:Content ID="Content1" ContentPlaceHolderID="TitleContent" runat="server">
  Index
</asp:Content>

<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">

  <h2>Desenvolvedores</h2>

  <ul>
    <%% foreach(var developer in Model.developers) { %>
      <li><%%= developer.name %> works for <%%= developer.company %> and is proficient in <%%= developer.languages %></li>
    <%% } %>
  </ul>
</asp:Content>
```
Para obter mais informações, consulte a [Documentação da API do driver MongoDB C#](http://api.mongodb.org/csharp/current/).
