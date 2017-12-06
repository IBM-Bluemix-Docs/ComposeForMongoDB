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

# Introduzione a Compose for MongoDB
{: #getting-started-with-compose-for-mongodb}

{{site.data.keyword.composeForMongoDB_full}} utilizza l'aggregazione e l'indicizzazione e l'esecuzione di query potenti e un ampio supporto driver di MongoDB che è stato l'inizio dell'archiviazione dati JSON per molte nuove imprese e aziende. {{site.data.keyword.composeForMongoDB}} offre un sistema di distribuzione con ridimensionamento automatico facile da utilizzare. Offre disponibilità e ridondanza, backup no-stop in locale e automatizzati, strumenti di monitoraggio, integrazione nei sistemi di avviso, prestazioni delle viste di analisi e altro ancora, tutto con un'interfaccia utente semplice e ordinata.
{:shortdesc}

**Nota:** tutte le istanze del servizio Compose di cui è stato eseguito il provisioning prima del 14 settembre 2016 che sono ancora attive e che possono ancora essere utilizzate a cui si può fare direttamente accesso da [https://www.compose.com/](https://www.compose.com). È possibile accedere direttamente a tutte le istanze del servizio Compose di cui è stato eseguito il provisioning da questo punto in avanti ed è possibile utilizzarle nel tuo account {{site.data.keyword.cloud}}.

## Creazione di un'istanza del servizio Compose per MongoDB

[Crea un'istanza {{site.data.keyword.composeForMongoDB}}](https://console.ng.bluemix.net/catalog/services/compose-for-mongodb/).

Quando crei un'istanza del servizio, assicurati di scegliere un nome per il tuo servizio e un nome per la credenziale. Lascia il servizio senza bind; puoi collegare un'applicazione al tuo servizio successivamente utilizzando le credenziali fornite quando è stato eseguito il provisioning del servizio.  I vari valori delle credenziali sono elencati nella sezione *Credenziali disponibili*.

Quando esegui il provisioning della tua istanza {{site.data.keyword.composeForMongoDB}} puoi scegliere i piani *Standard* o *Enterprise*. Con il piano *Enterprise*, puoi eseguire il provisioning della tua istanza {{site.data.keyword.composeForMongoDB}} in un cluster {{site.data.keyword.composeEnterprise}} disponibile. {{site.data.keyword.composeEnterprise}} fornisce la sicurezza e l'isolamento necessari per la conformità aziendale e utilizza la rete dedicata per garantire le prestazioni dei database distribuiti. Per ulteriori dettagli, consulta la [Documentazione aziendale Compose](../ComposeEnterprise/index.html).

## Gestione di Compose for MongoDB

Puoi gestire il tuo servizio dal dashboard del servizio. Qui puoi trovare le informazioni sul tuo database {{site.data.keyword.cloud_notm}} Compose e su come collegarti ad esso. Puoi anche:
- gestire i tuoi backup
- assegnare ulteriori risorse al tuo servizio
- utilizzare le whitelist per limitare l'accesso ai tuoi database 
Per ulteriori informazioni, consulta [Impostazioni](./dashboard-settings.html).

## Connessione a Compose for MongoDB

Puoi collegarti al tuo servizio utilizzando le credenziali create insieme al servizio o con le stringhe di connessione e la riga di comando forniti nella scheda *Overview* nel tuo dashboard del servizio.

## Connessione di un'applicazione {{site.data.keyword.cloud_notm}} a Compose for MongoDB

Per collegare un'applicazione {{site.data.keyword.cloud_notm}} al tuo servizio, utilizza le credenziali create insieme al servizio. Puoi trovare le informazioni su come collegare un'applicazione {{site.data.keyword.cloud_notm}} a un servizio {{site.data.keyword.composeForMongoDB}} in [Connessione a un'applicazione {{site.data.keyword.cloud_notm}}](./connecting-bluemix-app.html).

## Connessione a Compose for MongoDB dall'esterno di {{site.data.keyword.cloud_notm}}

Se desideri collegarti a {{site.data.keyword.composeForMongoDB}} dall'esterno di {{site.data.keyword.cloud_notm}}, puoi utilizzare la riga di comando o le stringhe di connessione fornite. Puoi trovare le informazioni su come collegarti in [Connessione a un'applicazione esterna](./connecting-external.html).
