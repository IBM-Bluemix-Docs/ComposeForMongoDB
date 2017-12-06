---

Copyright:
  Years: 2017
lastupdated: "2017-10-23"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Panoramica sul servizio

La pagina _Overview_ ti mostra le informazioni sul tuo database {{site.data.keyword.cloud}} Compose. La panoramica include le informazioni di identificazione essenziali e l'utilizzo della risorsa corrente. Troverai inoltre una sezione per le stringhe di connessione che puoi utilizzare con gli strumenti o utilizzare gli strumenti per il collegamento al tuo database.

## Dettagli della distribuzione

Il pannello _Deployment Details_ mostra i dettagli del tuo servizio.

![Dettagli della distribuzione](./images/mongodb-deployment-details.png "Una vista del pannello dei dettagli della distribuzione")

### Tipo

Il tipo di database offerto dal servizio e la versione che il servizio utilizza.

### Nome

Un identificativo interno per il servizio.

### Utilizzo

La dimensione del tuo database e la quantità di memoria fornita dal tuo piano di servizio.


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
