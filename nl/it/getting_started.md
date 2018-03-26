---

copyright:
  years: 2016,2018
lastupdated: "2018-02-16"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}


# Esercitazione introduttiva
Questa esercitazione utilizza l'applicazione di esempio [compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs) che illustra come utilizzare Node.js per il collegamento a un servizio {{site.data.keyword.composeForMongoDB_full}} utilizzando le credenziali fornite. L'applicazione crea, legge da, e scrive su un database utilizzando i dati forniti tramite l'interfaccia web dell'applicazione.
{: shortdesc}

## Prima di iniziare

Assicurati di avere un account [{{site.data.keyword.cloud_notm}}][ibm_cloud_signup_url]{:new_window}.

Dovrai anche installare [Node.js](https://nodejs.org/) e [Git](https://git-scm.com/downloads).

## Passo 1: crea un'istanza del servizio {{site.data.keyword.composeForMongoDB}}
{: #create-service}

Puoi creare un servizio {{site.data.keyword.composeForMongoDB}} dalla pagina [{{site.data.keyword.composeForMongoDB}}](https://console.{DomainName}/catalog/services/compose-for-mongodb/) nel catalogo {{site.data.keyword.cloud_notm}}.

Scegli un nome del servizio, una regione, un'organizzazione e uno spazio in cui eseguire il provisioning del servizio e per il campo **Select a database version**, scegli _Latest Preferred Version_.

Successivamente, scegli un piano dei prezzi per il tuo servizio. Puoi scegliere i piani *Standard* o *Enterprise*. Con il piano *Enterprise*, puoi eseguire il provisioning della tua istanza {{site.data.keyword.composeForMongoDB}} in un cluster {{site.data.keyword.composeEnterprise}} disponibile. {{site.data.keyword.composeEnterprise}} fornisce la sicurezza e l'isolamento necessari per la conformità aziendale e utilizza la rete dedicata per garantire le prestazioni dei database distribuiti. Consulta la documentazione di [Compose Enterprise](../ComposeEnterprise/index.html) per ulteriori dettagli.

## Passo 2: clona l'applicazione di esempio Hello World da Github

Clona l'applicazione Hello World nel tuo ambiente locale dal tuo terminale utilizzando il seguente comando:

```
git clone https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs.git
```

## Passo 3: installa le dipendenze dell'applicazione

Utilizza npm per installare le dipendenze.

1. Dal tuo terminale, modifica la directory in cui si trova l'applicazione di esempio.
  
  ```
  cd compose-mongodb-helloworld-nodejs
  ```

2. Installa le dipendenze elencate nel file `package.json`.
  
  ```
  npm install
  ```

## Passo 4: crea le credenziali del servizio

Prima di passare l'applicazione a {{site.data.keyword.cloud_notm}} puoi eseguirla localmente per verificare la connessione alla tua istanza del servizio {{site.data.keyword.composeForMongoDB}}. Per la connessione al servizio dovrai creare una serie di credenziali del servizio.

1. Dal tuo dashboard {{site.data.keyword.cloud_notm}}, apri la tua istanza del servizio {{site.data.keyword.composeForMongoDB}}.
2. Seleziona _Service Credentials_ dal menu principale per aprire la vista delle credenziali del servizio.
3. Fai clic su **New Credential**.
4. Scegli un nome per le tue credenziali e fai clic su **Add**.
5. Le tue nuove credenziali sono ora elencate. Fai clic su **View credentials** nella riga corrispondente della tabella per visualizzare le credenziali e fai clic sull'icona **Copy** per copiare le tue credenziali.
6. In un editor di tua scelta, crea un nuovo file con quanto segue, inserendo le tue credenziali come mostrato di seguito:

  ```
  {
    "services": {
      "compose-for-mongodb": [
        {
          "credentials": INSERISCI QUI LE TUE CREDENZIALI
        }
      ]
    }
  }
  ```
6. Salva il file come `vcap-local.json` nella directory in cui si trova l'applicazione di esempio.

Per evitare di esporre accidentalmente le tue credenziali quando passi un'applicazione a Github o a {{site.data.keyword.cloud_notm}} devi essere sicuro che il file che contiene le tue credenziali sia elencato in ignora file rilevanti. Se apri `.cfignore` e `.gitignore` nella tua directory dell'applicazione vedrai che `vcap-local.json` è elencato in entrambi, per cui non sarà incluso nei file che vengono caricati quando passi l'applicazione a Github o a {{site.data.keyword.cloud_notm}}.
{: .tip}

## Passo 5: esegui l'applicazione localmente

```
npm start
```

L'applicazione è ora in esecuzione all'indirizzo [http://localhost:8080](http://localhost:8080). Puoi aggiungere parole e definizioni al tuo database {{site.data.keyword.composeForMongoDB}}. Quando arresti e riavvii l'applicazione, tutte le parole che hai già aggiunto vengono visualizzate quando aggiorni la pagina.

Il passo successivo è di collegare la tua applicazione alla tua istanza del servizio e distribuirla a {{site.data.keyword.cloud_notm}}.

## Passo 6: scarica e installa lo strumento CLI {{site.data.keyword.cloud_notm}}

Lo strumento CLI {{site.data.keyword.cloud_notm}} è quello che utilizzerai per comunicare con {{site.data.keyword.cloud_notm}} dal tuo terminale o dalla riga di comando. Per i dettagli, consulta [Scarica e installa la CLI {{site.data.keyword.cloud_notm}}](https://console.{DomainName}/docs/cli/reference/bluemix_cli/download_cli.html).

## Passo 7: connessione a {{site.data.keyword.cloud_notm}}

1. Collegati a {{site.data.keyword.cloud_notm}} nello strumento della riga di comando e segui le istruzioni per accedere.

  ```
  bx login
  ```

  Se hai un ID utente federato, utilizza il comando `bx login --sso` per accedere con il tuo ID SSO (Single Sign On). Consulta [Accesso con un ID federato](https://console.{DomainName}/docs/cli/login_federated_id.html#federated_id) per ulteriori informazioni.
  {: .tip}

2. Assicurati di puntare all'organizzazione e allo spazio {{site.data.keyword.cloud_notm}} corretti.

  ```
  bx target --cf
  ```

  Scegli dalle opzioni fornite, utilizzando gli stessi valori che hai utilizzato quando hai creato il servizio.

## Passo 8: aggiorna il file manifest dell'applicazione
{: #update-manifest}

{{site.data.keyword.cloud_notm}} utilizza un file manifest - `manifest.yml` per associare un'applicazione a un servizio. Segui questa procedura per creare il tuo file manifest.

1. In un editor, apri un nuovo file e aggiungi quanto segue:

  ```
  ---
  applications:
  - name:    compose-mongodb-helloworld-nodejs
    host:    compose-mongodb-helloworld-nodejs
    memory:  128M
    services:
      - my-compose-for-mongodb-service
  ```

2. Modifica il valore `host` con qualcosa di univoco. L'host che scegli determinerà il dominio secondario dell'URL della tua applicazione:  `<host>.mybluemix.net`.
3. Modifica il valore `name`. Il valore che scegli sarà il nome dell'applicazione come viene visualizzato nel tuo dashboard {{site.data.keyword.cloud_notm}}.
4. Aggiorna il valore `services` in modo che corrisponda al nome del servizio che hai creato in [Crea un'istanza del servizio {{site.data.keyword.composeForMongoDB}}](#create-service). 

## Passo 9: trasmetti la tua applicazione a {{site.data.keyword.cloud_notm}}.

Quando trasmetti l'applicazione sarà automaticamente associata al servizio specificato nel file manifest.

```
bx cf push
```

## Passo 10: controlla che l'applicazione sia collegata al tuo servizio {{site.data.keyword.composeForMongoDB}}

1. Passa al tuo dashboard del servizio {{site.data.keyword.composeForMongoDB}}
2. Seleziona _Connections_ dal menu del dashboard. La tua applicazione dovrebbe essere elencata in _Connected Applications_.

Se la tua applicazione non è elencata, ripeti i passi da 7 a 8, assicurandoti di aver immesso i dettagli corretti in [manifest.yml](#update-manifest).

## Passo 11: utilizza l'applicazione

Ora, quando visiti `<host>.mybluemix.net/` potrai visualizzare i contenuti della tua raccolta {{site.data.keyword.composeForMongoDB}}. Man mano che aggiungi le parole e le loro definizioni, vengono aggiunte al database e visualizzate. Se arresti e riavvii l'applicazione vedrai elencate tutte le parole e le definizioni che hai già aggiunto.


## Passi successivi

Per ulteriori informazioni su come funziona l'applicazione di esempio [compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs), puoi leggere il file readme dell'applicazione o i commenti del codice in `server.js`, che forniscono alcune informazioni sulle funzioni dell'applicazione.

Per iniziare ad esplorare il tuo servizio {{site.data.keyword.composeForMongoDB}}, consulta i seguenti argomenti sul dashboard del servizio:

- [Panoramica sul dashboard](./dashboard-overview.html)
- [Backup](./dashboard-backups.html)
- [Impostazioni  ](./dashboard-settings.html)

Per informazioni sulle credenziali che hai creato per l'applicazione per il collegamento al tuo servizio, consulta [Credenziali disponibili](./connecting-bluemix-app.html#available-credentials).

[ibm_cloud_signup_url]: https://ibm.biz/compose-for-mongodb-signup
