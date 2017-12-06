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

# Sauvegardes
{: #backups}

Vous pouvez créer et télécharger des sauvegardes à partir de la page *Gérer* du tableau de bord de votre service {{site.data.keyword.composeForMongoDB_full}}. Les sauvegardes planifiées comme manuelles sont disponibles.

## Affichage des sauvegardes existantes

Des sauvegardes quotidiennes de votre base de données sont automatiquement planifiées. Pour afficher vos sauvegardes existantes, accédez à la page *Gérer* du tableau de bord de votre service. 

![Sauvegardes](./images/mongodb-backups-show.png "Liste des sauvegardes disponibles")

Cliquez sur la ligne correspondante pour développer les options de chaque sauvegarde disponible.
  
![Options de sauvegarde](./images/mongodb-backups-options.png "Options d'une sauvegarde.") 

## Création d'une sauvegarde à la demande

Outre les sauvegardes planifiées, vous pouvez créer une sauvegarde manuelle. Pour créer une sauvegarde manuelle, accédez à la page *Gérer* du tableau de bord de votre service et cliquez sur *Backup now*.

## Téléchargement d'une sauvegarde

Pour télécharger une sauvegarde, accédez à la page *Gérer* du tableau de bord de votre service et cliquez sur *Télécharger* sur la ligne correspondant à la sauvegarde que vous voulez télécharger.

## Utilisation d'une sauvegarde avec une base de données locale

Vous pouvez utiliser votre sauvegarde {{site.data.keyword.composeForMongoDB}} pour exécuter une copie locale de votre base de données. 

Pour obtenir une restauration locale de vos bases de données et données :

1. Téléchargez une sauvegarde à partir du tableau de bord de votre service.
2. Installez MongoDB et les outils MongoDB en local.
3. Extrayez les données de la sauvegarde dans un nouveau répertoire.
4. Démarrez une instance MongoDB locale avec la commande `mongod --dbpath ./db` où './db' est le répertoire qui contient les données extraites.

## Restauration d'une sauvegarde

Pour restaurer une sauvegarde sur une nouvelle instance de service, suivez la procédure d'affichage des sauvegardes, puis cliquez sur la ligne correspondante afin de développer les options de la sauvegarde que vous voulez télécharger. Cliquez sur le bouton **Restore**. Un message vous indiquant qu'une restauration a été initiée s'affiche. La nouvelle instance de service sera automatiquement nommée "mongodb-restore-[timestamp]" ; elle s'affiche dans votre tableau de bord au démarrage de la mise à disposition.