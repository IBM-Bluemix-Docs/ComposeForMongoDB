---

copyright:
  years: 2016,2018
lastupdated: "2017-11-15"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# A propos de {{site.data.keyword.composeForMongoDB}}
{: #about-compose-for-mongodb}

{{site.data.keyword.composeForMongoDB_full}} utilise la prise en charge puissante d'indexation et d'interrogation, d'agrégation et de pilote étendu propre à MongoDB qui a fait de ce dernier le magasin de données JSON incontournable d'un grand nombre de startups et d'entreprises. {{site.data.keyword.composeForMongoDB}} fournit un système de déploiement de mise à l'échelle automatique facile à utiliser. Il offre des fonctions de haute disponibilité et de redondance, des sauvegardes sans interruption automatisées et à la demande, des outils de surveillance, l'intégration dans des systèmes d'alerte, des vues d'analyse de performance, etc., via une interface utilisateur simple et nette.
{:shortdesc}

**Remarque :** les instances de service Compose qui ont été mises à disposition avant le 14 septembre 2016 et qui sont toujours actives peuvent encore être utilisées et sont directement accessibles sur le site [https://www.compose.com/](https://www.compose.com). Les instances de service Compose mises à disposition depuis cette date sont directement accessibles et utilisables dans votre compte {{site.data.keyword.cloud}}.

## Création d'une instance de service {{site.data.keyword.composeForMongoDB}}

Vous pouvez créer un service {{site.data.keyword.composeForMongoDB}} à partir de la [page {{site.data.keyword.composeForMongoDB}}](https://console.{DomainName}/catalog/services/compose-for-mongodb/) du catalogue {{site.data.keyword.cloud_notm}}.

Choisissez un nom de service ainsi qu'une région, une organisation et un espace dans lesquels mettre le service à disposition. Vous pouvez utiliser la zone **Select a database version** pour sélectionner parmi les versions de base de données disponibles.

Lorsque vous mettez votre instance {{site.data.keyword.composeForMongoDB}} à disposition, vous avez le choix entre le plan *Standard* et le plan *Entreprise*. Avec le plan *Entreprise*, vous pouvez mettre votre instance {{site.data.keyword.composeForMongoDB}} à disposition dans un cluster {{site.data.keyword.composeEnterprise}} disponible. {{site.data.keyword.composeEnterprise}} offre la sécurité et l'isolement requis par les normes de conformité des entreprises et utilise un réseau dédié pour garantir les performances des bases de données déployées. Pour plus d'informations, voir la [documentation Compose - Entreprise](../ComposeEnterprise/index.html).

## Gestion de {{site.data.keyword.composeForMongoDB}}

Vous pouvez gérer votre service depuis son tableau de bord. Vous y trouverez des informations concernant la base de données Compose d'{{site.data.keyword.cloud_notm}} et la manière de vous y connecter. Vous pouvez également :
- gérer vos sauvegardes ;
- allouer plus de ressources à votre service ;
- constituer des listes blanches pour limiter l'accès à vos bases de données. 
Pour plus d'informations, voir [Paramètres](./dashboard-settings.html).

## Connexion à {{site.data.keyword.composeForMongoDB}}

Vous pouvez vous connecter à votre service avec les données d'identification créées en même temps que le service ou avec les chaînes de connexion et la ligne de commande fournies dans l'onglet *Vue d'ensemble* du tableau de bord de votre service.

## Connexion d'une application {{site.data.keyword.cloud_notm}} à {{site.data.keyword.composeForMongoDB}}

Pour connecter une application {{site.data.keyword.cloud_notm}} à votre service, utilisez les données d'identification créées en même temps que le service. Pour plus d'informations sur la connexion d'une application {{site.data.keyword.cloud_notm}} à un service {{site.data.keyword.composeForMongoDB}}, voir [Connexion d'une application {{site.data.keyword.cloud_notm}}](./connecting-bluemix-app.html).

## Connexion à {{site.data.keyword.composeForMongoDB}} depuis l'extérieur d'{{site.data.keyword.cloud_notm}}

Pour vous connecter à {{site.data.keyword.composeForMongoDB}} depuis l'extérieur d'{{site.data.keyword.cloud_notm}}, vous pouvez utiliser les chaînes de connexion ou la ligne de commande fournies. Pour plus d'informations sur ce type de connexion, voir [Connexion d'une application externe](./connecting-external.html).

## Création d'une application Web à l'aide d'une pile MEAN

Vous pouvez utiliser {{site.data.keyword.composeForMongoDB}} dans le cadre d'une pile MEAN pour créer une application Web. Suivez la procédure décrite dans le tutoriel [Modern Web Application using MEAN stack](../../tutorials/mean-stack.html) pour créer une application Web composée d'une base de données Mongo, d'une infrastructure Web Express, d'une infrastructure frontale Angular et d'un contexte d'exécution Node.js.
