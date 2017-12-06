---

copyright:
  years: 2016,2017
lastupdated: "2017-06-17"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.cloud_notm}}-Anwendung verbinden

Verbinden Sie eine {{site.data.keyword.cloud}}-Anwendung mit Ihrem Service mithilfe der Berechtigungsnachweise dieses Service, die bei dessen Bereitstellung erstellt werden. Die Beispielapp veranschaulicht die Verwendung von Node.js zur Herstellung einer Verbindung zu einem {{site.data.keyword.composeForMongoDB_full}}-Service mithilfe der bereitgestellten Berechtigungsnachweise und die Vorgehensweise zur Erstellung einer Datenbank sowie Lese- und Schreibvorgänge in dieser Datenbank. 

## Verbindung mithilfe der Beispielapp 'Hello World' herstellen

Die Beispielapp [compose-mongodb-helloworld-nodejs](https://github.com/IBM-Bluemix/compose-mongodb-helloworld-nodejs) veranschaulicht die Verwendung von Node.js zur Herstellung einer Verbindung zu einem {{site.data.keyword.composeForMongoDB}}-Service mithilfe der bereitgestellten Berechtigungsnachweise. Die Anwendung erstellt eine Datenbank, liest daraus und schreibt darin.

Laden Sie die Beispielapp herunter und befolgen Sie die Anweisungen in der Readme-Datei. Anschließend klicken Sie auf der Detailseite für die Anwendung in {{site.data.keyword.cloud_notm}} auf **App anzeigen**, um den Inhalt der Tabelle *examples* anzuzeigen.

## Verfügbare Berechtigungsnachweise

Feldname|Beschreibung
----------|-----------
`uri`|Der URI, der bei der Verbindungsherstellung zum Service verwendet werden soll. `uri` enthält das Schema (`mongodb:`), Administrator-Benutzernamen und Kennwort, den Hostnamen des Servers, die Nummer des Ports, zu dem die Verbindung hergestellt werden soll, den Datenbanknamen sowie `?ssl=true` zur Ermöglichung von SSL-Verbindungen.
`uri_cli`|Eine `mongo`-Shellbefehlszeile, die eine Verbindung zur Datenbankinstanz herstellt.
`ca_certificate_base64`|Ein selbst signiertes Zertifikat, mit dem bestätigt wird, dass eine App eine Verbindung zum geeigneten Server herstellt. Das Zertifikat ist base64-codiert. Der Schlüssel muss vor der Verwendung decodiert werden, wie in der Beispielapp gezeigt.
`deployment_id`|Eine interne ID für den Service entsprechend der Erstellung in Compose.
`db_type`|Der Datenbanktyp, der vom Service angeboten wird, in diesem Fall `mongodb`.
`name`|Der Name der Datenbankimplementierung.
{: caption="Tabelle 1. Compose for MongoDB - Berechtigungsnachweise" caption-side="top"}
