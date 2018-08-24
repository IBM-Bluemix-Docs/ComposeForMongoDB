---
copyright:
  years: 2016,2018
lastupdated: "2018-06-11"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Architektur 
{: #architecture}

## Replikation

Ein {{site.data.keyword.composeForMongoDB_full}}-Service wird in einem Replikatsatz mit drei Datenknoten bereitgestellt, der als ein einzelnes Shard (Teil) konfiguriert ist. Sie wählen eine Region aus, in der der Service bereitgestellt wird, und die drei Datenknoten werden über die Verfügbarkeitszonen der Region verteilt. Anschließend werden die Daten in allen drei Verfügbarkeitszonen repliziert. MongoDB überträgt alle Schreiboperationen per Push-Operation an den Primärdatenknoten und gibt sie anschließend über das Betriebsprotokoll ('oplog') an den sekundären Datenknoten auf dem Shard weiter. Wenn der Primärknoten ausfällt, wird der sekundäre Knoten zum primären Knoten hochgestuft. Auch wenn ein beliebiges Datenelement (Member) nicht mehr erreichbar sein sollte, setzt Ihr MongoDB-Shard seinen Betrieb ordnungsgemäß fort.

## Plattenverschlüsselung

Für alle {{site.data.keyword.composeForMongoDB}}-Services erfolgt die Verschlüsselung im Ruhezustand. Ihre Daten befinden sich auf Servern, auf denen die Verschlüsselung auf Datenträgerebene aktiviert ist. 

Da es sich bei {{site.data.keyword.composeForMongoDB}} um einen verwalteten Service handelt, sind Operatoren von Compose auf {{site.data.keyword.cloud}} in der Lage, Daten zu sehen. Wenn Sie persönliche Informationen speichern, wird zusätzlich zur Plattenverschlüsselung empfohlen, Informationen zu verschlüsseln, bevor sie in der Datenbank gespeichert werden, oder Erweiterungen bzw. Features zu verwenden, um die Verschlüsselung auf der Datenbank selbst zu ermöglichen. Diese Methoden können zwar den Bedienungskomfort oder die Leistung beeinträchtigen, doch es hat sich bewährt, den Schutz persönlicher Informationen mit Verschlüsselung sicherzustellen.

## Automatische Skalierung

Die Ressourcen werden basierend auf der Gesamtplattenspeicherbelegung der MongoDB-Datenbanken automatisch skaliert. Der Hauptspeicher basiert auf einem Faktor des bereitgestellten Plattenspeichers im Verhältnis von 1:4. Diese Ressourcen werden als Einheiten gebündelt.

Die automatische Skalierung ist so konzipiert, dass sie als Reaktion auf die kurz-bis mittelfristigen Trends Ihrer Datenbank erfolgt. Ihr Service wird in stündlichen Intervallen überprüft. Wenn der Plattenspeicherplatz für den Service knapp zu drohen wird, werden der Bereitstellung weitere Einheiten zugeordnet. 

Bei der automatischen Skalierung wird kein Scale-down für Bereitstellungen durchgeführt, bei denen sich die Platten-/Speicherbelegung verringert hat. Die für Ihre Datenbanken bereitgestellten Ressourcen bleiben für zukünftige Anforderungen weiterhin bestehen, sofern Sie Ihre Bereitstellung nicht per Scale-down manuell nach unten skalieren.
{: .tip}

## Die Administratordatenbank

Wenn Sie Daten in der Administratordatenbank speichern, werden diese nicht zufriedenstellend ausgeführt und es besteht das Risiko, diese Daten zu verlieren.

Wie bei anderen MongoDB-Installationen verfügt auch {{site.data.keyword.composeForMongoDB}} über eine Administratordatenbank. Die Administratordatenbank enthält Informationen zu Benutzern und Rollen innerhalb der Datenbank sowie verschiedene Protokollsammlungen. Bei einer einfachen MongoDB-Installation werden diese gemeinsam mit anderen Daten in der Datenbank gespeichert. 

Bei {{site.data.keyword.composeForMongoDB}} verwendet eine Architektur mit erhöhter Ausfallsicherheit die geteilte Architektur von MongoDB. Dies umfasst auch die gemeinsame Nutzung von Administrationsdaten zwischen Shards (Teilen). Dies wird durch die Erstellung eines weiteren Datenbankclusters erreicht, der die Administratordatenbank enthält.

Der Build dieser Cluster-Administratordatenbank ist nicht für die Skalierung vorgesehen und so kommt es beim Speichern von Daten zu einer schnellen Leistungsverschlechterung. Es ist daher zwingend erforderlich, dass Sie sicherstellen, dass Anwendungen keine Daten in die Administratordatenbank schreiben.
