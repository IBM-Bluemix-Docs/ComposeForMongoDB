---

Copyright:
  years: 2017,2018
lastupdated: "2018-05-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Panoramica sul servizio

La pagina _Overview_ ti mostra le informazioni sul tuo database {{site.data.keyword.cloud}} Compose. La panoramica include le informazioni di identificazione essenziali e l'utilizzo della risorsa corrente. Troverai inoltre una sezione per le stringhe di connessione che puoi utilizzare con gli strumenti o utilizzare gli strumenti per il collegamento al tuo database.

## Deployment Details

Il pannello _Deployment Details_ mostra i dettagli del tuo servizio.

![Deployment Details](./images/mongodb-deployment-details.png "Una vista del pannello dei dettagli della distribuzione")

### Type

Il tipo di database offerto dal servizio e la versione che il servizio utilizza. Se è disponibile una versione più recente del database, viene visualizzata una notifica, insieme a un link alla sezione [Aggiorna versione](/docs/services/ComposeForMongoDB/dashboard-settings.html#upgrade-version) del tuo dashboard del servizio.

### ID

Un identificativo interno per il servizio.

### Usage

La dimensione del tuo database e la quantità di memoria fornita dal tuo piano di servizio.

## Lavori correnti

L'effettuare delle modifiche amministrative al tuo servizio (come un ridimensionamento o l'esecuzione di un backup manuale) avvia un lavoro. Mentre un lavoro è in esecuzione, viene visualizzato il pannello _Current Jobs_ nella pagina _Overview_, che visualizza il nome del lavoro e una barra di avanzamento. Quando il lavoro è completo, non viene più visualizzato il pannello _Current Jobs_ nella pagina _Overview_.

## Stringhe di connessione

Puoi trovare le stringhe di connessione disponibili per il tuo servizio in una serie di schede nella pannello _Connection Strings_. Puoi trovare come utilizzare le stringhe di connessione al tuo servizio in [Connecting an external application](./connecting-external.html).

### HTTPS

La stringa di connessione **HTTPS** può essere utilizzata da alcune librerie client e contiene tutte le informazioni necessarie al collegamento di altre librerie.

### Stringa di connessione

Puoi utilizzare la tua stringa di connessione direttamente in un'applicazione per collegarti a {{site.data.keyword.composeForMongoDB}} o impostarla come una variabile di ambiente. Per far ciò, esegui quanto segue nella shell:

```
export MONGODB_URL="<CONNECTION STRING>"
```

### Riga di comando

La **Riga di comando** è un comando pre-formattato che richiamerà `mongo` con i parametri corretti. Per utilizzarla, dovrai aver installato gli strumenti mongo nel sistema locale. Puoi trovare ulteriori informazioni su come farlo in [Connessione a un'applicazione esterna](./connecting-external.html).

### Certificato SSL

Il tuo servizio Compose {{site.data.keyword.cloud_notm}} ti fornisce un certificato SSL che puoi utilizzare per collegarti al tuo database.


## API di gestione dell'istanza

Puoi gestire il tuo servizio {{site.data.keyword.composeForMongoDB}} tramite l'API {{site.data.keyword.cloud_notm}} Compose.

### Endpoint fondazione

L'endpoint fondazione è formato dalla regione in cui risiede il servizio e dall'ID dell'istanza del servizio. Sarà all'inizio di ogni endpoint.

### ID distribuzione

L'ID della distribuzione è necessario per la maggior parte delle chiamate e identifica l'istanza di distribuzione specifica.

### Riferimenti

Per ulteriore documentazione e i riferimenti sull'utilizzo dell'API {{site.data.keyword.cloud_notm}} Compose, in tutti i servizi {{site.data.keyword.cloud_notm}} Compose, leggi [The {{site.data.keyword.cloud_notm}} Compose API](https://www.compose.com/articles/the-ibm-cloud-compose-api/).
