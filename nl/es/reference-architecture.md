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

# Arquitectura 
{: #architecture}

## Réplica

Un servicio {{site.data.keyword.composeForMongoDB_full}} se suministra en un conjunto de réplicas de tres nodos de datos, configurado como un único fragmento. Seleccione una región en la que se ha desplegado el servicio, los tres nodos de datos se distribuirán a través de las zonas de disponibilidad de la región y los datos se replicarán en los tres. MongoDB envía todas las operaciones de escritura al nodo de datos primario y luego las propaga (a través del oplog) al nodo de datos secundario en el fragmento. Si el nodo primario falla, el nodo secundario se promociona a primario. Si algún miembro de datos se convierte en no accesible, el fragmento de MongoDB continúa operando con normalidad.

## Cifrado de disco

Todos los servicios de {{site.data.keyword.composeForMongoDB}} tienen cifrado en reposo. Los datos residen en servidores que tienen el cifrado de nivel de volumen habilitado. 

Puesto que {{site.data.keyword.composeForMongoDB}} es un servicio gestionado, es posible que los operadores de {{site.data.keyword.cloud}} Compose vean los datos. Además del cifrado de disco, le recomendamos que, si está almacenando información personal, cifre información antes de almacenarla en la base de datos o utilizando extensiones o características para habilitar el cifrado en la propia base de datos. Si bien estos métodos pueden afectar a la usabilidad o al rendimiento, es una buena práctica asegurarse de que la información personal esté protegida con cifrado.

## Escalado automático

Los recursos se escalan automáticamente en función del uso de almacenamiento de disco total de las bases de datos MongoDB. La memoria se basa en una proporción de disco suministrada de 1:4. Estos recursos se empaquetan como unidades.

El escalado automático está diseñado para responder a las tendencias a corto y medio plazo de la base de datos. Cada hora, el servicio está marcado y si se está quedando corto en el espacio de disco, se asignan más unidades al despliegue. 

El escalado automático no reduce los despliegues en los que el uso de disco/memoria se ha reducido. Los recursos suministrados a las bases de datos permanecerán para sus necesidades futuras, o hasta que reduzca el despliegue manualmente.
{: .tip}

## La base de datos de administración

Si almacena datos en la base de datos de administración, no funcionará correctamente, y se expone a perder datos.

Al igual que otras instalaciones de MongoDB, {{site.data.keyword.composeForMongoDB}} tiene una base de datos de administración. La base de datos de administración contiene información sobre los usuarios y los roles de la base de datos, además de varias recopilaciones de seguimiento. En una instalación sencilla de MongoDB, estos datos se almacenan junto con los demás datos de la base de datos. 

En {{site.data.keyword.composeForMongoDB}}, una arquitectura más resistente que utiliza la arquitectura fragmentada de MongoDB, esto implica compartir los datos de administración entre fragmentos. Esto se consigue mediante la creación de otro clúster de base de datos que contenga la base de datos de administración.

Esta base de datos de administración de clúster no está creada para ser escalada, y se degrada rápidamente con cualquier cantidad de datos que se almacene en ella. Por lo tanto, es imperativo asegurarse de que las aplicaciones no graben datos en la base de datos de administración.
