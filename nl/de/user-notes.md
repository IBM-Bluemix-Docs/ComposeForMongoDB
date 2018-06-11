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

# Benutzerhinweise für {{site.data.keyword.composeForMongoDB}}
{: #user-notes-compose-for-mongodb}

Diese Hinweise behandeln verschiedene Themen, die Benutzer von {{site.data.keyword.composeForMongoDB}} gemeldet haben. 

## Die Administratordatenbank

Wenn Sie Daten in der Administratordatenbank speichern, werden diese nicht zufriedenstellend ausgeführt und es besteht das Risiko, diese Daten zu verlieren. 

Wie bei anderen MongoDB-Installationen verfügt auch {{site.data.keyword.composeForMongoDB}} über eine Administratordatenbank. Die Administratordatenbank enthält Informationen zu Benutzern und Rollen innerhalb der Datenbank sowie verschiedene Protokollsammlungen. Bei einer einfachen MongoDB-Installation werden diese gemeinsam mit anderen Daten in der Datenbank gespeichert.  

Bei {{site.data.keyword.composeForMongoDB}} verwendet eine Architektur mit erhöhter Ausfallsicherheit die geteilte Architektur von MongoDB. Dies umfasst auch die gemeinsame Nutzung von Administrationsdaten zwischen Shards (Teilen). Dies wird durch die Erstellung eines weiteren Datenbankclusters erreicht, der die Administratordatenbank enthält. 

Der Build dieser Cluster-Administratordatenbank ist nicht für die Skalierung vorgesehen und so kommt es beim Speichern von Daten zu einer schnellen Leistungsverschlechterung. Es ist daher zwingend erforderlich, dass Sie sicherstellen, dass Anwendungen keine Daten in die Administratordatenbank schreiben. 

## Verbindungszeichenfolgen

Eine Funktionsübernahme auf der Clientseite fällt in die Verantwortung der Anwendungsentwickler. Solange Sie nicht mit einem Treiber arbeiten, der die Funktionsübernahmefehler vollständig behandelt, sollten Sie Folgendes beachten: 

* Arbeiten Sie mit einem Treiber, der mehrere Endpunkte in seiner Verbindungskonfiguration akzeptiert. 
* Stellen Sie sicher, dass Ihre Anwendungsaufrufe zum Schreiben in oder Lesen aus der Datenbank entsprechend auf Fehler reagieren. Dies schließt folgende Optionen ein: 
  + Protokollierung des Fehlers und Wiederherstellen der Verbindung.
  + Wiederherstellen der Verbindung und Wiederholen der Operation, die den Fehler verursacht hat. 
  + Steuerung der Warteschlange der fehlgeschlagenen Operation und Planung der Verbindungswiederholung. 






