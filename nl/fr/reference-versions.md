---
copyright:
  years: 2016,2018
lastupdated: "2018-06-05"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Versions

Versions déployables| Version préférée
----------|-----------
3.2.10, 3.2.11, 3.2.18, 3.4.10 | 3.2.18
{: caption="Tableau 1. Versions de MongoDB" caption-side="top"}

## Version préférée

La version préférée est généralement la version la plus récente de la base de données MongoDB disponible pour {{site.data.keyword.composeForMongoDB}}. Il s'agit de la version par défaut dans la liste déroulante du sélecteur de version sur la page du catalogue. C'est également la version automatiquement mise à disposition par l'API lorsqu'aucune version n'est spécifiée dans l'appel.

### Nouveau protocole de version préférée

Lorsqu'une nouvelle version est disponible, sa publication est annoncée et elle devient disponible pour déploiement. Après publication un délai d'environ 7 jours s'écoule pendant lequel la nouvelle version est disponible sans être la version préférée. Ce délai permet aux utilisateurs de déployer et tester la nouvelle version tout en disposant toujours de la version en cours. Au bout de ce délai de 7 jours, la nouvelle version est définie en tant que version préférée ou une nouvelle date de modification est annoncée.

Vous trouverez la liste des versions disponibles pour mise à disposition sur la {{site.data.keyword.composeForMongoDB}} [page du catalogue](https://console.{DomainName}/catalog/services/compose-for-mongodb).

Pour obtenir la liste des versions actuellement disponibles pour votre service {{site.data.keyword.composeForMongoDB}}, vous pouvez utiliser le
noeud final [GET /2016-07/deployments/:id/versions](https://apidocs.compose.com/v1.0/reference#2016-07-get-deployments-versions).
