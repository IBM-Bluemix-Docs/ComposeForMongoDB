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

# Connexion d'une application {{site.data.keyword.cloud_notm}}

Pour connecter une application {{site.data.keyword.cloud}} à votre service, utilisez les données d'identification créées lors de la mise à disposition du service. Le modèle d'application montre comment utiliser Node.js pour établir une connexion à un service {{site.data.keyword.composeForMongoDB_full}} à l'aide des données d'identification fournies et comment créer une base de données, lire dans cette base de données et y écrire.

## Connexion à l'aide du modèle d'application 'Hello World'

Le modèle d'application [compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs) montre comment utiliser Node.js pour établir une connexion à un service {{site.data.keyword.composeForMongoDB}} à l'aide des données d'identification fournies. L'application crée une base de données, y lit et y écrit.

Téléchargez le modèle d'application et suivez les instructions contenues dans le fichier Readme. Ensuite, sur la page des détails d'application d'{{site.data.keyword.cloud_notm}}, cliquez sur **Afficher l'application** pour afficher le contenu du tableau *Exemples*.

## Données d'identification disponibles

Nom de zone|Description
----------|-----------
`uri`|URI à utiliser pour établir la connexion au service. `uri` comprend le schéma (`mongodb:`), le nom et le mot de passe de l'administrateur, le nom d'hôte du serveur, le numéro de port auquel se connecter, le nom de base de données, ainsi que `?ssl=true` pour activer les connexions SSL.
`uri_cli`|Ligne de commande shell `mongo` qui permet d'établir la connexion à l'instance de base de données.
`ca_certificate_base64`|Certificat autosigné utilisé pour confirmer qu'une application se connecte au serveur approprié. Le certificat est codé en base64. Vous devez décoder la clé avant de l'utiliser, comme illustré dans le modèle d'application.
`deployment_id`|Identificateur interne du service, créé dans Compose.
`db_type`|Type de base de données fourni par le service, en l'occurrence, `mongodb`.
`name`|Nom du déploiement de base de données.
{: caption="Tableau 1. Données d'identification Compose for MongoDB" caption-side="top"}
