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

# Architecture 
{: #architecture}

## Réplication

Un service {{site.data.keyword.composeForMongoDB_full}} est fourni avec trois jeux de répliques de noeud de données, configurés en tant que fragment unique. Vous sélectionnez une région où le service est déployé, les trois noeuds de données sont propagés dans les zones de disponibilité de la région, puis vos données sont répliquées dans les trois noeuds. MongoDB envoie toutes les opérations d'écriture au noeud de données principal, puis les propage (via oplog) au noeud de données secondaire sur le fragment. Si le noeud principal échoue, le noeud secondaire devient le principal. Si l'un des membres de données n'est plus accessible, votre fragment MongoDB continue à fonctionner normalement.

## Chiffrement de disque

Tous les services {{site.data.keyword.composeForMongoDB}} disposent du chiffrement au repos. Vos données résident sur des serveurs où le chiffrement au niveau volume est activé. 

Etant donné que {{site.data.keyword.composeForMongoDB}} est un service géré, les opérateurs {{site.data.keyword.cloud}} Compose peuvent voir les données. Outre le chiffrement du disque, il est recommandé de chiffrer vos informations personnelles avant de les stocker dans la base de données ou d'utiliser des extensions ou des fonctions permettant d'activer le chiffrement sur la base de données elle-même. Même si ces méthodes risquent d'avoir un impact sur la facilité d'utilisation ou les performances, il est conseillé de s'assurer que les informations personnelles sont protégées par un chiffrement.

## Mise à l'échelle automatique

Les ressources sont automatiquement mises à l'échelle en fonction de l'utilisation totale du stockage sur disque des bases de données MongoDB. La mémoire est basée sur un rapport d'espace disque mis à disposition de 1 sur 4. Ces ressources sont intégrées en tant qu'unités.

La mise à l'échelle automatique est conçue pour répondre aux tendances à court et moyen termes de votre base de données. Votre service est contrôlé toutes les heures et, s'il est à court d'espace disque, des unités supplémentaires sont attribuées au déploiement. 

Une mise à l'échelle automatique ne réduit pas la taille des déploiements où l'utilisation du disque/de la mémoire à diminué. Les ressources mises à disposition de vos bases de données sont conservées pour les besoins ultérieurs ou jusqu'à ce que vous réduisiez manuellement votre déploiement.
{: .tip}

## Base de données Admin

Si vous stockez des données dans la base de données Admin, cette dernière ne fonctionne pas de manière optimale et vous risquez de perdre des données.

A l'instar des autres installations MongoDB, {{site.data.keyword.composeForMongoDB}} possède une base de données Admin. Cette dernière contient des informations sur les utilisateurs et les rôles de la base de données, ainsi que diverses collectes de suivi. Dans le cas d'une installation MongoDB simple, ces informations sont stockées avec d'autres données dans la base de données. 

Dans {{site.data.keyword.composeForMongoDB}}, une architecture à résilience plus élevée utilise l'architecture fragmentée de MongoDB. Cela implique le partage des données d'administration entre les fragments. A cet effet, un autre cluster de base de données est créé pour contenir la base de données Admin.

Ce cluster de base de données Admin n'est pas adapté à la mise à l'échelle et se dégrade rapidement si quelques données y sont stockées. Il est donc impératif de vérifier que les applications n'écrivent pas de données dans la base de données Admin.
