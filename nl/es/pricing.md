---

copyright:
  years: 2016,2018
lastupdated: "2018-01-03"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Tarifas
{: #pricing}

## Configuración básica
Un servicio de {{site.data.keyword.composeForMongoDB_full}} empieza por un conjunto de réplicas de tres nodos de datos, en una configuración de una sola fragmentación. Cada nodo tiene 1 GB de almacenamiento y 102 MB de memoria, lo que es igual a 1 unidad de recursos. El servicio _incluye_ la réplica y la alta disponibilidad, de modo que cada unidad y el precio por unidad _incluye_ el coste de los recursos en los tres nodos.

También se incluyen dos direccionadores de mongos para gestionar conexiones y alta disponibilidad de 64 MB de memoria cada uno.

### Coste
La configuración de servicio base tiene un precio establecido. Consulte los mosaicos del catálogo en {{site.data.keyword.cloud_notm}} para ver los precios básicos en su moneda local. Por ejemplo, el precio base en dólares de EE.UU. es de 18 dólares USD/mes.


## Opciones de expansión
Si necesita más almacenamiento o memoria para el servicio, puede aumentar los recursos asignados en una proporción 10:1 de almacenamiento de disco para la unidad de memoria. Aumentar el disco asignado al despliegue también aumenta la RAM asignada. Una unidad de {{site.data.keyword.composeForMongoDB}} tiene 1 GB de almacenamiento a 102 MB de memoria y cada unidad y el precio por unidad _incluye_ el coste para aumentar los recursos en los tres nodos de datos. 

### Coste
Cada unidad adicional (1 GB de almacenamiento/102 MB de memoria) tiene un precio por unidad, que aparece en la moneda local en el mosaico del catálogo de {{site.data.keyword.cloud_notm}} para el servicio. En dólares de EE.UU., cada unidad adicional cuesta 18 dólares. A medida que aumenta el tamaño _total_ de todos los servicios de {{site.data.keyword.composeForMongoDB}}, el precio por unidad disminuye, como se muestra en la tabla de precio por niveles.

### Fijación de precios por grado
Número de unidades|Precio por unidad
----------|-----------
De 1 a 9 unidades|precio base por unidad -- 18,00 dólares USD/unidad
De 10 a 24 unidades|10 % de reducción -- 16,20 dólares USD/unidad
De 25 a 49 unidades|20 % de reducción -- 14,40 dólares USD/unidad
De 50 a 99 unidades|30 % de reducción -- 12,60 dólares USD/unidad
De 100 a 499 unidades|40 % de reducción -- 10,80 dólares USD/unidad
De 500 a 999 unidades|50 % de reducción -- 9,00 dólares USD/unidad
De 1.000 a 4.999 unidades|60 % de reducción -- 7,20 dólares USD/unidad
Más de 5.000 unidades|70 % de reducción -- 5,40 dólares USD/GB
{: caption="Tabla 1. Precio por niveles de {{site.data.keyword.composeForMongoDB}}" caption-side="top"}
