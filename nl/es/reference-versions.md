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

# Versiones

Versiones desplegables| Versión preferida
----------|-----------
3.2.10, 3.2.11, 3.2.18, 3.4.10 | 3.2.18
{: caption="Tabla 1. Versiones de MongoDB" caption-side="top"}

## Versión preferida

La versión preferida es normalmente la versión más reciente de la base de datos MongoDB que está disponible para {{site.data.keyword.composeForMongoDB}}. Es la versión que es el valor predeterminado en el selector de versión desplegable de la página de catálogo. También es la versión que la API suministra automáticamente si no se especifica ninguna versión en la llamada.

### Nuevo protocolo de versión preferido

Cuando una nueva versión está disponible, su release se anuncia y está disponible para el despliegue. Siguiendo al release, hay una ventana de 7 días aproximadamente en la que está disponible la versión más reciente, pero no es la versión preferida. Esta ventana le ofrece la oportunidad de desplegar y probar la nueva versión, mientras que la versión actual aún está disponible. Al final de la ventana de 7 días, la nueva versión se establece como la versión preferida, o se anuncia una nueva fecha para este cambio.

La lista de versiones disponibles para suministrar se encuentra en la [página de catálogo](https://console.{DomainName}/catalog/services/compose-for-mongodb) de {{site.data.keyword.composeForMongoDB}}.

Para obtener una lista actual de las versiones disponibles para el servicio de {{site.data.keyword.composeForMongoDB}}, puede utilizar el punto final
[GET /2016-07/deployments/:id/versions](https://apidocs.compose.com/v1.0/reference#2016-07-get-deployments-versions).
