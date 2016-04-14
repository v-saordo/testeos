<properties
   pageTitle="Expectativas de la versión preliminar de Almacenamiento de datos SQL | Microsoft Azure"
   description="Resumen de las capacidades de la versión preliminar pública y los objetivos de disponibilidad general de Almacenamiento de datos SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Expectativas de la versión preliminar de Almacenamiento de datos SQL

En este artículo se describen las capacidades de la versión preliminar de Almacenamiento de datos SQL y nuestros objetivos de disponibilidad general del servicio. Actualizaremos esta información de manera continua mientras mejoramos las capacidades de la versión preliminar pública.

Nuestros objetivos para el Almacenamiento de datos SQL:

- Rendimiento predecible y escalabilidad lineal hasta petabytes de datos.
- Alta confiabilidad para todas las operaciones de almacenamiento de datos, respaldadas por un Contrato de nivel de servicio (SLA).

Trabajaremos de manera continua en la consecución de estos objetivos antes de poner a Almacenamiento de datos SQL a disposición del público en general.

## Rendimiento predecible y escalable

Almacenamiento de datos SQL de Azure presenta Unidades de almacenamiento de datos (DWU) como una forma de medir los recursos informáticos (CPU, memoria, E/S de almacenamiento) disponibles para el almacenamiento de datos. Aumentar el número de DWU aumenta los recursos. A medida que aumenta el número de DWU, el Almacenamiento de datos SQL ejecuta operaciones en paralelo (por ejemplo, la carga o la consulta de datos) a través de recursos más distribuidos. Esto reduce la latencia y mejora el rendimiento.

Cualquier almacén de datos tiene 2 métricas de rendimiento fundamentales:

- Velocidad de carga. El número de registros que se pueden cargar en el almacén de datos por segundo. Medimos específicamente el número de registros que se pueden importar a través de PolyBase, de Almacenamiento de blobs de Azure a una tabla con un índice de almacén-columna en clústeres.
- Velocidad de digitalización. El número de registros que se pueden recuperar de forma secuencial del almacén de datos por segundo. Medimos específicamente el número de registros devueltos por una consulta de un índice de almacén de columnas en clústeres.


Se están midiendo algunas mejoras importantes del rendimiento y pronto se compartirán las velocidades esperadas. Durante la versión preliminar, realizaremos mejoras continuas (por ejemplo, aumentar la compresión y el almacenamiento en caché) para aumentar estas métricas de rendimiento y asegurar que se escalan de forma predecible.


## Alta confiabilidad respaldada por un Contrato de nivel de servicio

### Protección de datos

Almacenamiento de datos SQL almacena todos los datos en el Almacenamiento de Azure con blobs de redundancia geográfica. Se mantienen tres copias sincrónicas de los datos en la región de Azure local para garantizar la protección de datos transparente en caso de errores localizados (por ejemplo, errores en la unidad de almacenamiento). Además, se mantienen tres copias asincrónicas más en una región remota de Azure para garantizar la protección de los datos en caso de errores regionales (recuperación ante desastres). Las regiones locales y remotas están emparejadas para mantener latencias de sincronización aceptables (p. ej., Este de EE. UU y Oeste de EE. UU).


### Copias de seguridad

Almacenamiento de datos SQL de Azure realiza una copia de seguridad de todos los datos cada 8 horas como mínimo mediante instantáneas de Almacenamiento de Azure. Estas instantáneas se mantienen durante 7 días. Esto permite restaurar los datos en un mínimo de 21 momentos anteriores en los últimos 7 días hasta la hora en que se tomó la última instantánea. Puede restaurar datos desde una instantánea mediante las API de PowerShell o REST.

Las instantáneas se copian de forma asincrónica en una región de Azure remota para aumentar la capacidad de recuperación en caso de errores regionales (recuperación ante desastres).


### Finalización de consultas

Almacenamiento de datos SQL almacena los datos en uno o más nodos informáticos que almacenan cada uno algunos de los datos de usuario y controlan la ejecución de consultas en esos datos. Como parte de la arquitectura de procesamiento masivamente paralela (MPP), las consultas se ejecutan en paralelo en los nodos de cálculo. Almacenamiento de datos SQL detecta y mitiga automáticamente los errores de nodo de cálculo. Sin embargo, en la versión preliminar, es posible que una operación falle (por ejemplo, la carga o la consulta de datos) debido a errores de nodo individual. En la versión preliminar, realizamos mejoras continuas para completar correctamente las operaciones a pesar de los errores de nodo.


## Pasos siguientes

[Introducción][] a la versión preliminar pública.

<!--Image references-->

<!--Article references-->
[Introducción]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0309_2016-->