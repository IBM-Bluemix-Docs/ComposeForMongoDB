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

# {{site.data.keyword.composeForMongoDB}} - Remarques utilisateur
{: #user-notes-compose-for-mongodb}

Ces remarques traitent de différents sujets abordés par les utilisateurs de {{site.data.keyword.composeForMongoDB}}.

## Base de données Admin

Si vous stockez des données dans la base de données Admin, cette dernière ne fonctionne pas de manière optimale et vous risquez de perdre des données.

A l'instar des autres installations MongoDB, {{site.data.keyword.composeForMongoDB}} possède une base de données Admin. Cette dernière contient des informations sur les utilisateurs et les rôles de la base de données, ainsi que diverses collectes de suivi. Dans le cas d'une installation MongoDB simple, ces informations sont stockées avec d'autres données dans la base de données. 

Dans {{site.data.keyword.composeForMongoDB}}, une architecture à résilience plus élevée utilise l'architecture fragmentée de MongoDB. Cela implique le partage des données d'administration entre les fragments. A cet effet, un autre cluster de base de données est créé pour contenir la base de données Admin.

Ce cluster de base de données Admin n'est pas adapté à la mise à l'échelle et se dégrade rapidement si quelques données y sont stockées. Il est donc impératif de vérifier que les applications n'écrivent pas de données dans la base de données Admin. 

## Chaînes de connexion

Le basculement côté client relève de la responsabilité du concepteur d'applications. A moins que vous n'utilisiez un pilote qui traite entièrement les erreurs de basculement, il est conseillé de procéder comme suit :

* Utilisez un pilote qui accepte plusieurs noeuds finaux dans sa configuration de connexion.
* Vérifiez que les appels de vos applications en vue d'une lecture ou d'une écriture dans la base de données réagissent aux erreurs de manière appropriée. Ces opérations incluent : 
  + la journalisation de l'erreur et la reconnexion,
  + la reconnexion et une nouvelle tentative de l'opération à l'origine de l'erreur,
  + la mise en file d'attente de l'opération ayant échoué et la planification de la reconnexion.






