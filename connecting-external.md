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

# Connecting an external application
{: #connecting-external-app}

There are two ways of connecting an external application to {{site.data.keyword.composeForMongoDB}}:

- A **Connection String** can be used by some client libraries and contains all the information needed for other libraries to connect.

- **Command Line** is a preformatted command which will invoke `mongo` with the correct parameters.

You'll find both on the *Overview* page of your {{site.data.keyword.composeForPostgreSQL}} service.

For in depth application and driver support, check out the appropriate documentation and communities for your specific language and the driver that your application is using. 

* [Go](#go--golang-mongodb-and-compose)
* [NodeJS Native](#nodejs--native)
* [Mongoose + Node](#mongoose-node-and-compose)
* [R](#r-mongodb-and-compose)
* [Ruby](#ruby-mongodb-and-compose)
* [Python](#python-mongodb-and-compose)
* [PHP](#php-mongodb-and-compose)
* [C# and .NET](#c--net-mongodb-and-compose)

If you're looking for languages that you do not see above, try the [MongoDB.org Driver List](http://www.mongodb.org/display/DOCS/Drivers).

## Go / Golang

As in the other examples, this document assumes an environmental variable called `MONGODB_URL` with your connection string.  To set this variable, execute the following in the shell:

```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```

The mgo driver is the standard mongo driver for go, and can be installed by:

```shell
go get gopkg.in/mgo.v2
```

The mgo documentation, which is available at [http://labix.org/mgo](http://labix.org/mgo), covers most typical connections but as Compose MongoDB has SSL enabled, there are some extra steps to create and pass the TLS configuration to the mgo Dial function.

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
The line that removes "?ssl=true" from the URI (19) is needed as mgo will currently error if given the standard parameter for SSL/TLS connections. To connect and verify the SSL Public Certificate of the server is essentially the same, but needs some additional steps to load the certificate, in this case from a file "servercert.crt":

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

**Quick note:** In this example, we are assuming that your Compose connection string is set in an environment variable `MONGODB_URL`, like this:
```javascript
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name?ssl=true"
```
This code uses the [node-mongodb-native](https://github.com/christkv/node-mongodb-native) driver, though in production you may want something a little less... *nesty*. Like all good Node packages, you can get it via [NPM](http://npmjs.org/).
```shell
npm install mongodb
```
### Connecting with Node.js (Javascript)

Here we connect, just enabling the SSL/TLS encryption without validating the server:
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

If we want to use the server's SSL public certificate (stored in a file "servercert.crt" in this example) then we need to read it and include it in the options, while flipping sslValidate to true like this:

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
If you prefer a more ES6 flavor to your examples try:
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
### Connecting with Node.js (Coffeescript)
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
That should do the trick!

## Mongoose and Node

**Quick note:** In this example, we are assuming that your Compose connection string is set in an environment variable `MONGODB_URL`, like this:
```shell
var MONGODB_URL="mongodb://user:pass@server.compose.io:port_name/db_name"
```
This code uses the [mongoose](http://mongoosejs.com/) driver. Like all good Node packages, you can get it via [NPM](http://npmjs.org/).
```shell
npm install mongoose
```
### Connecting with Mongoose (Javascript)

The basic technique is the same as the Node.js/direct examples above. Create an options map, add in the requires SSL parameters and then pass that options map with the URL for MongoDB to the `mongoose.connect()` method. In the below example, we connect with a certificate and, again, list the available collections:
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

**Quick note:** In this example, we are assuming that your Compose connection string is set in an environment variable `MONGODB_URL`, like this:
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
If you are using Ruby (along with frameworks like Ruby on Rails, Sinatra, etc.), you can start out by installing the `mongo` (2.x) gem. It should go without saying, but you will need [RubyGems](http://rubygems.org). If you are using a pre-1.9 Ruby, you'll need to add `require 'rubygems'` at the start of the examples.

 Without using a certificate, the code is simply:
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
The Logger line mutes the driver's debug messages (there's a lot of them). Comment it out if you want to see more.

To connect with a certificate, the code is similar, but more option settings are needed when connecting:
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
Note the `servercert.crt` file name being passed twice to denote the certificate is also its own authority.

## Python

**Quick note:** In this example, we are assuming that your Compose connection string is set in an environment variable `MONGODB_URL`, like this:
```shell
MONGODB_URL="mongodb://user:pass@server.compose.io/database_name"
```
If you are using Python, you can start out by [installing PyMongo](http://api.mongodb.org/python/current/installation.html).
```python
import os
import pymongo
import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_cert_reqs=ssl.CERT_NONE)
db = client.get_default_database()
print db.collection_names()

```
To use the server's SSL certificate, it's simply a matter of adding it as a parameter like so:
```python
import os
import pymongo
import ssl

MONGODB_URL = os.environ.get('MONGODB_URL')
client = pymongo.MongoClient(MONGODB_URL,ssl_ca_certs="./servercert.crt")
db = client.get_default_database()
print db.collection_names()
```
And then, you should be on your way.

## PHP, MongoDB, and Compose

**Quick note:** In this example, we are assuming that your Compose connection string is set in an environment variable `MONGODB_URL`, like this:
```shell
export MONGODB_URL="mongodb://user:pass@server.compose.io/db_name"
```
This originally began as a [Gist](https://gist.github.com/coderoshi) from Larry Hitchon of AppFog. We made some improvements and made it Compose-centric. It's a pretty [straightforward driver](http://php.net/manual/en/class.mongodb.php). If you don't have PECL, you should [install PECL](http://pecl.php.net/).
```shell
    sudo pecl install mongo
```
And then, to the code sample:
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

That should get you well on your way.

## C# / .NET

This guide assumes that you have already downloaded and installed the MongoDB C# driver for your project. 

If you have not done so, you can find the installer at: [http://github.com/mongodb/mongo-csharp-driver/downloads](http://github.com/mongodb/mongo-csharp-driver/downloads).

Once installation is complete, you will be able to reference the **MongoDriver** and **MongoBSON** DLLs within Visual Studio.

### Create a New Project

To begin, letʼs create a new project in Visual Studio. I chose to use **ASP.NET MVC 2 Web Application** in this example. *(The examples given can be easily ported to WebForms if necessary.)*
![Creating a project in Visual Studio.](./images/create-project.jpg)
### Add References

Now we will need to reference the **MongoDriver** and **MongoBSON** DLLs so that we can connect to our MongoDB database. The C# driver installer added these two DLLs to the .NET tab of your reference window.

With these references added, we are now able to connect and query our Compose database with just a few lines of code.
![Adding a reference from the .NET list.](./images/csharp-add-reference.jpg)
### Add Connection Information

Next, let's set up our connection string with our database information by adding a new connection string in our application's web.config file. The entry should look similar to this:
```csharp
<connectionStrings>
  <add name="ApplicationServices"
    connectionString="data source=.\SQLEXPRESS;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|aspnetdb.mdf;User Instance=true"
    providerName="System.Data.SqlClient" />
  <add name="Compose"
    connectionString="your mongo uri"/>
</connectionStrings>
```
Of course, **your mongo uri** should be replaced with the actual Compose URI that is provided to you in the web interface. You can get this by doing the following:

1. Log into [Compose](https://www.compose.io)
2. Click on the port number of the database you want to connect to
3. Copy or note the URI that is provided.

You will just need the root URI, so you can choose any database you would like.

### Setting up your Model and Controller

Now, let's create a model to hold our documents. The database used in this example is a collection of developers with basic information, so we will create a Data Transformation Object (DTO) to store these properties and return a generic list of these DTOs in our model.

Create a DTO directory under the Model directory for our **DeveloperDTO.cs** file.
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

Add our **DeveloperModel.cs** to the Models directory. *Make sure you include a using clause for the DTO directory.*
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
Now let's create our DeveloperController. 

In this example, let's name it DeveloperController. You can uncheck the box for "Add action methods for Create, Update and Delete Scenarios" as we won't be covering this functionality in the Getting Started guide.

Open your newly created controller and add your using clauses. At a minimum, you should have the following.

Notice the highlighted line where we are declare a private `MongoDatabase` object and instantiating it in our constructor.

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
As you can see, I broke the database call into a separate method, which is listed below. Here, we utilize the `MongoServer.Create` method to initialize a connection to our Compose server using our connection string from our web.config. Now we can call `GetDatabase` to get our `MongoDatabase` instance. 

In this case, my database name is **t2**. 

For more information on these methods, take a look at the [CSharp Driver Tutorial](http://www.mongodb.org/display/DOCS/CSharp+Driver+Tutorial#CSharpDriverTutorial-Createmethod).
```csharp
static MongoDatabase retreive_mongodb_db()
{
  return MongoServer.Create(
    ConfigurationManager.ConnectionStrings["Compose"].ConnectionString)
    .GetDatabase("t2");
}
```
Add an `Index` method to our `DeveloperController`

Now that our connections are set up, let's add a method to our **DeveloperController.cs** that will assist in delivering the database content to our soon-to-be created view.
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
Success!

We now have a connection to our database and a method to deliver the content to our view. All we need to do now is hydrate our model and pass it to the view.

### Viewing Data

In this example, we simply iterate over our developers and print them to the screen. Create a `Developer` folder in the Views directory and add a new .aspx ﬁle called `Index.aspx`. Add the code below and youʼre all set.
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
For more information, please check out the [MongoDB C# Driver API documentation](http://api.mongodb.org/csharp/current/).