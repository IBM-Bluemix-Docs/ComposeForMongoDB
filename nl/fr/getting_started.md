---

copyright:
  years: 2016,2018
lastupdated: "2018-05-29"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}


# Tutoriel d'initiation
Ce tutoriel s'appuie sur le modèle d'application [compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs) pour montrer comment utiliser Node.js afin d'établir une connexion à un service {{site.data.keyword.composeForMongoDB_full}}. L'application crée une base de données, y lit et y écrit en utilisant les données fournies via l'interface Web de l'application.
{: shortdesc}

## Avant de commencer

Assurez-vous que vous avez un [{{site.data.keyword.cloud_notm}} compte][ibm_cloud_signup_url]{:new_window}.

Vous devez également installer [Node.js](https://nodejs.org/) et [Git](https://git-scm.com/downloads).

## Etape 1 : Création d'une instance de service {{site.data.keyword.composeForMongoDB}}
{: #create-service}

Vous pouvez créer un service {{site.data.keyword.composeForMongoDB}} à partir de la [page {{site.data.keyword.composeForMongoDB}}](https://console.{DomainName}/catalog/services/compose-for-mongodb/) du catalogue {{site.data.keyword.cloud_notm}}.

Choisissez un nom de service, une région, une organisation et un espace dans lesquels mettre le service à disposition, et sélectionnez _Latest Preferred Version_ dans la zone **Select a database version**.

Ensuite, sélectionnez un plan de tarification pour votre service. Vous avez le choix entre le plan *Standard* et le plan *Entreprise*. Avec le plan *Entreprise*, vous pouvez mettre votre instance {{site.data.keyword.composeForMongoDB}} à disposition dans un cluster {{site.data.keyword.composeEnterprise}} disponible. {{site.data.keyword.composeEnterprise}} offre la sécurité et l'isolement requis par les normes de conformité des entreprises et utilise un réseau dédié pour garantir les performances des bases de données déployées. Pour plus d'informations, voir la documentation [{{site.data.keyword.composeEnterprise}}](/docs/services/ComposeEnterprise/index.html).

Cliquez sur **Créer** pour mettre votre service à disposition. La mise à disposition peut prendre un certain temps. Vous pouvez vérifier sa progression en accédant à la vue _Gérer_ du service.

Vous ne pouvez pas connecter une application au service tant que la mise à disposition n'est pas terminée.
{: .tip}

## Etape 2 : Clonage du modèle d'application Hello World depuis Github

Depuis votre terminal, clonez l'application Hello World dans votre environnement local à l'aide de la commande suivante :

```
git clone https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs.git
```
{: codeblock}

## Etape 3 : Installation des dépendances de l'application

Pour installez des dépendances, utilisez npm.

1. Depuis votre terminal, accédez au répertoire qui contient le modèle d'application.
  
  ```
  cd compose-mongodb-helloworld-nodejs
  ```
  {: codeblock}

2. Installez les dépendances répertoriées dans le fichier `package.json`.
  
  ```
  npm install
  ```

## Etape 4 : Téléchargement et installation de l'outil d'interface de ligne de commande {{site.data.keyword.cloud_notm}}

L'outil d'interface de ligne de commande {{site.data.keyword.cloud_notm}} vous permet de communiquer avec {{site.data.keyword.cloud_notm}} à partir de votre terminal ou de la ligne de commande. Pour plus d'informations, voir [Téléchargement et installation de l'interface de ligne de commande {{site.data.keyword.cloud_notm}}](https://console.{DomainName}/docs/cli/reference/bluemix_cli/download_cli.html).

## Etape 5 : Connexion à {{site.data.keyword.cloud_notm}}

1. Connectez-vous à {{site.data.keyword.cloud_notm}} dans l'outil de ligne de commande ete suivez les invites de connexion.

  ```
  ibmcloud login
  ```

  Si vous disposez d'un ID utilisateur fédéré, utilisez la commande `ibmcloud login --sso` pour vous connecter avec votre ID de connexion unique (Single Sign On). Voir [Connexion à l'aide d'un ID fédéré](https://console.{DomainName}/docs/cli/login_federated_id.html#federated_id) pour en savoir plus.
  {: .tip}

2. Assurez-vous de cibler l'organisation et l'espace {{site.data.keyword.cloud_notm}} appropriés.

  ```
  ibmcloud target --cf
  ```

  Faites votre choix parmi les options fournies, en utilisant les mêmes valeurs que celles utilisées lors de la création du service.

## Etape 6 : Mise à jour du fichier manifeste de l'application
{: #update-manifest}

{{site.data.keyword.cloud_notm}} utilise un fichier manifeste, `manifest.yml`, pour associer une application à un service. Pour créer votre fichier manifeste, procédez comme suit :

1. Dans un éditeur, ouvrez un nouveau fichier et ajoutez-y les éléments suivants :

  ```
  ---
  applications:
  - name:    compose-mongodb-helloworld-nodejs
    host:    compose-mongodb-helloworld-nodejs
    memory:  128M
    services:
      - my-compose-for-mongodb-service
  ```

2. Remplacez la valeur `host` par une valeur unique. L'hôte que vous choisissez détermine le sous-domaine de l'URL de l'application :  `<host>.mybluemix.net`.
3. Modifiez la valeur de `name`. La valeur que vous choisissez sera le nom de l'application tel qu'il apparaît dans votre tableau de bord {{site.data.keyword.cloud_notm}}.
4. Mettez à jour la valeur `services` de sorte qu'elle corresponde au nom du service créé à l'étape [Création d'une instance de service {{site.data.keyword.composeForMongoDB}}](#create-service). 

## Etape 7 : Envoi de l'application à {{site.data.keyword.cloud_notm}} par commande push.

Cette étape échouera si le service n'a pas terminé la mise à disposition de l'étape 1. Vous pouvez vérifier sa progression en accédant à la vue _Gérer_ du service.
{: .tip}

Lorsque vous envoyez l'application par commande push, elle est automatiquement liée au service indiqué dans le fichier manifeste.

```
ibmcloud cf push
```

## Etape 8 : Vérification de la connexion de l'application à votre service {{site.data.keyword.composeForMongoDB}}

1. Accédez au tableau de bord du service {{site.data.keyword.composeForMongoDB}}.
2. Sélectionnez _Connexions_ dans le menu du tableau de bord. Votre application doit être répertoriée sous _Applications connectées_.

Si votre application n'est pas répertoriée, répétez les étapes 7 et 8, en vérifiant que le fichier [manifest.yml](#update-manifest) contient les informations appropriées.

## Etape 9 : Utilisation de l'application

Désormais, lorsque vous accédez à `<host>.mybluemix.net/` vous pouvez visualiser le contenu de votre collection {{site.data.keyword.composeForMongoDB}}. Les mots et définitions ajoutés sont ajoutés dans la base de données et affichés à mesure de leur ajout. Si vous arrêtez puis redémarrez l'application, tous les mots et définitions que vous avez déjà ajoutés sont maintenant répertoriés.

## Exécution de l'application en local

Au lieu d'envoyer l'application dans {{site.data.keyword.cloud_notm}} par commande push, vous pouvez l'exécuter en local afin de tester la connexion à votre instance de service {{site.data.keyword.composeForMongoDB}}. Pour vous connecter au service vous devrez créer un jeu de données d'identification du service.

1. A partir du tableau de bord {{site.data.keyword.cloud_notm}}, ouvrez votre instance de service {{site.data.keyword.composeForMongoDB}}.
2. Sélectionnez _Données d'identification pour le service_ dans le menu principal pour ouvrir la vue Données d'identification pour le service.
3. Cliquez sur **Nouvelles données d'identification**.
4. Choisissez un nom pour vos données d'identification, puis cliquez sur **Ajouter**.
5. Vos nouvelles données d'identification figurent maintenant sur la liste. Cliquez sur **Afficher les données d'identification** sur la ligne correspondante du tableau pour afficher les données d'identification, puis cliquez sur l'icône **Copier** pour copier vos données d'identification.
6. Dans l'éditeur de votre choix, créez un fichier en y insérant vos données d'identification comme suit :

  ```
  {
    "services": {
      "compose-for-mongodb": [
        {
          "credentials": INSERT YOUR CREDENTIALS HERE
        }
      ]
    }
  }
  ```
  {: codeblock}
7. Sauvegardez le fichier sous l'intitulé `vcap-local.json` dans le répertoire qui contient le modèle d'application.

Pour éviter d'exposer accidentellement vos données d'identification lors de l'envoi d'un modèle d'application par commande push à Github ou {{site.data.keyword.cloud_notm}}, vous devez vous assurer que le fichier qui contient ces données d'identification est répertorié dans le fichier des éléments à ignorer approprié. Si vous ouvrez `.cfignore` et `.gitignore` dans le répertoire de votre application, vous constaterez que `vcap-local.json` figure dans les deux, de sorte qu'il ne sera pas inclus dans les fichiers téléchargés lorsque vous envoyez l'application par commande push à Github ou à {{site.data.keyword.cloud_notm}}.
{: .tip}

Maintenant, démarrez le serveur local.

```
npm start
```

L'application s'exécute désormais à [http://localhost:8080](http://localhost:8080). Vous pouvez ajouter des mots et des définitions à votre base de données {{site.data.keyword.composeForMongoDB}}. Si vous arrêtez puis redémarrez l'application, tous les mots que vous avez déjà ajoutés s'affichent lorsque vous actualisez la page.

Pour plus d'informations sur les données d'identification que vous avez créées pour que l'application se connecte à votre service, voir [Données d'identification disponibles](./connecting-bluemix-app.html#available-credentials).

## Etapes suivantes

Pour mieux comprendre comment fonctionne le modèle d'application [compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs), lisez le fichier Readme de l'application ou les commentaires sur le code dans `server.js`, qui fournissent des informations sur les fonctions de l'application.

Pour commencer à explorer votre service {{site.data.keyword.composeForMongoDB}}, voir les rubriques suivantes relatives au tableau de bord du service :

- [Présentation du tableau de bord](./dashboard-overview.html)
- [Sauvegardes](./dashboard-backups.html)
- [Paramètres](./dashboard-settings.html)

[ibm_cloud_signup_url]: https://ibm.biz/compose-for-mongodb-signup
