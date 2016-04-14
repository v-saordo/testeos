<properties      
    pageTitle="Partición y escalado de datos en DocumentDB con particionamiento | Microsoft Azure"      
    description="Consulte cómo se escalan datos con una técnica denominada particionamiento. Obtenga información sobre cómo crear particiones de datos en DocumentDB y cuándo usar particiones por rango y hash."         
    keywords="Scale data, shard, sharding, documentdb, azure, Microsoft azure"
	services="documentdb"      
    authors="arramac"      
    manager="jhubbard"      
    editor="monicar"      
    documentationCenter=""/>
<tags       
    ms.service="documentdb"      
    ms.workload="data-services"      
    ms.tgt_pltfrm="na"      
    ms.devlang="na"      
    ms.topic="article"      
    ms.date="02/09/2016"      
    ms.author="arramac"/>

# Partición y escalado de datos en DocumentDB

[DocumentDB de Microsoft Azure](https://azure.microsoft.com/services/documentdb/) se ha diseñado para ayudarle a lograr un rendimiento rápido y predecible. Además, permite *escalar horizontalmente* sin problemas junto con su aplicación a medida que esta crezca. DocumentDB se ha utilizado para mejorar los servicios de producción a gran escala en Microsoft, por ejemplo, el almacén de datos de usuario que se utiliza con la suite MSN de aplicaciones móviles y web.

Puede lograr una escala casi infinita en términos de almacenamiento y capacidad de proceso para su aplicación de DocumentDB efectuando una partición horizontal de los datos: un concepto conocido comúnmente como **particionamiento**. Las cuentas de DocumentDB se pueden escalar linealmente con el costo mediante unidades agrupables denominadas **colecciones**. La mejor forma de dividir los datos en colecciones dependerá del formato de los datos y de los patrones de acceso.

Después de leer este artículo, podrá responder a las preguntas siguientes:

 - ¿Qué es el hash y la creación de particiones por rangos?
 - ¿Cuándo se usa cada técnica de partición y por qué?
 - ¿Sabe cómo compilar una aplicación particionada en Azure DocumentDB?

Este artículo presenta algunos conceptos acerca del particionamiento. Si está listo para escribir código que crea particiones de datos con el SDK de DocumentDB, eche un vistazo a [Creación de particiones de datos en DocumentDB con el SDK de .NET](documentdb-sharding.md).

## Colecciones = Particiones

Antes de profundizar más en las técnicas de escalado y partición de datos, es importante entender qué es una colección y qué no. Como ya sabrá, una colección es un contenedor para los documentos JSON. Las colecciones en DocumentDB no son simplemente contenedores *lógicos*, sino también *físicos*. Constituyen el límite de la transacción para los desencadenadores y procedimientos almacenados, además de ser el punto de entrada para las consultas y las operaciones CRUD. Cada colección tiene asignada una cantidad reservada de capacidad de proceso que no se comparte con otras colecciones de la misma cuenta. Por lo tanto, puede escalar horizontalmente la aplicación, tanto en términos de almacenamiento como de capacidad de proceso, mediante la adición de más colecciones y la posterior distribución de documentos en ellas.

Las colecciones son distintas de las tablas incluidas en las bases de datos relacionales. Las colecciones no aplican un esquema. Por lo tanto, se pueden almacenar distintos tipos de documentos con esquemas diferentes en la misma colección. Sin embargo, puede optar por utilizar las colecciones para almacenar objetos de un solo tipo, como se haría con las tablas. El mejor modelo depende solo de la forma en que aparezcan los datos juntos en las consultas y las transacciones.

## Creación de particiones con DocumentDB

Hay dos enfoques que pueden usarse para la partición de datos con Azure DocumentDB (o cualquier sistema distribuido con dicho propósito): *creación de particiones por rangos* y *creación de particiones por hash*. Esto implica elegir un solo nombre de propiedad JSON dentro del documento como *clave de partición*, normalmente la propiedad ID natural, por ejemplo, "userID" para el almacenamiento de usuario o "deviceId" para escenarios de IoT. En el caso de datos de serie temporal, se usa "timestamp" como clave de partición ya que los datos se suelen insertar y buscar por intervalos de tiempo. Aunque es habitual usar una sola propiedad, podría ser una propiedad diferente para distintos tipos de documentos, por ejemplo, usar "id" para documentos de usuario y "ownerUserId" para comentarios. El paso siguiente es enrutar todas las operaciones (como crear y consultar) a las colecciones adecuadas con la clave de partición que se incluye en una solicitud.

Veamos estas técnicas más detalladamente.

## Creación de particiones por rangos

En la creación de particiones por rangos, las particiones se asignan en función de si la clave de partición está dentro de un rango determinado. Esto se utiliza normalmente para las particiones con propiedades *time stamp*, por ejemplo, la hora de un evento (eventTime) entre el 1 y el 2 de febrero de 2015.

> [AZURE.TIP] La partición por rangos se debe usar si las consultas están restringidas a determinados rangos de valores con respecto a la clave de partición.

Un caso especial de la creación de particiones por rangos se da cuando el rango es un solo valor. Normalmente, se usa para crear particiones por valores discretos como región (por ejemplo, la partición correspondiente a Escandinavia contiene Noruega, Dinamarca y Suecia).

> [AZURE.TIP] Las particiones por rangos ofrecen el máximo grado de control en la administración de una aplicación multiempresa. Puede asignar varios inquilinos a una sola colección, un solo inquilino a una sola colección o, incluso, un solo inquilino a varias colecciones.

## Creación de particiones por hash

En la creación de particiones por hash, las particiones se asignan en función del valor de una función hash, lo que permite distribuir uniformemente las solicitudes y los datos entre una cantidad determinada de particiones. Normalmente, se usa para la partición de datos producidos o consumidos en un gran número de clientes distintos, y resulta útil para almacenar perfiles de usuario, elementos de catálogo y datos de telemetría de dispositivos de IoT ("Internet de las cosas").

> [AZURE.TIP] La creación de particiones por hash se debe usar siempre que haya demasiadas entidades para enumerar (por ejemplo, si se trata de usuarios o dispositivos) y también cuando la tasa de solicitudes sea bastante uniforme entre las entidades.

## Elección de la técnica adecuada para crear las particiones

Entonces, ¿qué técnica de partición es la adecuada para usted? Todo depende del tipo de datos y de sus patrones de acceso habituales. El hecho de elegir la técnica adecuada para crear las particiones al efectuar el diseño permite evitar la "deuda técnica" y controlar el crecimiento del tamaño de los datos y los volúmenes de solicitudes.

- La **creación de particiones por rangos** se usa generalmente en el contexto de las fechas, puesto que ofrece un mecanismo fácil y natural para determinar la antigüedad de las particiones en función de la marca de tiempo. También es útil cuando las consultas suelen estar limitadas a un rango de tiempo, ya que este se alinea con los límites de la partición. También permite agrupar y organizar conjuntos de datos desordenados y sin relacionar de una forma natural, por ejemplo, agrupar los inquilinos por organización o los estados por región geográfica. El rango también ofrece un control detallado a la hora de migrar los datos entre una colección y otra. 
- La creación de **particiones por hash** es útil a fin de lograr un equilibrio de carga uniforme de las solicitudes y hacer un uso eficaz de la capacidad de proceso y del almacenamiento aprovisionado. El uso *coherente de algoritmos de hash* permite minimizar la cantidad de datos que se deben mover cuando se agrega o elimina una partición.

No tiene por qué elegir solo una técnica de partición. Una *combinación* de estas técnicas también puede ser útil, según la situación. Por ejemplo, si almacena los datos de telemetría de un vehículo, un buen enfoque podría ser dividir los datos de telemetría del dispositivo por rangos según la marca de tiempo para facilitar la administración de las particiones y, a continuación, subdividir los datos según el número de identificación del vehículo con el fin de lograr un escalado horizontal para la capacidad de proceso (partición combinada por rango y hash).

## Desarrollo de una aplicación con particiones
Hay que tener en cuenta tres áreas de diseño clave a la hora de desarrollar una aplicación particionada en DocumentDB.

- Forma de enrutar las creaciones y lecturas (incluidas las consultas) hacia las colecciones adecuadas.
- Forma de almacenar y recuperar la configuración de la resolución de las particiones, también conocido como "mapa de particiones".
- Forma de agregar o eliminar particiones a medida que aumenta el volumen de datos.

Profundicemos un poco en estas áreas.

## Enrutamiento de creaciones y consultas

El enrutamiento de solicitudes de creación de documentos es directo para la creación de particiones por hash y por rangos. El documento se crea en la partición a partir del valor de hash o del rango que corresponda a la clave de partición.

Normalmente, las consultas y las lecturas deberían tener un ámbito de una clave de partición única, por lo que las consultas pueden aplicarse solo a las particiones coincidentes. Las consultas referidas a todos los datos, sin embargo, requieren la *dispersión* de la solicitud por varias particiones y la posterior combinación de los resultados. Tenga en cuenta que es posible que algunas consultas tengan que aplicar una lógica personalizada para combinar los resultados, por ejemplo, a la hora recuperar los N resultados principales.

## Administración del mapa de particiones

También hay que determinar cómo se va a almacenar el mapa de particiones, cómo van a cargar y recibir actualizaciones los clientes cuando haya cambios y cómo se comparten entre varios clientes. Si el mapa de particiones no cambia a menudo, puede guardarlo simplemente en el archivo de configuración de la aplicación.

De lo contrario, puede guardarlo en cualquier almacén persistente. Un patrón de diseño habitual que hemos visto en producción consiste en serializar los mapas de particiones como JSON y almacenarlos también en colecciones de DocumentDB. Después, los clientes pueden almacenar en caché el mapa con el fin de evitar las idas y vueltas adicionales y, a continuación, efectuar sondeos periódicamente sobre los cambios. Si los clientes modifican el mapa de particiones, asegúrese de que utilicen un esquema de nomenclatura coherente y concurrencia optimista (eTags) para posibilitar actualizaciones coherentes en el mapa de particiones.

## Adición y eliminación de particiones para escalado de datos

Con DocumentDB, puede agregar y quitar colecciones en cualquier momento y usarlas para almacenar nuevos datos entrantes, así como reequilibrar los datos disponibles en las colecciones existentes. Consulte la página [Límites](documentdb-limits.md) para conocer el número de colecciones. También puede llamarnos siempre que desee aumentar dichos límites.

Es muy fácil agregar y eliminar una nueva partición con el proceso de creación de particiones por rango. Por ejemplo, para agregar una nueva región geográfica o un nuevo rango de tiempo para los datos recientes, basta con anexar las particiones nuevas al mapa de particiones. Para dividir una partición existente en varias particiones o mezclar dos particiones se requiere un poco más esfuerzo. Hay que hacer lo siguiente:

- Desconectar la partición para las lecturas.
- Enrutar las lecturas a las particiones (usando la antigua configuración de particiones) y también a la nueva configuración de particiones durante la migración. Tenga en cuenta que las garantías de nivel de coherencia y las transacciones no estarán disponibles hasta que se complete la migración.

El método hash es relativamente más complicado para agregar y eliminar particiones. Las técnicas de hash simples provocan un orden aleatorio. Además, requieren la mayoría de los datos para moverse. El uso de **hashing de forma coherente** garantiza que solo hay que mover una fracción de los datos.

Una manera relativamente fácil de agregar nuevas particiones sin necesidad de mover datos es "derramar" los datos a una nueva colección y, a continuación, dispersar las solicitudes entre la colección nueva y la antigua. Sin embargo, este enfoque, debe usarse en pocas ocasiones (por ejemplo, para repartir los datos en momentos de picos de trabajo y para almacenar los datos temporalmente hasta que se puedan mover).

## Pasos siguientes
En este artículo, hemos presentado algunas técnicas habituales para efectuar particiones de datos con DocumentDB y hemos señalado cuándo se debe usar cada técnica o combinación de técnicas.

-   A continuación, eche un vistazo a este [artículo](documentdb-sharding.md) sobre cómo se pueden dividir los datos mediante resoluciones de partición con el SDK de DocumentDB. 
-   Descargue uno de los [SDK admitidos](https://msdn.microsoft.com/library/azure/dn781482.aspx)
-   Póngase en contacto con nosotros a través de los [foros de soporte técnico de MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB) si tiene alguna pregunta.
   


 

<!---HONumber=AcomDC_0211_2016-->