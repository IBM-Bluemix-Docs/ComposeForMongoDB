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

# Configurazione della connessione
{: #connection-configuration}

Le connessioni al database nei cluster partizionati MongoDB sono gestiti dai router mongos. I servizi {{site.data.keyword.composeForMongoDB}} vengono forniti con due di questi router con 64MB di memoria l'uno. HAProxy è stato aggiunto nei router Mongo, fornendo un modo per proteggere le connessioni TLS/SSL. I due portali consentono alle applicazioni di mantenere la connettività se uno dei portali diventa irraggiungibile. Quasi tutti i driver MongoDB gestiscono più punti di connessione ma il failover sul lato client è responsabilità del progettista dell'applicazione. A meno che tu non stia lavorando con un driver che gestisce completamente gli errori di failover, devi:
* Lavorare con un driver che accetta più endpoint nella sua configurazione della connessione.
* Assicurarti che le chiamate delle tue applicazioni per leggere o scrivere nel database reagiscano in modo appropriato agli errori. Le opzioni includono:
  + Registrazione dell'errore e riconnessione.
  + Riconnessione e nuovo tentativo dell'operazione che ha causato l'errore.
  + Accodamento dell'operazione non riuscita e pianificazione della riconnessione.

## Crittografia in transito

Tutti i portali HAProxy {{site.data.keyword.composeForMongoDB}} hanno TLS/SSL abilitati e supportano TLS versione 1.2. I certificati per il servizio sono certificati autofirmati. È possibile che tu debba salvare una copia locale del certificato per il tuo servizio e fornirne la posizione al tuo driver delle applicazioni per stabilire una connessione protetta.

## Limiti di connessioni

I due router mongos hanno un limite di connessioni di 5000 connessioni per mongos. Se si supera il limite di connessioni, il tuo servizio smetterà di rispondere. Puoi gestire le connessioni dalla tua applicazione tramite la funzione di pooling di connessioni del tuo driver.

## Timeout proxy

I router mongos gestiscono il ciclo di vita delle connessioni tra il server e il client. Li descriviamo qui in dettaglio come guida di riferimento per gli scrittori di driver e per gli altri che hanno un interesse nell'attività di basso livello delle connessioni al database {{site.data.keyword.composeForMongoDB}}.

Impostazione | Valore | Si applica
----------|-----------|-----------
client | 5m | Quando si prevede che il client riconosca o invii i dati.
connect | 4s | Quando si sta stabilendo una connessione tra il proxy e il server.
server | 5m | Quando si prevede che il server riconosca o invii i dati.
tunnel | 1d | Quando viene stabilita una connessione bidirezionale tra un client e un server e la connessione rimane inattiva in entrambe le direzioni.
client-fin | 1h | Quando si prevede che il client riconosca o invii dati mentre una direzione è già stata arrestata.
check | 5s | Come un controllo di timeout secondario quando si sta stabilendo una connessione tra il proxy e il server.
{: caption="Tabella 1. Timeout router Mongos" caption-side="top"}
