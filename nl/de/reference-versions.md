---
copyright:
  years: 2016,2018
lastupdated: "2018-06-05"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Versionen

Bereitstellbare Versionen| Bevorzugte Version
----------|-----------
3.2.10, 3.2.11, 3.2.18, 3.4.10 | 3.2.18
{: caption="Tabelle 1. Versionen von MongoDB" caption-side="top"}

## Bevorzugte Version

Die bevorzugte Version ist normalerweise die neueste Version der MongoDB-Datenbank, die für {{site.data.keyword.composeForMongoDB}} verfügbar ist. Standardmäßig wird diese Version in der Dropdown-Liste des Versionsselektors auf der Katalogseite angezeigt. Außerdem wird die bevorzugte Version automatisch von der API bereitgestellt, sofern der Aufruf keine Versionsangabe beinhaltet.

### Protokoll für neue bevorzugte Versionen

Wenn eine neue Version zur Verfügung gestellt wird, so wird ihre Veröffentlichung angekündigt und sie wird zur Bereitstellung verfügbar gemacht. Im Anschluss an die Veröffentlichung gibt es ein Zeitfenster mit einer Dauer von ungefähr 7 Tagen, in dem die neueste Version zwar bereits verfügbar ist, aber noch nicht als bevorzugte Version gilt. Dieses Zeitfenster ermöglicht Ihnen, die Version bereitzustellen und zu testen, während die aktuelle Version weiterhin zur Verfügung steht. Am Ende des 7-tägigen Zeitfensters wird die neue Version als bevorzugte Version definiert oder es wird ein neues Datum für diese Änderung angekündigt.

Die Liste der für die Bereitstellung verfügbaren Versionen finden Sie auf der {{site.data.keyword.composeForMongoDB}} [-Katalogseite](https://console.{DomainName}/catalog/services/compose-for-mongodb).

Eine aktuelle Liste der für Ihren {{site.data.keyword.composeForMongoDB}}-Service verfügbaren Versionen können Sie über den Endpunkt [GET /2016-07/deployments/:id/versions](https://apidocs.compose.com/v1.0/reference#2016-07-get-deployments-versions) abrufen.
