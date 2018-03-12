---

copyright:
  years: 2016,2018
lastupdated: "2018-02-16"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}


# Guía de aprendizaje de iniciación
Esta guía de aprendizaje utiliza la app de ejemplo [compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs) para demostrar cómo utilizar Node.js para conectarse a un servicio de {{site.data.keyword.composeForMongoDB_full}} utilizando las credenciales proporcionadas. La aplicación crea, lee y escribe en una base de datos utilizando datos suministrados a través de la interfaz web de la app.
{: shortdesc}

## Antes de empezar

Asegúrese de que tiene una [cuenta de {{site.data.keyword.cloud_notm}}][ibm_cloud_signup_url]{:new_window}.

También deberá instalar [Node.js](https://nodejs.org/) y [Git](https://git-scm.com/downloads).

## Paso 1: Crear una instancia de servicio de {{site.data.keyword.composeForMongoDB}}
{: #create-service}

Puede crear un servicio de {{site.data.keyword.composeForMongoDB}} desde la [página de {{site.data.keyword.composeForMongoDB}}](https://console.{DomainName}/catalog/services/compose-for-mongodb/) en el catálogo de {{site.data.keyword.cloud_notm}}.

Elija un nombre de servicio, región, organización y espacio en los que suministrar el servicio, y para el campo **Seleccionar una versión de base de datos**, elija _Versión preferida más reciente_.

A continuación, elija un plan de precios para el servicio. Puede elegir los planes *Estándar* o *Empresa*. Con el plan *Empresa*, puede suministrar la instancia de {{site.data.keyword.composeForMongoDB}} en un clúster disponible de {{site.data.keyword.composeEnterprise}}. {{site.data.keyword.composeEnterprise}} proporciona la seguridad y nivel de aislamiento necesarios para el cumplimiento de las reglas empresariales y utiliza redes dedicadas para garantizar el rendimiento de las bases de datos desplegadas. Consulte la documentación de [Compose Enterprise](../ComposeEnterprise/index.html) para obtener más detalles.

## Paso 2: Clonar la app de ejemplo Hello World desde Github

Clone la app Hello World a su entorno local desde el terminal utilizando el mandato siguiente:

```
git clone https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs.git
```

## Paso 3: Instalar las dependencias de la app

Utilice npm para instalar dependencias.

1. Desde el terminal, cambie el directorio a donde se encuentra la app de ejemplo.
  
  ```
  cd compose-mongodb-helloworld-nodejs
  ```

2. Instale las dependencias listadas en el archivo `package.json`.
  
  ```
  npm install
  ```

## Paso 4: Crear credenciales de servicio

Antes de enviar la app en {{site.data.keyword.cloud_notm}}, puede ejecutarla localmente para probar la conexión a su instancia de servicio de {{site.data.keyword.composeForMongoDB}}. Para conectarse al servicio deberá crear un conjunto de credenciales de servicio.

1. Desde el panel de control de {{site.data.keyword.cloud_notm}}, abra la instancia de servicio de {{site.data.keyword.composeForMongoDB}}.
2. Seleccione _Credenciales de servicio_ en el menú principal para abrir la vista Credenciales de servicio.
3. Pulse **Nueva credencial**.
4. Elija un nombre para las credenciales y pulse **Añadir**.
5. Sus nuevas credenciales aparecerán ahora. Pulse **Ver credenciales** en la fila correspondiente de la tabla para ver las credenciales, y pulse el icono **Copiar** para copiar sus credenciales.
6. En el editor de su elección, cree un archivo nuevo con lo siguiente, insertando sus credenciales como se muestra:

  ```
  {
    "services": {
      "compose-for-mongodb": [
        {
          "credentials": INSERT YOUR CREDENTIALS HERE
        }
      ]
    }
  }
  ```
6. Guarde el archivo como `vcap-local.json` en el directorio donde se encuentra la app de ejemplo.

Para evitar exponer sus credenciales accidentalmente al impulsar una aplicación a Github o {{site.data.keyword.cloud_notm}}, debe asegurarse de que el archivo que contiene las credenciales esté listado en el archivo ignore relevante. Si abre `.cfignore` y `.gitignore` en el directorio de la aplicación, verá que `vcap-local.json` se lista en ambos, por lo que no se incluirá en los archivos que se cargan al enviar la app a Github o {{site.data.keyword.cloud_notm}}.
{: .tip}

## Paso 5: Ejecutar la app localmente

```
npm start
```

La app se está ejecutando en [http://localhost:8080](http://localhost:8080). Puede añadir palabras y definiciones a la base de datos de {{site.data.keyword.composeForMongoDB}}. Cuando se detenga y se reinicie la app, cualquier palabra que haya añadido ya se mostrará al renovar la página.

La próxima etapa es conectar su app a la instancia de servicio y desplegar la app en {{site.data.keyword.cloud_notm}}.

## Paso 6: Descargar e instalar la herramienta de CLI de {{site.data.keyword.cloud_notm}}

La herramienta de CLI de {{site.data.keyword.cloud_notm}} es lo que utilizará para comunicarse con {{site.data.keyword.cloud_notm}} desde su terminal o línea de mandatos. Para obtener detalles, consulte [Descargar e instalar la CLI de {{site.data.keyword.cloud_notm}}](https://console.{DomainName}/docs/cli/reference/bluemix_cli/download_cli.html).

## Paso 7: Conectar a {{site.data.keyword.cloud_notm}}

1. Conéctese a {{site.data.keyword.cloud_notm}} en la herramienta de línea de mandatos y siga las indicaciones para iniciar la sesión.

  ```
  bx login
  ```

  Si tiene un ID de usuario federado, utilice el mandato `bx login --sso` para iniciar sesión con el ID de inicio de sesión único. Consulte [Inicio de sesión con un ID federado](https://console.{DomainName}/docs/cli/login_federated_id.html#federated_id) para obtener más información.
  {: .tip}

2. Asegúrese de que se está dirigiendo a la organización y al espacio de {{site.data.keyword.cloud_notm}} correctos.

  ```
  bx target --cf
  ```

  Elija entre las opciones proporcionadas, utilizando los mismos valores que ha utilizado al crear el servicio.

## Paso 8: Actualizar el archivo de manifiesto de la app
{: #update-manifest}

{{site.data.keyword.cloud_notm}} utiliza un archivo de manifiesto, `manifest.yml`, para asociar una aplicación con un servicio. Siga estos pasos para crear el archivo de manifiesto.

1. En un editor, abra un archivo nuevo y añada lo siguiente:

  ```
  ---
  applications:
  - name:    compose-mongodb-helloworld-nodejs
    host:    compose-mongodb-helloworld-nodejs
    memory:  128M
    services:
      - my-compose-for-mongodb-service
  ```

2. Cambie el valor `host` por uno que sea exclusivo. El host que elija determinará el subdominio del URL de la aplicación: `<host>.mybluemix.net`.
3. Cambie el valor `name`. El valor que elija será el nombre de la app que aparecerá en el panel de control de {{site.data.keyword.cloud_notm}}.
4. Actualice el valor `services` para que coincida con el nombre del servicio que ha creado en [Crear una instancia de servicio de {{site.data.keyword.composeForMongoDB}}](#create-service). 

## Paso 9: Enviar la app a {{site.data.keyword.cloud_notm}}.

Cuando se envíe la app, se enlazará automáticamente al servicio especificado en el archivo de manifiesto.

```
bx cf push
```

## Paso 10: Comprobar que la app está conectada al servicio de {{site.data.keyword.composeForMongoDB}}

1. Vaya al panel de control de servicio de {{site.data.keyword.composeForMongoDB}}
2. Seleccione _Conexiones_ desde el menú del panel de control. La aplicación debe estar listada en _Aplicaciones conectadas_.

Si su aplicación no aparece, repita los pasos 7 y 8, asegurándose de que haya especificado los detalles correctos en [manifest.yml](#update-manifest).

## Paso 11: Utilizar la app

Ahora, cuando se visite `<host>.mybluemix.net/`, podrá ver el contenido de la recopilación de {{site.data.keyword.composeForMongoDB}}. A medida que añada palabras y sus definiciones, se añadirán a la base de datos y se mostrarán. Si detiene y reinicia la app, verá que aparece listada cualquier palabra y definición que ya haya añadido.


## Pasos siguientes

Para obtener más información sobre cómo funciona la app de ejemplo [compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs), puede leer el archivo readme de la aplicación, o los comentarios de código en `server.js`, que dan alguna información sobre las funciones de la app.

Para empezar a explorar su servicio de {{site.data.keyword.composeForMongoDB}}, consulte los temas siguientes sobre el panel de control del servicio:

- [Visión general del panel de control](./dashboard-overview.html)
- [Copias de seguridad](./dashboard-backups.html)
- [Valores](./dashboard-settings.html)

Para obtener información sobre las credenciales que ha creado para que la aplicación se conecte al servicio, verá [Credenciales disponibles](./connecting-bluemix-app.html#available-credentials).

[ibm_cloud_signup_url]: https://ibm.biz/compose-for-mongodb-signup
