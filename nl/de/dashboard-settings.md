---

Copyright:
  years: 2017,2018
lastupdated: "2017-11-20"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Einstellungen

Mit diesen Funktionen können Sie Ihren {{site.data.keyword.composeForMongoDB}}-Service besser an Ihre Bedürfnisse und Anforderungen anpassen.


## Upgradeversion

Wenn eine neue Version der Datenbank verfügbar ist, wird ein Dropdown-Menü angezeigt, in dem Sie auswählen können, auf welche Version Sie das Upgrade durchführen wollen. Andernfalls ist Ihr Service auf dem neuesten verfügbaren Stand und die Anzeige zeigt die aktuellen Versionsinformationen an.

![Versionsanzeige](./images/mongodb-version-show.png "Versionsanzeige")


## Ressourcen skalieren

Wenn Ihr Service zusätzlichen Speicher benötigt oder Sie den Speicherplatz verringern wollen, der Ihrem Service zugeordnet ist, können Sie dazu die Ressourcen skalieren.

1. Navigieren Sie zur Seite _Übersicht_ Ihres Service.
2. Klicken Sie in der Anzeige _Bereitstellungsdetails_ auf **Ressourcen skalieren**. Die Seite 'Ressourcen skalieren' wird geöffnet.

  ![Seite 'Ressourcen skalieren'](./images/mongodb-scale-show.png "Seite 'Ressourcen skalieren'")

3. Passen Sie den Schieberegler an, um den Speicher zu erhöhen oder zu verringern, der dem {{site.data.keyword.composeForMongoDB}}-Service zugeordnet ist. Verschieben Sie den Schieberegler nach links, um den Speicherplatz zu verringern, oder nach rechts, um ihn zu erhöhen.
4. Klicken Sie auf **Bereitstellung skalieren**, um die Neuskalierung auszulösen und zur Dashboardübersicht zurückzukehren. 
  
Nach dem Abschluss der Skalierung wird die Anzeige _Bereitstellungsdetails_ aktualisiert und zeigt die aktuelle Nutzung sowie den neuen Wert für den verfügbaren Speicher an.


## Whitelists

Wenn Sie den Zugriff auf Ihre Datenbank beschränken wollen, können Sie bestimmte IP-Adressen oder IP-Adressbereiche für Ihren Service auf eine Whitelist setzen. Wenn in der Whitelist keine IP-Adressen enthalten sind, wird die Whitelist inaktiviert und die Bereitstellung akzeptiert Verbindungen von jedem System im Internet.

![IP-Adressen auf Whitelist setzen](./images/mongodb-whitelist-show.png "Felder der Whitelist.")

### IP-Adressen
Das Feld *IP* kann eine einzelne vollständige IPv4- oder IPv6-Adresse mit oder ohne Netzmaske annehmen. Ohne Netzmaske müssen eingehende Verbindungen von genau dieser IP-Adresse kommen. 

Beachten Sie Folgendes: Zwar lässt der IP-Eintrag IPv6 zu, doch ist derzeit keine Compose-Bereitstellung für den IPv6-Netzbetrieb verfügbar, sodass keine Filterung dieser Adressen möglich ist.

### Netzmasken
Verwenden Sie eine Netzmaske, um eine Verbindung von einem angegebenen IP-Adressbereich zuzulassen. Bei Verwendung einer Netzmaske muss die IP-Adresse vollständig angegeben werden. Es muss also beispielsweise 192.168.1.0/24 statt 192.168.1/24 eingegeben werden.

### Beschreibung
Die *Beschreibung* kann ein beliebiger für den Benutzer hilfreicher Text zum Identifizieren des Eintrags in der Whitelist sein, beispielsweise ein Kundenname, eine Projekt-ID oder eine Personalnummer. Das Beschreibungsfeld ist erforderlich.

### Compose-Services
Einträge in die Whitelist werden automatisch zu den Compose-Servern hinzugefügt, damit sie eine Verbindung herstellen können.

### Entfernen
Klicken Sie, wenn Sie eine IP-Adresse oder Netzmaske aus der Whitelist entfernen wollen, auf den neben ihr angezeigten Eintrag *Entfernen*.
Wenn alle Einträge in der Whitelist entfernt werden, wird die Whitelist inaktiviert und die TCP-Zugriffsportale oder Mongos-Routerportale akzeptieren alle IP-Adressen.
