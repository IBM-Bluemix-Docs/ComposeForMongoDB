---

copyright:
  years: 2016,2018
lastupdated: "2017-11-15"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Informationen zu {{site.data.keyword.composeForMongoDB}}
{: #about-compose-for-mongodb}

{{site.data.keyword.composeForMongoDB_full}} verwendet die leistungsfähige Indexierung und Abfrage, die Aggregation und die breite Treiberunterstützung von MongoDB, die es zum gefragtesten JSON-Datenspeicher für viele Startup- und etablierte Unternehmen gemacht haben. {{site.data.keyword.composeForMongoDB}} bietet ein einfaches, selbstskalierendes Bereitstellungssystem. Es bietet hohe Verfügbarkeit und Redundanz, automatisierte und anforderungsbasierte dauerhaft aktive Sicherungsfunktionen, Überwachungstools, Integration in Warnsysteme, Ansichten für die Leistungsanalyse und vieles mehr, alles in einer übersichtlichen, leicht zu bedienenden Benutzerschnittstelle.
{:shortdesc}

**Hinweis:** Alle Compose-Serviceinstanzen, die vor dem 14. September 2016 bereitgestellt wurden und noch aktiv sind, können weiterhin verwendet werden und sind über [https://www.compose.com/](https://www.compose.com) zugänglich. Jede Compose-Serviceinstanz, die ab diesem Punkt bereitgestellt wird, ist direkt über Ihr {{site.data.keyword.cloud}}-Konto zugänglich und kann dort verwendet werden.

## {{site.data.keyword.composeForMongoDB}}-Serviceinstanz erstellen

Sie können einen {{site.data.keyword.composeForMongoDB}}-Service über die [{{site.data.keyword.composeForMongoDB}}-Seite](https://console.{DomainName}/catalog/services/compose-for-mongodb/) im {{site.data.keyword.cloud_notm}}-Katalog erstellen.

Wählen Sie einen Servicenamen und eine Region, eine Organisation und einen Bereich zur Bereitstellung des Service aus. Im Feld **Datenbankversion auswählen** können Sie eine der verfügbaren Datenbankversionen auswählen.

Bei der Bereitstellung Ihrer {{site.data.keyword.composeForMongoDB}}-Instanz können Sie den Plan *Standard* oder *Enterprise* auswählen. Mit dem Plan *Enterprise* können Sie Ihre {{site.data.keyword.composeForMongoDB}}-Instanz in einem {{site.data.keyword.composeEnterprise}}-Cluster bereitstellen. {{site.data.keyword.composeEnterprise}} stellt die für die Konformität mit Enterprise erforderliche Sicherheit und Isolation bereit und stellt mithilfe eines dedizierten Netzbetriebs die Leistung der bereitgestellten Datenbanken sicher. Weitere Details finden Sie in der [Dokumentation zu Compose Enterprise](../ComposeEnterprise/index.html).

## {{site.data.keyword.composeForMongoDB}} verwalten

Sie können Ihren Service über das Service-Dashboard verwalten. Hier finden Sie Informationen zu Ihrer {{site.data.keyword.cloud_notm}} Compose-Datenbank und dazu, wie Sie eine Verbindung zu ihr herstellen. Außerdem können Sie:
- Ihre Sicherungen verwalten
- Ihrem Service mehr Ressourcen zuordnen
- Whitelists verwenden, um den Zugriff auf Ihre Datenbank zu beschränken. 
Weitere Informationen finden Sie im Abschnitt [Einstellungen](./dashboard-settings.html).

## Verbindung zu {{site.data.keyword.composeForMongoDB}} herstellen

Sie können mit den Berechtigungsnachweisen, die zusammen mit Ihrem Service erstellt werden, oder mithilfe der Verbindungszeichenfolgen und der Befehlszeile, die auf der Registerkarte *Übersicht* Ihres Service-Dashboards bereitgestellt werden, eine Verbindung zu dem Service herstellen.

## Verbindung von einer {{site.data.keyword.cloud_notm}}-Anwendung zu {{site.data.keyword.composeForMongoDB}} herstellen

Um eine {{site.data.keyword.cloud_notm}}-Anwendung mit Ihrem Service zu verbinden, verwenden Sie die Berechtigungsnachweise, die zusammen mit dem Service erstellt wurden. Informationen zum Herstellen einer Verbindung von einer {{site.data.keyword.cloud_notm}}-Anwendung zu einem {{site.data.keyword.composeForMongoDB}}-Service finden Sie im Abschnitt [{{site.data.keyword.cloud_notm}}-Anwendung verbinden](./connecting-bluemix-app.html).

## Verbindung zu {{site.data.keyword.composeForMongoDB}} außerhalb von {{site.data.keyword.cloud_notm}} herstellen

Wenn Sie außerhalb von {{site.data.keyword.cloud_notm}} eine Verbindung zu {{site.data.keyword.composeForMongoDB}} herstellen wollen, können Sie dazu die bereitgestellten Verbindungszeichenfolgen oder die Befehlszeile verwenden. Informationen dazu, wie sich eine Verbindung herstellen lässt, finden Sie im Abschnitt [Externe Anwendung verbinden](./connecting-external.html).

## Eine Webanwendung mithilfe des MEAN-Stack erstellen

Sie können {{site.data.keyword.composeForMongoDB}} als Teil eines MEAN-Stack zum Erstellen einer Webanwendung verwenden. Führen Sie die Schritte im Lernprogramm [Modern Web Application using MEAN stack](../../tutorials/mean-stack.html) aus, um eine Webanwendung zu erstellen, die sich aus MongoDB-Datenbank, Express-Web-Framework, Angular-Front-End-Framework und Node.js-Laufzeit zusammensetzt.
