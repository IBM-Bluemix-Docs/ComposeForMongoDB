---

copyright:
  years: 2016,2018
lastupdated: "2017-06-17"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Connessione a un'applicazione {{site.data.keyword.cloud_notm}}

Per collegare un'applicazione {{site.data.keyword.cloud}} al tuo servizio, utilizza le credenziali create quando è stato eseguito il provisioning del servizio. 'applicazione di esempio illustra come utilizzare Node.js per il collegamento a un servizio {{site.data.keyword.composeForMongoDB_full}} utilizzando le credenziali fornite e come creare un database e come leggere e scrivere in esso.

## Collegamento utilizzando l'applicazione di esempio 'Hello World'

L'applicazione di esempio [compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs) illustra come utilizzare Node.js per il collegamento a un servizio {{site.data.keyword.composeForMongoDB}} utilizzando le credenziali fornite. L'applicazione crea, legge da, e scrive su un database.

Scarica l'applicazione di esempio e segui le istruzioni nel file readme. In seguito, nella pagina dei dettagli della tua applicazione in {{site.data.keyword.cloud_notm}}, fai clic su **Visualizza applicazione** per visualizzare il contenuto della tabella *esempi*.

## Credenziali disponibili

Nome campo|Descrizione
----------|-----------
`uri`|L'URI da utilizzare durante il collegamento al servizio. `uri` include lo schema (`mongodb:`), il nome utente e la password amministratore, il nome host del server, il numero di porta a cui collegarsi, il nome del database e `?ssl=true` per abilitare le connessioni SSL.
`uri_cli`|Una riga di comando shell `mongo` che esegue il collegamento all'istanza del database.
`ca_certificate_base64`|Un certificato auto-firmato che viene utilizzato per confermare che un'applicazione sia collegata al server corretto. Il certificato si basa su codifica base64. Devi decodificare la chiave prima di utilizzarla, come mostrato nell'applicazione di esempio.
`deployment_id`|Un identificativo interno per il servizio come creato in Compose.
`db_type`|Il tipo di database offerto dal servizio: in questo caso, `mongodb`.
`name`|Il nome di distribuzione del database.
{: caption="Tabella 1. Credenziali di Compose for MongoDB" caption-side="top"}
