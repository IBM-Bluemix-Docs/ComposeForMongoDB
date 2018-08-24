---
copyright:
  years: 2016,2018
lastupdated: "2018-06-08"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Verbindungskonfiguration
{: #connection-configuration}

Database-Verbindungen in Clustern mit MongoDB-Shards werden von mongos-Routern verarbeitet. {{site.data.keyword.composeForMongoDB}}-Services werden mit zwei dieser Router mit jeweils 64 MB Hauptspeicher bereitgestellt. In die Mongo-Router wurde HAProxy eingefügt, wodurch sich nun eine Möglichkeit für sichere TLS/SSL-Verbindungen ergibt. Durch die zwei Portale bleibt für Anwendungen die Konnektivität auch dann erhalten, wenn eines der Portale nicht mehr erreichbar ist. Nahezu alle MongoDB-Treiber können mehrere Verbindungspunkte verarbeiten, aber Funktionsübernahme auf der Clientseite fällt in die Verantwortung der Anwendungsentwickler. Solange Sie nicht mit einem Treiber arbeiten, der die Funktionsübernahmefehler vollständig behandelt, sollten Sie Folgendes beachten:
* Arbeiten Sie mit einem Treiber, der mehrere Endpunkte in seiner Verbindungskonfiguration akzeptiert.
* Stellen Sie sicher, dass Ihre Anwendungsaufrufe zum Schreiben in oder Lesen aus der Datenbank angemessen auf Fehler reagieren. Dies schließt folgende Optionen ein:
  + Protokollierung des Fehlers und Wiederherstellen der Verbindung.
  + Wiederherstellen der Verbindung und Wiederholen der Operation, die den Fehler verursacht hat.
  + Steuerung der Warteschlange der fehlgeschlagenen Operation und Planung der Verbindungswiederholung.

## Verschlüsselung in Transit

Alle HAProxy-Portale von {{site.data.keyword.composeForMongoDB}} sind für TLS/SSL aktiviert und unterstützen TLS Version 1.2. Bei den Zertifikaten für den Service handelt es sich um selbst signierte Zertifikate. Möglicherweise müssen Sie eine Kopie des Zertifikats für Ihren Service lokal speichern und die Position dieser lokalen Kopie in Ihrem Anwendungstreiber angeben, um eine sichere Verbindung herstellen zu können.

## Grenzwerte für die Anzahl von Verbindungen

Für die beiden mongos-Router gilt ein Grenzwert von 5.000 Verbindungen pro mongos. Das Überschreiten des Verbindungsgrenzwerts hat zur Folge, dass Ihr Service nicht mehr reagiert. Über die Funktion Ihres Treibers für Verbindungspooling können Sie Verbindungen von ihrer Anwendung aus verwalten.

## Proxy-Zeitlimits

Die mongos-Router verwalten den Lebenszyklus von Verbindungen zwischen dem Server und dem Client. Die Zeitlimitwerte sind hier als Orientierung für Treiberautoren und all diejenigen aufgeführt, die sich für die maschinennahe Aktivität von {{site.data.keyword.composeForMongoDB}}-Datenbankverbindungen interessieren.

Einstellung | Wert | Gültigkeit
----------|-----------|-----------
client | 5 m | Wenn vom Client erwartet wird, Daten zu bestätigen oder zu senden.
connect | 4 s | Wenn eine Verbindung zwischen dem Proxy und dem Server hergestellt wird.
server | 5 m | Wenn vom Server erwartet wird, Daten zu bestätigen oder zu senden.
tunnel | 1 Tag | Wenn eine bidirektionale Verbindung zwischen einem Client und einem Server hergestellt wurde und die Verbindung in beiden Richtungen inaktiv bleibt.
client-fin | 1 h | Wenn vom Client erwartet wird, Daten zu bestätigen oder zu senden, während eine Verbindungsrichtung bereits beendet wurde.
check | 5 s | Als sekundäre Zeitlimitüberprüfung, wenn eine Verbindung zwischen dem Proxy und dem Server hergestellt wird.
{: caption="Tabelle 1. Zeitlimits für mongos-Router" caption-side="top"}
