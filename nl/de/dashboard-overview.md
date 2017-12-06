---

Copyright:
  Years: 2017
lastupdated: "2017-10-23"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Übersicht über den Service

Auf der Seite _Übersicht_ finden Sie Informationen zu Ihrer {{site.data.keyword.cloud}} Compose-Datenbank. Die Übersicht enthält die zentralen Identifikationsinformationen sowie die aktuelle Ressourcennutzung. Außerdem finden Sie einen Abschnitt für Verbindungszeichenfolgen, die Sie mit Tools verwenden können, und Angaben zum Herstellen von Verbindungen zu Ihrer Datenbank mithilfe von Tools.

## Bereitstellungsdetails

Die Anzeige _Bereitstellungsdetails_ enthält Details zu Ihrem Service.

![Bereitstellungsdetails](./images/mongodb-deployment-details.png "Ansicht der Anzeige 'Bereitstellungsdetails'")

### Typ

Der Datenbanktyp, der vom Service angeboten wird, und die Datenbankversion, die Ihr Service verwendet.

### Name

Eine interne ID für den Service.

### Nutzung

Die Größe Ihrer Datenbank und der von Ihrem Serviceplan bereitgestellte Speicherplatz.


## Verbindungszeichenfolgen

Die verfügbaren Verbindungszeichenfolgen für Ihren Service befinden sich in einer Gruppe von Registerkarten in der Anzeige _Verbindungszeichenfolgen_. Informationen dazu, wie sich mithilfe der Verbindungszeichenfolgen eine Verbindung zu Ihrem Service herstellen lässt, finden Sie im Abschnitt [Externe Anwendung verbinden](./connecting-external.html).

### HTTPS

Die **HTTPS**-Verbindungszeichenfolge kann von bestimmten Clientbibliotheken verwendet werden und enthält alle Informationen, die andere Bibliotheken zum Herstellen einer Verbindung benötigen. 

### Verbindungszeichenfolge

Sie können Ihre Verbindungszeichenfolge direkt in einer Anwendung verwenden, um eine Verbindung zu {{site.data.keyword.composeForMongoDB}} herzustellen oder sie als Umgebungsvariable festzulegen. Führen Sie dazu Folgendes in der Shell aus:

```
export MONGODB_URL="<CONNECTION STRING>"
```

### Befehlszeile

Die **Befehlszeile** ist ein vorformatierter Befehl, der `mongo` mit den korrekten Parametern aufruft. Um ihn verwenden zu können, müssen mongo-Tools auf dem lokalen System installiert sein. Informationen dazu, wie Sie dazu vorgehen müssen, finden Sie im Abschnitt [Externe Anwendung verbinden](./connecting-external.html).

### SSL-Zertifikat

Ihr Compose-{{site.data.keyword.cloud_notm}}-Service stellt Ihnen ein SSL-Zertifikat bereit, mit dem Sie eine Verbindung zu Ihrer Datenbank herstellen können.
