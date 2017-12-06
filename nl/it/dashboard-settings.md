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
{:tip: .tip}

# Impostazioni

Queste funzioni ti consentono di adattare il tuo servizio {{site.data.keyword.composeForMongoDB}} per soddisfare in modo migliore i tuoi bisogni e requisiti.


## Aggiorna versione

Se è disponibile una nuova versione del database, viene visualizzato un menu a discesa, che ti consente di selezionare per quale versione desideri eseguire l'aggiornamento. Altrimenti, il tuo servizio è all'ultima versione disponibile e il pannello visualizza le informazioni sulla versione corrente.

![Il pannello della versione](./images/mongodb-version-show.png "Il pannello della versione")


## Ridimensionamento delle risorse

Se il tuo servizio necessita ulteriore memoria o desideri ridurre la quantità di memoria assegnata al tuo servizio, puoi eseguire queste operazioni per ridimensionare le risorse.

1. Passa alla pagina _Overview_ del tuo servizio.
2. Nel pannello _Deployment Details_, fai clic su **Scale Resources**. Viene aperta la pagina di ridimensionamento delle risorse.

  ![La pagina di ridimensionamento delle risorse](./images/mongodb-scale-show.png "La pagina di ridimensionamento delle risorse")

3. Modifica lo slider per aumentare o diminuire la memoria assegnata al servizio {{site.data.keyword.composeForMongoDB}}. Sposta lo slider a sinistra per ridurre la quantità di memoria o a destra per incrementarla.
4. Fai clic su **Scale Deployment** per attivare il ridimensionamento e ritorna alla panoramica del dashboard. Viene visualizzato un messaggio 'Scaling initiated' all'inizio della pagina per farti sapere che il ridimensionamento è in corso e anche il pannello _Deployment Details_ visualizza il ridimensionamento in corso.

  ![Ridimensionamento in corso](./images/scaling-in-progress.png "Il pannello dei dettagli, che mostra che il ridimensionamento del database è in corso")
  
  Quando il ridimensionamento è completo il pannello _Deployment Details_ si aggiorna per visualizzare l'utilizzo corrente e il nuovo valore per la memoria disponibile.


## Whitelist

Se desideri limitare l'accesso ai tuoi database, puoi inserirei in una whitelist indirizzi IP specifici o intervalli di indirizzi IP del tuo servizio. Quando non sono presenti indirizzi IP nella whitelist, essa è disabilitata e la distribuzione accetterà le connessioni da tutti i sistemi in internet.

![IP della whitelist](./images/mongodb-whitelist-show.png "I campi della whitelist.")

### Indirizzi IP
Il campo *IP* può avere solo un indirizzo IPv4 o IPv6 con o senza una netmask. Senza una netmask, le connessioni in entrata devono provenire esattamente da tale indirizzo IP. 

Tieni presente che anche se la voce IP consente IPv6, nessuna distribuzione Compose è al momento disponibile per la rete IPv6, per cui questi indirizzi non possono essere filtrati.

### Netmask
Per consentire una connessione da un intervallo specificato di indirizzi IP, utilizza una netmask. L'indirizzo IP deve essere completamente specificato quando utilizzi una netmask. Questo significa che devi immettere, ad esempio, 192.168.1.0/24 invece di 192.168.1/24.

### Descrizione
*Description* può essere un qualsiasi testo significativo per l'utente per identificare la voce della whitelist - ad esempio, un nome del cliente, un identificativo del progetto o un numero del dipendente. Il campo descrizione è obbligatorio.

### Servizi Compose
Le voci della whitelist vengono automaticamente aggiunte ai server di Compose per consentire loro di collegarsi.

### Rimozione
Per rimuovere un indirizzo IP o una netmask dalla whitelist, fai clic sulla voce *Remove* visualizzata al loro fianco.
Quando tutte le voci nella whitelist sono state rimosse, sarà disabilitata e tutti gli indirizzi IP saranno accettati dai portali di accesso TCP o del router Mongos.
