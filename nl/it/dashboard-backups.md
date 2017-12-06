---

copyright:
  years: 2017
lastupdated: "2017-09-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Backup
{: #backups}

Puoi creare e scaricare i backup dalla pagina *Manage* del tuo dashboard del servizio {{site.data.keyword.composeForMongoDB_full}}. Sono disponibili sia i backup manuali che pianificati.

## Visualizzazione dei backup esistenti

I backup giornalieri del tuo database vengono automaticamente pianificati. Per visualizzare i tuoi backup esistenti, passa alla pagina *Manage* del tuo dashboard del servizio. 

![Backup](./images/mongodb-backups-show.png "Un elenco di backup disponibili")

Fai clic sulla riga corrispondente per espandere le opzioni per ogni backup disponibile.
  
![Opzioni backup](./images/mongodb-backups-options.png "Opzioni per il backup.") 

## Creazione di un backup su richiesta 

Come per i backup pianificati puoi creare un backup manualmente. Per creare un backup manuale, passa alla pagina *Manage* del tuo dashboard del servizio e fai clic su *Backup now*.

## Scaricamento di un backup

Per scaricare un backup, passa alla pagina *Manage* del tuo dashboard del servizio e fai clic su *Download* nella riga corrispondente del backup che desideri scaricare.

## Utilizzo di un backup con un database locale 

Puoi utilizzare il tuo backup {{site.data.keyword.composeForMongoDB}} per eseguire una copia locale del tuo database.

Per ottenere un ripristino remoto dei tuoi database e dati:

1. Scarica un backup dal tuo dashboard del servizio.
2. Installa MongoDB e gli strumenti MongoDB localmente.
3. Estrai i dati dal backup in una nuova directory.
4. Avvia un'istanza MongoDB locale con il comando `mongod --dbpath ./db` dove './db' è la directory che contiene i dati estratti.

## Ripristino di un backup

Per ripristinare un backup in una nuova istanza, segui le istruzioni per visualizzare i backup esistenti, quindi fai clic sulla riga corrispondente per espandere le opzioni del backup che desideri scaricare. Fai clic sul pulsante **Restore**. Viene visualizzato un messaggio per farti sapere che è stato avviato un ripristino. La nuova istanza del servizio sarà automaticamente denominata "mongodb-restore-[timestamp]" e visualizzata nel tuo dashboard dopo l'avvio del provisioning.
