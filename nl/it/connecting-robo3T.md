---

copyright:
  years: 2016,2017
lastupdated: "2017-08-03"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Connessione con Robo3T

Robo 3T (precedentemente Robomongo) è una GUI leggera e gratuita per MongoDB. Il suo obiettivo principale è di eseguire query, creare indici e visualizzare i documenti.

1. Installa [Robo3T](https://robomongo.org/).
2. Vai alla pagina _Overview_ del tuo dashboard del servizio. Tutte le informazioni necessarie al collegamento utilizzando Robo3T possono essere trovate in questa scheda.

  ![La pagina della panoramica](./images/service_overview.png)

3. Nel pannello _Connection Strings_, fai clic su **SSL Certificates** per aprire la scheda dei certificati SSL. Salva il certificato SSL del tuo servizio. Innanzitutto, copia il certificato SSL utilizzando il pulsante di copia nell'angolo in alto a destra del pannello del certificato SSL:

    ![Il pulsante di copia](./images/copy_icon.png)

  Successivamente, utilizzando il tuo editor di testo preferito, crea un nuovo file, incolla il contenuto del certificato SSL nel file e salvalo.

4. Apri Robo3T e passa alla scheda _Connection Settings_. Per compilare questi campi hai bisogno di un nome host e un numero di porta. Torna alla pagina _Overview_ del tuo dashboard del servizio, li troverai nella scheda della riga di comando, nel pannello _Connection Settings_.

5. Copia i valori nei campi dell'indirizzo nella scheda _Connection Settings_.

  ![Impostazioni connessione Robo3T](./images/Robo3T_connection.png "Il pannello di connessione a Robo3T")

  Lascia l'impostazione _Type_ come "Direct Connection"
  {: tip}

6. Apri la scheda _Authentication_. Per compilare questi campi hai bisogno di un nome utente e di una password. Torna alla pagina _Overview_ del tuo dashboard del servizio, li troverai nella scheda della riga di comando, nel pannello _Connection Settings_. 

7. Assicurati che la casella _Perform authentication_ sia selezionata e immetti i valori nome utente e password dalla tua stringa della riga di comando.

  ![Impostazioni autenticazione Robo3T](./images/Robo3T_auth.png "Il pannello di autenticazione Robo3T")

8. Apri la scheda _SSL_. Assicurati che la casella _Use SSL protocol_ sia selezionata e che _Authentication Method_ sia impostato su "Use CA Certificate".

  ![Impostazioni SSL Robo3T](./images/Robo3T_SSL.png "Il pannello SSL Robo3T")

9. Utilizza il campo _CA Certificate_ per immettere o ricercare l'ubicazione del file del certificato SSL che hai creato.

10. Fai clic su **Save** per terminare.

