---

copyright:
  years: 2016,2017
lastupdated: "2017-10-23"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Einführung in Compose for MongoDB
{: #getting-started-with-compose-for-mongodb}

{{site.data.keyword.composeForMongoDB_full}} verwendet die leistungsfähige Indexierung und Abfrage, die Aggregation und die breite Treiberunterstützung von MongoDB, die es zum gefragtesten JSON-Datenspeicher für viele Startup- und etablierte Unternehmen gemacht haben. {{site.data.keyword.composeForMongoDB}} bietet ein einfaches, selbstskalierendes Bereitstellungssystem. Es bietet hohe Verfügbarkeit und Redundanz, automatisierte und anforderungsbasierte dauerhaft aktive Sicherungsfunktionen, Überwachungstools, Integration in Warnsysteme, Ansichten für die Leistungsanalyse und vieles mehr, alles in einer übersichtlichen, leicht zu bedienenden Benutzerschnittstelle.
{:shortdesc}

**Hinweis:** Alle Compose-Serviceinstanzen, die vor dem 14. September 2016 bereitgestellt wurden und noch aktiv sind, können weiterhin verwendet werden und sind über [https://www.compose.com/](https://www.compose.com) zugänglich. Jede Compose-Serviceinstanz, die ab diesem Punkt bereitgestellt wird, ist direkt über Ihr {{site.data.keyword.cloud}}-Konto zugänglich und kann dort verwendet werden.

## Compose for MongoDB-Serviceinstanz erstellen

[Erstellen Sie eine {{site.data.keyword.composeForMongoDB}}-Instanz](https://console.ng.bluemix.net/catalog/services/compose-for-mongodb/).

Wenn Sie eine Instanz des Service erstellen, achten Sie darauf, sowohl einen Namen für den Service als auch einen Namen für den Berechtigungsnachweis auszuwählen. Lassen Sie den Service ungebunden; Sie können später eine Anwendung mit Ihrem Service verbinden, indem Sie die Berechtigungsnachweise verwenden, die bei der Bereitstellung des Service angegeben wurden.  Die verschiedenen Werte für die Berechtigungsnachweise sind im Abschnitt *Verfügbare Berechtigungsnachweise* aufgeführt.

Bei der Bereitstellung Ihrer {{site.data.keyword.composeForMongoDB}}-Instanz können Sie den Plan *Standard* oder *Enterprise* auswählen. Mit dem Plan *Enterprise* können Sie Ihre {{site.data.keyword.composeForMongoDB}}-Instanz in einem {{site.data.keyword.composeEnterprise}}-Cluster bereitstellen. {{site.data.keyword.composeEnterprise}} stellt die für die Konformität mit Enterprise erforderliche Sicherheit und Isolation bereit und stellt mithilfe eines dedizierten Netzbetriebs die Leistung der bereitgestellten Datenbanken sicher. Weitere Details finden Sie in der [Dokumentation zu Compose Enterprise](../ComposeEnterprise/index.html).

## Compose for MongoDB verwalten

Sie können Ihren Service über das Service-Dashboard verwalten. Hier finden Sie Informationen zu Ihrer {{site.data.keyword.cloud_notm}} Compose-Datenbank und dazu, wie Sie eine Verbindung zu ihr herstellen. Außerdem können Sie:
- Ihre Sicherungen verwalten
- Ihrem Service mehr Ressourcen zuordnen
- Whitelists verwenden, um den Zugriff auf Ihre Datenbank zu beschränken. Weitere Informationen finden Sie im Abschnitt [Einstellungen](./dashboard-settings.html).

## Verbindung zu Compose for MongoDB herstellen

Sie können mit den Berechtigungsnachweisen, die zusammen mit Ihrem Service erstellt werden, oder mithilfe der Verbindungszeichenfolgen und der Befehlszeile, die auf der Registerkarte *Übersicht* Ihres Service-Dashboards bereitgestellt werden, eine Verbindung zu dem Service herstellen.

## Verbindung von einer {{site.data.keyword.cloud_notm}}-Anwendung zu Compose for MongoDB herstellen

Um eine {{site.data.keyword.cloud_notm}}-Anwendung mit Ihrem Service zu verbinden, verwenden Sie die Berechtigungsnachweise, die zusammen mit dem Service erstellt wurden. Informationen zum Herstellen einer Verbindung von einer {{site.data.keyword.cloud_notm}}-Anwendung zu einem {{site.data.keyword.composeForMongoDB}}-Service finden Sie im Abschnitt [{{site.data.keyword.cloud_notm}}-Anwendung verbinden](./connecting-bluemix-app.html).

## Verbindung zu Compose for MongoDB außerhalb von {{site.data.keyword.cloud_notm}} herstellen

Wenn Sie außerhalb von {{site.data.keyword.cloud_notm}} eine Verbindung zu {{site.data.keyword.composeForMongoDB}} herstellen wollen, können Sie dazu die bereitgestellten Verbindungszeichenfolgen oder die Befehlszeile verwenden. Informationen dazu, wie sich eine Verbindung herstellen lässt, finden Sie im Abschnitt [Externe Anwendung verbinden](./connecting-external.html).
