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

# Notas del usuario de {{site.data.keyword.composeForMongoDB}}
{: #user-notes-compose-for-mongodb}

Estas notas abarcan diversas cuestiones que han sido planteadas por los usuarios de {{site.data.keyword.composeForMongoDB}}.

## La base de datos de administración

Si almacena datos en la base de datos de administración, no funcionará correctamente, y se expone a perder datos.

Al igual que otras instalaciones de MongoDB, {{site.data.keyword.composeForMongoDB}} tiene una base de datos de administración. La base de datos de administración contiene información sobre los usuarios y los roles de la base de datos, además de varias recopilaciones de seguimiento. En una instalación sencilla de MongoDB, estos datos se almacenan junto con los demás datos de la base de datos. 

En {{site.data.keyword.composeForMongoDB}}, una arquitectura más resistente que utiliza la arquitectura fragmentada de MongoDB, esto implica compartir los datos de administración entre fragmentos. Esto se consigue mediante la creación de otro clúster de base de datos que contenga la base de datos de administración.

Esta base de datos de administración de clúster no está creada para ser escalada, y se degrada rápidamente con cualquier cantidad de datos que se almacene en ella. Por lo tanto, es imperativo asegurarse de que las aplicaciones no graben datos en la base de datos de administración.

## Series de conexión

La migración tras error en el lado del cliente es responsabilidad del diseñador de aplicaciones. Si no utiliza un controlador que gestione por completo los errores de migración tras error, debe:

* Utilizar un controlador que acepte varios puntos finales en su configuración de conexión.
* Asegurarse de que las llamadas de aplicaciones para leer o escribir en la base de datos reaccionen a los errores adecuadamente. Entre las opciones, se incluyen:
  + Registrar el error y reconectar.
  + Reconectar y reintentar la operación que ha provocado el error.
  + Poner en cola la operación que ha fallado y planificar la reconexión.






