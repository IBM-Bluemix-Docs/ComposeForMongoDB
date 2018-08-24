---
copyright:
  years: 2016,2018
lastupdated: "2018-06-11"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Architettura 
{: #architecture}

## Replica

Un servizio {{site.data.keyword.composeForMongoDB_full}} viene fornito come una serie di repliche di tre nodi di dati, configurata come una singola partizione. Selezioni una regione in cui viene distribuito il servizio, i tre nodi di dati vengono distribuiti nelle zone di disponibilità della regione e i tuoi dati vengono quindi replicati tra tutti e tre. MongoDB esegue il push di tutte le operazioni di scrittura sul nodo di dati primario e ne esegue quindi la propagazione (tramite l'oplog) al nodo di dati secondario nella partizione. Se si verifica un malfunzionamento del nodo primario, il nodo secondario viene promosso al ruolo di nodo primario. Se uno qualsiasi dei membri di dati diventa irraggiungibile, la tua partizione MongoDB continua a funzionare normalmente.

## Crittografia del disco

Tutti i servizi {{site.data.keyword.composeForMongoDB}} hanno la crittografia dei dati inattivi. I tuoi dati si trovano su server che hanno la crittografia a livello di volume abilitata. 

Poiché {{site.data.keyword.composeForMongoDB}} è un servizio gestito, gli operatori di {{site.data.keyword.cloud}} Compose possono visualizzare i dati. Oltre alla crittografia del disco noi ti consigliamo, se stai archiviando informazioni personali, di crittografarle prima di archiviarle nel database o di servirti di estensioni o funzioni per abilitare la crittografia sul database stesso. Anche se questi metodi possono influire sull'usabilità o sulle prestazioni, è buona norma assicurarsi che le informazioni personali siano protette con la crittografia.

## Ridimensionamento automatico

Le risorse vengono ridimensionate automaticamente in base all'utilizzo di archiviazione disco totale dei database MongoDB. La memoria è basata su un rapporto di disco di cui viene eseguito il provisioning di 1:4. Queste risorse sono raccolte in bundle come unità.

Il ridimensionamento automatico è progettato per rispondere alle tendenze a medio e a lungo termine del tuo database. Il tuo servizio viene controllato ogni ora e, se sta esaurendo lo spazio su disco, delle ulteriori unità vengono assegnate alla distribuzione. 

Il ridimensionamento automatico non riduce le distribuzioni in cui l'utilizzo di disco/memoria si è ridotto. Le risorse di cui è stato eseguito il provisioning ai tuoi database rimarranno per le tue future esigenze oppure finché non riduci manualmente la tua distribuzione.
{: .tip}

## Il database di amministrazione

Se memorizzi i dati nel database di amministrazione, le prestazioni non saranno buone e rischi di perdere dei dati.

Come le altre installazioni MongoDB, {{site.data.keyword.composeForMongoDB}} ha un database di amministrazione. Il database di amministrazione contiene le informazioni su utenti e ruoli nel database, insieme alle diverse raccolte di traccia. Su una semplice installazione MongoDB, tali dati sono archiviati insieme agli altri dati nel database. 

Su {{site.data.keyword.composeForMongoDB}}, un'architettura più resiliente utilizza l'architettura a partizioni di MongoDB. Ciò comporta la condivisione dei dati di amministrazione tra le partizioni. Tale risultato si ottiene creando un altro cluster di database che contiene il database di amministrazione.

Questo database di amministrazione del cluster non è progettato per essere ridimensionato e le sue prestazioni si ridurranno rapidamente con qualsiasi quantità di dati archiviata al suo interno. È pertanto imperativo assicurarti che le applicazioni non scrivano dati nel database di amministrazione.
