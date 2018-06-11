---

copyright:
  years: 2017,2018
lastupdated: "2017-06-17"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Note per l'utente per {{site.data.keyword.composeForMongoDB}}
{: #user-notes-compose-for-mongodb}

Queste note coprono diversi argomenti che sono stati sollevati da utenti {{site.data.keyword.composeForMongoDB}}.

## Il database di amministrazione

Se memorizzi i dati nel database di amministrazione, le prestazioni non saranno buone e rischi di perdere dei dati.

Come le altre installazioni MongoDB, {{site.data.keyword.composeForMongoDB}} ha un database di amministrazione. Il database di amministrazione contiene le informazioni su utenti e ruoli nel database, insieme alle diverse raccolte di traccia. Su una semplice installazione MongoDB, tali dati sono archiviati insieme agli altri dati nel database. 

Su {{site.data.keyword.composeForMongoDB}}, un'architettura più resiliente utilizza l'architettura a partizioni di MongoDB. Ciò comporta la condivisione dei dati di amministrazione tra le partizioni. Tale risultato si ottiene creando un altro cluster di database che contiene il database di amministrazione.

Questo database di amministrazione del cluster non è progettato per essere ridimensionato e le sue prestazioni si ridurranno rapidamente con qualsiasi quantità di dati archiviata al suo interno. È pertanto imperativo assicurarti che le applicazioni non scrivano dati nel database di amministrazione.

## Stringhe di connessione

Il failover al lato client è responsabilità del progettista dell'applicazione. A meno che tu non stia lavorando con un driver che gestisce completamente gli errori di failover, devi:

* Lavorare con un driver che accetta più endpoint nella sua configurazione della connessione.
* Assicurarti che le chiamate delle tue applicazioni per leggere o scrivere nel database reagiscano in modo appropriato agli errori. Le opzioni includono:
  + Registrazione dell'errore e riconnessione.
  + Riconnessione e nuovo tentativo dell'operazione che ha causato l'errore.
  + Accodamento dell'operazione non riuscita e pianificazione della riconnessione.






