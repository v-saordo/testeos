<properties 
    pageTitle="Casos de uso comunes de DocumentDB | Microsoft Azure" 
    description="Obtenga información acerca de los cinco principales casos de uso para DocumentDB: contenido generado por usuarios, registro de eventos, datos del catálogo, datos de las preferencias del usuario e Internet de las cosas (IoT)." 
    services="documentdb" 
    authors="h0n" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/10/2016" 
    ms.author="hawong"/>

# Casos de uso comunes de DocumentDB
En este artículo se proporciona información general acerca de varios casos de uso comunes de DocumentDB. Las recomendaciones de este artículo sirven como punto de partida para desarrollar su aplicación con DocumentDB.

Después de leer este artículo, podrá responder a las preguntas siguientes:
 
- ¿Cuáles son los casos de uso comunes de DocumentDB?
- ¿Cuáles son las ventajas de usar DocumentDB como almacén de contenido generado por usuarios?
- ¿Cuáles son las ventajas de utilizar DocumentDB como almacén de datos de catálogo?
- ¿Cuáles son las ventajas de usar DocumentDB como almacén del registro de eventos?
- ¿Cuáles son las ventajas de utilizar DocumentDB como almacén de datos de las preferencias de usuario?
- ¿Cuáles son las ventajas de usar DocumentDB como almacén de datos para los sistemas de Internet de las cosas (IoT)?

    
## Contenido generado por usuarios
Un caso de uso común para DocumentDB es almacenar y consultar el contenido generado por usuarios para aplicaciones web y móviles, especialmente aplicaciones de medios sociales. Algunos ejemplos de contenido generado por usuarios son las sesiones de chat, los tweets, las entradas de blog, las valoraciones y los comentarios. A menudo, el contenido generado por usuarios en las aplicaciones de medios sociales es una mezcla de texto sin formato, propiedades, etiquetas y relaciones que no están limitadas por una estructura rígida.

Los contenidos como chats, comentarios y publicaciones pueden almacenarse en DocumentDB sin requerir transformaciones u objetos complejos en las capas de asignación relacional. Se pueden agregar o modificar fácilmente las propiedades de datos para satisfacer los requisitos a medida que los desarrolladores recorran en iteración el código de la aplicación, permitiendo así un rápido desarrollo.

Las aplicaciones que se integran con varias redes sociales deben responder a los esquemas cambiantes de estas redes. Como los datos se indizan automáticamente de forma predeterminada en DocumentDB, los datos están listos para ser consultados en cualquier momento. Por lo tanto, estas aplicaciones tienen la flexibilidad para recuperar las proyecciones en función de sus necesidades respectivas.

Muchas de las aplicaciones sociales se ejecutan a escala global y pueden presentar patrones de uso impredecibles. La flexibilidad en el escalado del almacén de datos es esencial, ya que el nivel de la aplicación se escala para satisfacer la demanda de uso. Es posible escalar horizontalmente mediante la adición de particiones de datos adicionales a una cuenta de DocumentDB. Además, también puede crear cuentas adicionales de DocumentDB en varias regiones. Para ver la disponibilidad del servicio de DocumentDB por regiones, consulte [Regiones de Azure](https://azure.microsoft.com/regions/#services).

## Datos del catálogo
Entre los escenarios de uso de los datos de catálogo se encuentra el almacenamiento y la consulta de un conjunto de atributos para entidades como personas, lugares y productos. Algunos ejemplos de datos de catálogo son cuentas de usuario, catálogos de productos, registros de dispositivo para Internet de las cosas y sistemas de lista de materiales. Los atributos de estos datos pueden variar y cambiar con el tiempo para satisfacer los requisitos de la aplicación.

Piense por ejemplo en un catálogo de productos para un proveedor de piezas para vehículos. Cada pieza puede tener sus propios atributos además de los atributos comunes que comparten todas las piezas. Además, los atributos de una pieza específica pueden cambiar al año siguiente, cuando se lance un nuevo modelo. Como almacén de documentos JSON, DocumentDB admite los esquemas flexibles y permite representar datos con propiedades anidadas, y por ello es adecuado para almacenar los datos del catálogo.

## Datos del registro
El registro de aplicaciones a menudo se emite en grandes volúmenes y puede tener atributos variables en función de la versión de la aplicación implementada o los eventos de registro de los componentes. Los datos del registro no están limitados por relaciones complejas o estructuras rígidas. Los datos del registro se guardan en formato JSON cada vez más, dado que se trata de un formato y ligero y de fácil lectura para los humanos.
   
Normalmente, hay dos casos de uso principales relacionados con los datos del registro de eventos. El primer caso de uso es realizar consultas ad hoc en un subconjunto de datos para solucionar problemas. Durante la solución de problemas, lo primero es recuperar un subconjunto de datos desde los registros, normalmente por serie temporal. A continuación, se realiza una exploración en profundidad filtrando el conjunto de datos por niveles de error o mensajes de error. Es aquí donde el almacenamiento de registros de eventos en DocumentDB supone una ventaja. Los datos de registro almacenados en DocumentDB se indizan automáticamente de forma predeterminada y, por tanto, están listos para recibir consultas en cualquier momento. Además, los datos del registro pueden conservarse entre las particiones de datos como una serie de tiempo. Los registros antiguos pueden llevarse al almacenamiento en frío según la directiva de retención.

El segundo caso de uso se refiere a los trabajos de análisis de datos de larga ejecución realizados sin conexión sobre un volumen grande de datos de registro. Entre los ejemplos de este caso de uso se incluye el análisis de disponibilidad del servidor, el análisis de errores de aplicación y el análisis de datos clickstream. Normalmente, se usa Hadoop para realizar estos tipos de análisis. Con el conector de Hadoop para DocumentDB, las bases de datos de DocumentDB funcionan como orígenes y receptores de datos para trabajos de Pig, Hive y Map/Reduce. Para obtener detalles sobre el conector de Hadoop para DocumentDB, consulte [Ejecución de un trabajo de Hadoop con DocumentDB y HDInsight](documentdb-run-hadoop-with-hdinsight.md).

## Datos de las preferencias del usuario
En la actualidad, las aplicaciones web y móviles más modernas incluyen experiencias y vistas complejas. Estas vistas y experiencias son normalmente dinámicas, encargándose de las preferencias o los estados de ánimo del usuario y las necesidades de personalización de marca. Por lo tanto, las aplicaciones requieren la capacidad de recuperar la configuración personalizada eficazmente para representar elementos de interfaz de usuario y experiencias rápidamente.

JSON es un formato eficaz para representar datos de diseño de la interfaz de usuario que no solo es ligero, sino que también se puede interpretar fácilmente mediante JavaScript. DocumentDB ofrece niveles de coherencia ajustables que permiten lecturas rápidas con escrituras de baja latencia. Por lo tanto, el almacenamiento de datos de diseño de la interfaz de usuario, incluida la configuración personalizada como documentos JSON en DocumentDB, es un medio eficaz para obtener estos datos a través de la red.

## Datos de sensores de dispositivos
Los casos de uso de IoT comparten a menudo algunos patrones en el modo de recopilar, procesar y almacenar datos. En primer lugar, estos sistemas permiten asumir ráfagas de datos procedentes de sensores de dispositivos de distintas configuraciones regionales. A continuación, estos sistemas procesan y analizan datos de streaming para obtener perspectivas en tiempo real. Y por último, pero no por ello menos importante, la mayor parte de los datos, si no todos, se almacenarán eventualmente en un almacén de datos para realizar análisis sin conexión y consultas ad hoc.

Microsoft Azure ofrece servicios completos que se pueden aprovechar para casos de uso de IoT. Los servicios de IoT de Azure son un conjunto de servicios que incluyen Centros de eventos de Azure, Azure DocumentDB, Análisis de transmisiones de Azure, Centro de notificaciones de Azure, Aprendizaje automático de Azure, Azure HDInsight y PowerBI.

Las ráfagas de datos pueden ser asumidas por los Centros de eventos de Azure, ya que ofrecen una ingesta de datos de alto rendimiento con baja latencia. Los datos recibidos que requieran procesamiento para obtener información en tiempo real se pueden dirigir a Análisis de transmisiones de Azure para realizar el análisis en tiempo real. Los datos pueden cargarse en DocumentDB para realizar consultas ad hoc. Una vez que los datos se carguen en DocumentDB, estarán listos para recibir consultas. Los datos de DocumentDB pueden utilizarse como datos de referencia en el proceso de análisis en tiempo real. Además, los datos pueden afinarse y procesarse aún más conectando los datos de DocumentDB a HDInsight para trabajos de Pig, Hive o Map/Reduce Los datos afinados se cargan luego de nuevo en DocumentDB para la creación de informes.

Para ver una solución de IoT de ejemplo utilizando DocumentDB, EventHubs y Storm, consulte el [repositorio de ejemplos de hdinsight-storm en GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Para obtener más información sobre las posibilidades de Azure para IoT, consulte [Cree el Internet de sus cosas](http://www.microsoft.com/es-ES/server-cloud/internet-of-things.aspx).

## Pasos siguientes
 
Para empezar a usar DocumentDB, puede crear una [cuenta](https://azure.microsoft.com/pricing/free-trial/) y, a continuación, seguir nuestra [ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para obtener información sobre DocumentDB y buscar la información que necesita.

O bien, si desea leer más acerca de los usuarios que emplean DocumentDB, tiene a su disposición los siguientes testimonios de clientes:

- [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Integrador líder del sector ofrece a empresas multinacionales perspectivas globales en minutos con tecnologías en la nube flexibles.
- [News Republic](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Incorporación de inteligencia a las noticias para proporcionar información con propósito a ciudadanos comprometidos. 
- [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Para obtener un color uniforme en todo el mundo, las principales marcas recurren a SGS. Y SGS recurre a Azure.
- [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Telenor, empresa líder a nivel global, usa la nube para moverse con la velocidad de una startup. 
- [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). El almacén del futuro se ejecuta con la rapidez en la búsqueda y la sencillez en el flujo de datos.
 

<!---HONumber=AcomDC_0211_2016-->