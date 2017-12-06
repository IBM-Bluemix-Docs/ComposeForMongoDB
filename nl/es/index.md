---

copyright:
  years: 2016,2017
lastupdated: "2017-10-23"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Guía de inicio con Compose for MongoDB
{: #getting-started-with-compose-for-mongodb}

{{site.data.keyword.composeForMongoDB_full}} utiliza la potente indexación y consulta, agregación y soporte de controlador amplio de MongoDB que lo ha convertido en el almacén de datos JSON preferido para muchas empresas y nuevas empresas. {{site.data.keyword.composeForMongoDB}} ofrece un sistema de despliegue sencillo y de escalado automático. Entrega alta disponibilidad y redundancia, copias de seguridad automatizadas y a demanda sin parar, herramientas de supervisión, integración en sistemas de alerta, rendimiento de vistas de análisis, y mucho más, en una interfaz de usuario sencilla y clara.
{:shortdesc}

**Nota:** Cualquier instancia de servicio de Compose proporcionada antes del 14 de septiembre de 2016 que siga activa se puede seguir utilizando y se puede acceder a ella directamente en [https://www.compose.com/](https://www.compose.com). Se puede acceder directamente a cualquier instancia de servicio de Compose que se proporcione desde este momento en adelante y se utilizará dentro de la cuenta de {{site.data.keyword.cloud}}.

## Creación de una instancia del servicio Compose for MongoDB

[Cree una instancia de {{site.data.keyword.composeForMongoDB}}](https://console.ng.bluemix.net/catalog/services/compose-for-mongodb/).

Al crear una instancia del servicio, asegúrese de que elige un nombre para el servicio y un nombre de credencial. Deje el servicio desenlazado; puede conectar una aplicación al servicio más adelante utilizando las credenciales proporcionadas cuando se proporcione el servicio.  Los distintos valores de credenciales se listan en la sección *Credenciales disponibles*.

Cuando suministre la instancia de {{site.data.keyword.composeForMongoDB}}, puede elegir los planes *Estándar* o *Empresa*. Con el plan *Empresa*, puede suministrar la instancia de {{site.data.keyword.composeForMongoDB}} en un clúster disponible de {{site.data.keyword.composeEnterprise}}. {{site.data.keyword.composeEnterprise}} proporciona la seguridad y nivel de aislamiento necesarios para el cumplimiento de las reglas empresariales y utiliza redes dedicadas para garantizar el rendimiento de las bases de datos desplegadas. Consulte la [documentación de Compose Enterprise](../ComposeEnterprise/index.html) para ver más detalles.

## Gestión de Compose for MongoDB

Puede gestionar el servicio desde el panel de control del servicio. Aquí encontrará información sobre su base de datos de {{site.data.keyword.cloud_notm}} Compose y cómo conectarse a la misma. También puede:
- gestionar copias de seguridad
- asignar más recursos para el servicio
- utilizar listas blancas para restringir el acceso a las bases de datos. 
Para obtener más información, consulte [Valores](./dashboard-settings.html).

## Conexión a Compose for MongoDB

Puede conectarse a su servicio utilizando las credenciales que se crean junto con el servicio, o con las series de conexión y la línea de mandatos que se proporcionan en el separador *Visión general* del panel de control del servicio.

## Conexión de una aplicación {{site.data.keyword.cloud_notm}} a Compose for MongoDB

Para conectar una aplicación {{site.data.keyword.cloud_notm}} al servicio, utilice las credenciales que se crean junto con el servicio. Encontrará información sobre cómo conectar una aplicación {{site.data.keyword.cloud_notm}} a un servicio {{site.data.keyword.composeForMongoDB}} en el apartado [Conexión de una aplicación {{site.data.keyword.cloud_notm}}](./connecting-bluemix-app.html).

## Conexión a Compose for MongoDB desde fuera de {{site.data.keyword.cloud_notm}}

Si desea conectarse a {{site.data.keyword.composeForMongoDB}} desde fuera de {{site.data.keyword.cloud_notm}}, puede utilizar las series de conexión proporcionadas o la línea de mandatos. Encontrará información sobre cómo conectar en el apartado [Conexión de una aplicación externa](./connecting-external.html).
