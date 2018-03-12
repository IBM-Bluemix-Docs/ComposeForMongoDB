---

copyright:
  years: 2016,2018
lastupdated: "2018-01-03"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Preisstruktur
{: #pricing}

## Basiskonfiguration
Die Basiskonfiguration des {{site.data.keyword.composeForMongoDB_full}}-Service umfasst eine Replikatgruppe von drei Datenknoten in einer Einzelkonfiguration. Jeder Knoten hat 1 GB Speicher und 102 MB Hauptspeicher. Dies entspricht 1 Ressourceneinheit. Zum Service _gehören_ Replikation und Hochverfügbarkeit. In jedem Einzelpreis sind jeweils die Ressourcenkosten für alle drei Knoten _enthalten_.

Ferner sind zwei mongod-Router zur Verwaltung der Verbindungen sowie Hochverfügbarkeit mit jeweils 64 MB Hauptspeicher enthalten.

### Kosten
Die Basisservicekonfiguration hat einen Festpreis. Den Grundpreis in Ihrer Landeswährung finden Sie über die entsprechenden Katalogkacheln auf {{site.data.keyword.cloud_notm}}. Der Grundpreis in US-Dollar beträgt zum Beispiel $18/Monat.


## Erweiterungsoptionen
Wenn Sie zusätzlichen Speicher oder Hauptspeicher für Ihren Service benötigen, können Sie die zugeordneten Ressourcen in einem Verhältnis von 10:1 von Plattenspeicher zu Speichereinheit erhöhen. Durch Erhöhen des der Bereitstellung zugeordneten Plattenspeichers erhöht sich der zugeordnete RAM entsprechend. Eine {{site.data.keyword.composeForMongoDB}}-Einheit hat 1 GB Speicher und 102 MB Hauptspeicher. Im Einzelpreis pro Einheit sind die Kosten für die Erweiterung der Ressourcen auf allen drei Datenknoten _enthalten_. 

### Kosten
Jede zusätzliche Einheit (1 GB Speicher/102 MB Hauptspeicher) hat einen Einzelpreis, der auf der {{site.data.keyword.cloud_notm}}-Katalogkachel für den Service in Ihrer Landeswährung aufgelistet ist. In US-Dollar beträgt der Preis für jede zusätzliche Einheit $18. Mit zunehmender _Gesamtgröße_ Ihrer {{site.data.keyword.composeForMongoDB}}-Services verringert sich der Preis pro Einheit, wie aus der folgenden Tabelle zur gestaffelten Preisstruktur hervorgeht.

### Gestaffelte Preisstruktur
Anzahl der Einheiten|Einzelpreis
----------|-----------
1 - 9 Einheiten|Basiseinzelpreis -- $18,00 USD/Einheit
10 - 24 Einheiten|10% Ermäßigung -- $16,20 USD/Einheit
25 - 49 Einheiten|20% Ermäßigung -- $14,40 USD/USD/Einheit
50 - 99 Einheiten|30% Ermäßigung -- $12,60 USD/Einheit
100 - 499 Einheiten|40% Ermäßigung -- $10,80 USD/Einheit
500 - 999 Einheiten|50% Ermäßigung -- $9,00 USD/Einheit
1.000 - 4.999 Einheiten|60% Ermäßigung -- $7,20 USD/Einheit
5.000+ Einheiten|70% Ermäßigung -- $5,40 USD/GB
{: caption="Tabelle 1. Gestaffelte Preisstruktur von {{site.data.keyword.composeForMongoDB}}" caption-side="top"}
