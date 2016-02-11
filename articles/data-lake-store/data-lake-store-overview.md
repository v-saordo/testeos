<properties 
   pageTitle="Información general del Almacén de Azure Data Lake | Azure" 
   description="Comprenda qué es el Almacén de Azure Data Lake y el valor que proporciona en comparación con otros almacenes de datos" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/22/2016"
   ms.author="nitinme"/>

# Información general del Almacén de Azure Data Lake

El Almacén de Azure Data Lake es un repositorio de gran escala en toda la empresa para cargas de trabajo de análisis de macrodatos. Azure Data Lake permite capturar datos de cualquier tamaño, tipo y velocidad de ingesta en un único lugar para realizar análisis exploratorios y operativos.

> [AZURE.TIP] Use la [vía de aprendizaje del Almacén de Data Lake](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) para empezar a explorar el servicio Almacén de Azure Data Lake.

Se puede acceder al Almacén de Azure Data Lake desde Hadoop (disponible con el clúster de HDInsight) mediante las API de REST compatibles con WebHDFS. Está diseñado específicamente para habilitar el análisis de los datos almacenados y está optimizado para el rendimiento en escenarios de análisis de datos. De forma inmediata, incluye todas las capacidades de nivel empresarial –seguridad, facilidad de administración, escalabilidad, confiabilidad y disponibilidad– esenciales para los casos de uso empresariales reales.


![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Entre las capacidades clave de Azure Data Lake, se incluyen las siguientes.

### Creado para Hadoop

El Almacén de Azure Data Lake es un sistema de archivos de Apache Hadoop compatible con el Sistema de archivos distribuido de Hadoop (HDFS) que funciona con el ecosistema de Hadoop. Las aplicaciones o los servicios de HDInsight existentes que usen la API de WebHDFS se pueden integrar fácilmente con el Almacén de Data Lake. Además, el Almacén de Data Lake expone una interfaz de REST compatible con WebHDFS para aplicaciones.

Los datos almacenados en el Almacén de Data Lake se pueden analizar fácilmente mediante marcos analíticos de Hadoop como MapReduce o Hive. Los clústeres de HDInsight de Microsoft Azure se pueden aprovisionar y configurar para que accedan directamente a datos almacenados en el Almacén de Data Lake.

### Almacenamiento ilimitado, archivos de petabytes de tamaño

El Almacén de Azure Data Lake proporciona almacenamiento ilimitado y es adecuado para almacenar diversos datos para análisis. No se impone ningún límite al tamaño de cuenta, el tamaño de archivo o la cantidad de datos que se pueden almacenar en un Data Lake. El tamaño de los archivos individuales puede oscilar entre kilobytes y petabytes, por lo que es una buena opción para almacenar cualquier tipo de datos. Los datos se almacenan de forma duradera mediante la realización de varias copias y no hay ningún límite al período de tiempo durante el que se pueden almacenar los datos en Data Lake.

### Rendimiento optimizado para el análisis de macrodatos

El Almacén de Azure Data Lake se creó para ejecutar sistemas de análisis a gran escala que requieren un procesamiento masivo con el fin de consultar y analizar grandes cantidades de datos. Data Lake distribuye partes de un archivo entre varios servidores de almacenamiento individuales. Esto mejora el rendimiento de lectura cuando se lee el archivo en paralelo para realizar análisis de datos.


### Listo para la empresa: Alta disponibilidad y seguridad

El Almacén de Azure Data Lake proporciona confiabilidad y disponibilidad estándar del sector. Los recursos de datos se almacenan de forma duradera realizando copias redundantes para protegerse ante los errores inesperados. Las empresas pueden usar Azure Data Lake en sus soluciones como parte importante de su plataforma de datos existente.

Además, el Almacén de Data Lake también proporciona seguridad de nivel empresarial para los datos almacenados. Para obtener más información, consulte [Protección de los datos almacenados en el Almacén de Azure Data Lake](#DataLakeStoreSecurity).


### Todos los datos

El Almacén de Azure Data Lake puede almacenar cualquier dato en su formato nativo, tal cual, sin necesidad de transformarlo antes. El Almacén de Data Lake no requiere la definición de un esquema antes de que se carguen los datos, sino que deja que cada marco analítico interprete los datos y defina un esquema en el momento del análisis. La capacidad de almacenar archivos de formatos y tamaños arbitrarios hace posible que el Almacén de Data Lake administre datos estructurados, semiestructurados y no estructurados.

Los contenedores de datos de Almacén de Azure Data Lake son básicamente carpetas y archivos. Se opera en los datos almacenados mediante los SDK, el Portal de Azure y Azure Powershell. Siempre que ponga los datos en el almacén mediante estas interfaces y los contenedores adecuados, puede almacenar cualquier tipo de datos. Almacén de Data Lake no realiza ningún control especial de datos según el tipo de datos que almacena.


## <a name="DataLakeStoreSecurity"></a>Protección de los datos en el Almacén de Azure Data Lake

El Almacén de Azure Data Lake usa Azure Active Directory para la autenticación y listas de control de acceso (ACL) para administrar el acceso a los datos.

| Característica | Descripción |
|-----------------------------------------|------------------------------------------|
| Autenticación | El Almacén de Azure Data Lake se integra con Azure Active Directory (AAD) para la administración de identidad y acceso para todos los datos almacenados en el Almacén de Azure Data Lake. Como resultado de la integración, Azure Data Lake se beneficia de todas las características de AAD, lo que incluye la autenticación multifactor, el acceso condicional, el control de acceso basado en roles, la supervisión del uso de aplicaciones, la supervisión y las alertas de seguridad, etc. El Almacén de Azure Data Lake es compatible con el protocolo OAuth 2.0 para la autenticación en la interfaz de REST. |
| Control de acceso | El Almacén de Azure Data Lake proporciona control de acceso gracias a la compatibilidad con los permisos de estilo POSIX expuestos por el protocolo WebHDFS. En la versión actual, los permisos pueden especificarse en el nivel de Data Lake y se aplicarán a todos los archivos y carpetas en Data Lake. Para actualizaciones futuras, habilitaremos el control de acceso específico, en el que se permite especificar los permisos en archivos y carpetas individuales.|

Para obtener instrucciones sobre cómo proteger datos en el Almacén de Data Lake, consulte [Protección de los datos almacenados en el Almacén de Azure Data Lake](data-lake-store-secure-data.md).

## Aplicaciones compatibles con el Almacén de Azure Data Lake

Consulte la página con las [aplicaciones y servicios compatibles con Azure Data Lake](data-lake-store-compatible-oss-other-applications.md) para obtener una lista de aplicaciones de código abierto interoperables con el Almacén de Azure Data Lake. Consulte la página sobre la [integración con otros servicios de Azure](data-lake-store-integrate-with-other-services.md) para entender cómo el Almacén de Data Lake puede usarse con otros servicios de Azure para hacer posible una gama más amplia de escenarios.

## ¿Qué es el sistema de archivos del Almacén de Azure Data Lake (adl://)?

Se puede acceder al Almacén de Data Lake mediante el nuevo sistema de archivos, AzureDataLakeFilesystem (adl://), en entornos de Hadoop (disponibles con el clúster de HDInsight). Las aplicaciones y los servicios que usan adl:// pueden aprovechar aún más la optimización del rendimiento que no está actualmente disponible en WebHDFS. Como resultado, el Almacén de Data Lake ofrece la flexibilidad de elegir entre disponer del mejor rendimiento con la opción recomendada que usa adl:// o mantener el código existente usando la API de WebHDFS directamente. HDInsight de Azure aprovecha completamente AzureDataLakeFilesystem para proporcionar el mejor rendimiento en el Almacén de Data Lake.

Puede acceder a los datos en el Almacén de Data Lake mediante `adl://<data_lake_store_name>.azuredatalakestore.net`. Para obtener más información sobre cómo acceder a los datos del Almacén de Data Lake, consulte [Propiedades y acciones disponibles en los datos almacenados](data-lake-store-get-started-portal.md#properties).

## ¿Cómo comenzar a usar el Almacén de Azure Data Lake?

Consulte [Introducción al Almacén de Azure Data Lake mediante el Portal de Azure](data-lake-store-get-started-portal.md) para saber cómo aprovisionar un Almacén de Data Lake mediante el Portal de Azure. Una vez que aprovisione Azure Data Lake, puede aprender a usar productos de macrodatos tales como Análisis de Azure Data Lake o HDInsight de Azure con el Almacén de Data Lake. También puede crear una aplicación .NET para crear una cuenta de Almacén de Azure Data Lake y realizar operaciones como cargar datos, descargar datos, etc.

- [Tutorial: Introducción a Análisis de Azure Data Lake mediante el Portal de vista previa de Azure](data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Introducción al Almacén de Azure Data Lake mediante SDK de .NET](data-lake-store-get-started-net-sdk.md)
  

<!---HONumber=AcomDC_0128_2016-->