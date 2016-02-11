<properties
   pageTitle="Catálogo de datos de Azure: ¿Qué es el catálogo de datos?"
   description="Información general del Catálogo de datos de Microsoft Azure, incluidas sus características y los problemas que está diseñado para solucionar. Catálogo de datos de Azure proporciona capacidades que permiten a cualquier usuario (desde analistas a científicos de datos y desarrolladores) registrar, detectar, comprender y consumir orígenes de datos."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="12/18/2015"
   ms.author="maroche"/>

# ¿Qué es el Catálogo de datos de Azure?

El **Catálogo de datos de Microsoft Azure** es un servicio en la nube totalmente administrado que actúa como un sistema de registro y sistema de detección para orígenes de datos empresariales. **Catálogo de datos de Azure ** proporciona capacidades que permiten a cualquier usuario (desde analistas hasta científicos de datos y desarrolladores) detectar, comprender y consumir orígenes de datos y colaborar con sus conocimientos para crear y dar soporte a una comunidad y cultura de datos.

## Descripción del problema: información general y motivación

Tradicionalmente, la detección de orígenes de datos empresariales ha sido un proceso orgánico basado en conocimiento tribal. Esto presenta varios desafíos para las compañías que desean sacar el máximo partido de sus recursos de información.

-	Los usuarios no son conscientes de que existen orígenes de datos, a menos que entren en contacto con él como parte de otro proceso; no hay ninguna ubicación central en la que se registren los orígenes de datos.
-	A menos que un usuario conozca la ubicación de un origen de datos, no se puede conectar a los datos mediante una aplicación de cliente; las experiencias del consumo de datos requieren que los usuarios conozcan la cadena de conexión o la ruta de acceso.
-	A menos que un usuario conozca la ubicación de la documentación de un origen de datos, no podrá entender los usos previstos; los orígenes de datos y la documentación residen en lugares diferentes y se consumen a través de diferentes experiencias.
-	Si un usuario tiene preguntas acerca de un recurso de información, deberá localizar al experto o a un equipo responsable de los datos y contactar con esos expertos sin conexión; no hay ninguna conexión explícita entre los datos y aquellos con perspectivas expertas en su uso.
-  A menos que un usuario comprenda el proceso de solicitar acceso al origen de datos, la detección del origen de datos y su documentación siguen sin permitirle tener acceso a los datos que requiere.

Aunque estos desafíos se enfrentan a los consumidores de datos, los usuarios responsables de producir y mantener recursos de información se enfrentan a sus propios desafíos.

-	Anotar los orígenes de datos con metadatos descriptivos suele ser un esfuerzo perdido; las aplicaciones cliente suelen omitir las descripciones que se almacenan en el origen de datos.
-	La creación de documentación para orígenes de datos suele ser un esfuerzo perdido; mantener la documentación en sincronización con el origen de datos es una responsabilidad continuada y los usuarios no tienen confianza en la documentación, ya que a menudo se percibe como obsoleta.
- La restricción del acceso al origen de datos y la garantía de que los consumidores de datos saben cómo solicitar el acceso suponen un desafío continuo.

Crear y mantener la documentación para un origen de datos es complejo y lento. El desafío de conseguir que dicha documentación esté disponible para cualquier persona que use el origen de datos a menudo lo es en mayor medida.

Cuando se combinan, estos desafíos presentan una barrera importante para las empresas que desean para estimular y fomentar el uso y la comprensión de los datos empresariales.

## Descripción del servicio

**Catálogo de datos de Azure** está diseñado para abordar estos problemas y permitir a las empresas sacar el máximo partido de sus recursos de información existentes, haciéndolos fácilmente reconocibles y comprensibles para los usuarios que necesitan los datos que administran.

**Catálogo de datos de Azure** proporciona un servicio basado en la nube en el que se pueda registrar el origen de los datos. Los datos permanecen en su ubicación existente, pero se agrega una copia de los metadatos, junto con una referencia a la ubicación del origen de datos a **Catálogo de datos de Azure**. Estos metadatos también se indexan para conseguir que cada origen de datos se pueda detectar fácilmente a través de la búsqueda y puedan ser comprensibles para los usuarios que lo detecten.

Una vez que se ha registrado un origen de datos, a continuación sus metadatos se pueden enriquecer, por parte del usuario que realizó el registro o por parte de otros usuarios de la empresa. Cualquier usuario puede anotar un origen de datos proporcionando descripciones, etiquetas u otros metadatos, como la documentación y procesos para solicitar acceso al origen de datos. Estos metadatos descriptivos complementan a los metadatos estructurales (como nombres de columna y tipos de datos) registrados desde el origen de datos, para que sean más fáciles de detectar y comprender.

El descubrimiento y comprensión de los orígenes de datos y su uso es el propósito principal de registrar los orígenes. Cuando los usuarios de empresa necesitan datos para sus esfuerzos (que podrían ser de inteligencia empresarial, desarrollo de aplicaciones, ciencia de los datos o cualquier otra tarea donde se requieren los datos correctos) pueden usar la experiencia de detección del **Catálogo de datos de Azure** para encontrar rápidamente los datos que se ajusten a sus necesidades, comprender los datos para evaluar su adecuación para un propósito y consumir datos abriendo el origen de datos en la herramienta que desee. Al mismo tiempo, **Catálogo de datos de Azure** permite a los usuarios contribuir al catálogo mediante el etiquetado, documentación y anotación de orígenes de datos que ya se han registrado y registrando nuevos orígenes de datos que puede detectar, comprender y consumir la comunidad de usuarios del catálogo.

![Capacidades del Catálogo de datos de Azure](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## Registrar orígenes de datos

El registro de orígenes de datos se realiza mediante la herramienta de registro de orígenes de datos del **Catálogo de datos de Azure**. Esta aplicación puede descargarse desde el portal del **Catálogo de datos de Azure**.

El proceso de registro implica tres pasos básicos:

1.	Conectarse a un origen de datos: el usuario especifica la ubicación del origen de datos y las credenciales para conectarse al origen de datos, como una instancia de SQL Server.
2.	Seleccionar objetos para registrar: el usuario selecciona los objetos en la ubicación especificada que se debe registrar con **Catálogo de datos de Azure**. Esto puede ser el conjunto completo de las tablas de todas las bases de datos del servidor, o un subconjunto seleccionado específicamente de tablas y vistas.
3.	Completar el registro: el usuario completa el proceso y la herramienta de registro del origen de datos extrae los metadatos estructurales del origen de datos y envía los metadatos al servicio en la nube del **Catálogo de datos de Azure**.

> [AZURE.NOTE]Para la vista previa, actualmente **Catálogo de datos de Azure** admite los siguientes tipos de recursos y orígenes de datos:

- Tabla de SQL Server
- Vista de SQL Server
- Tabla de base de datos de Oracle
- Vista de la Base de datos de Oracle
- Tabla de Teradata
- Vista de Teradata
- Dimensión multidimensional de SQL Server Analysis Services
- Medida multidimensional de SQL Server Analysis Services
- KPI multidimensional de SQL Server Analysis Services
- Tabla tabular de SQL Server Analysis Services
- Informe de SQL Server Reporting Services
- Blob de almacenamiento de Azure
- Directorio de almacenamiento de Azure
- Archivo HDFS
- Directorio HDFS
- Tabla de Hive
- Archivo del Almacén de Azure Data Lake
- Directorio del Almacén de Azure Data Lake
- Tabla de MySQL
- Vista de MySQL

Se agregarán orígenes de datos y tipos de activos adicionales durante la vista previa del **Catálogo de datos de Azure**.

> [AZURE.IMPORTANT]Registrar un origen de datos en el **Catálogo de datos de Azure** no copia los datos del origen de datos, a menos que seleccione "Incluir vista previa" en la herramienta de registro del origen de datos. El registro de copia los metadatos del origen de datos, no los datos. Entre los ejemplos de los metadatos se incluyen los nombres de las tablas y otros objetos de origen de datos, junto con los nombres y tipos de datos de columnas y otros atributos de orígenes de datos. Los metadatos también incluyen la ubicación del origen de datos, para que los usuarios que detectan el origen de datos usando el **Catálogo de datos de Azure** puedan conectarse a continuación al origen de datos. Si selecciona "Incluir vista previa", la herramienta de registro de origen de datos también copiará en el **Catálogo de datos de Azure** un pequeño conjunto de registros que se mostrará a los usuarios que detecten el origen de datos en el portal del **Catálogo de datos de Azure**.

## Enriquecer los metadatos del origen de datos

Cuando se complete el registro, se pueden detectar y consumir los orígenes de datos, pero el verdadero valor del **Catálogo de datos de Azure** reside en disponer de metadatos de empresa descriptivos en la misma experiencia que los metadatos estructurales extraídos del origen de datos. Estos metadatos adicionales ofrecen tres ventajas importantes:

-	Los orígenes de datos registrados son más fácilmente detectables. Los metadatos ofrecidos por el usuario se agregan al índice de búsqueda del **Catálogo de datos de Azure**. Esto permite a los usuarios descubrir los datos mediante el uso de términos y conceptos que es posible que no estén presentes en el origen de datos original. Por ejemplo, si una tabla de base de datos que contiene datos del consumidor se denomina "tbl\_c45", proporcionar el nombre descriptivo "Cliente" provocará que se pueda detectar más fácilmente por parte de los usuarios que buscan los datos del cliente. Del mismo modo, proporcionar una descripción que incluye los nombres de los informes, paneles o procesos que usan los datos hará que el origen de datos sea más fácil de encontrar para los usuarios que usan dichos artefactos de bajada como sus términos de búsqueda.
-	Los orígenes de datos registrados son más fáciles de comprender una vez detectados. Los metadatos ofrecidos por el usuario se presentan para cualquier usuario del **Catálogo de datos de Azure** que vea el origen de datos anotado, lo cual ayuda a ofrecer contexto e información adicionales. Normalmente, la mayoría de los orígenes de datos no incluyen descripciones significativas ni documentación, y los que lo hacen a menudo se centran en las audiencias técnicas de desarrollador de bases de datos o DBA. Mediante el enriquecimiento de orígenes de datos en el **Catálogo de datos de Azure** mediante descripciones y etiquetas adecuadas para la audiencia, los usuarios pueden ayudar a garantizar que los usuarios que detectan los datos puedan entender sus detalles y su uso previsto.
-  Cada origen de datos registrado puede incluir información de acceso a solicitudes, para que los usuarios pueden comprender con facilidad y seguir los procesos existentes para solicitar el acceso al origen de datos y sus datos.

> [AZURE.NOTE]Cada usuario del **Catálogo de datos de Azure** puede agregar sus propias etiquetas y descripciones para los atributos y recursos de datos. El **Catálogo de datos de Azure** realizará un seguimiento del valor y el origen de cada anotación y mostrará el usuario que lo agregó. Este enfoque de micromecenazgo de los metadatos garantiza que todos los usuarios con una perspectiva de los datos y su uso puedan compartir sus opiniones y recursos con la comunidad de usuarios en general.

## Explorar, descubrir y comprender

El objetivo de registrar y enriquecer los orígenes de datos en el **Catálogo de datos de Azure** es que se puedan detectar, comprender y usar por parte de los usuarios de la empresa. El portal del **Catálogo de datos de Azure** es la herramienta principal de este proceso.

El portal del **Catálogo de datos de Azure** ofrece dos mecanismos principales para la detección y exploración de los datos: la búsqueda y el filtrado.

Para buscar orígenes de datos en el **Catálogo de datos de Azure**, simplemente escriba un término de búsqueda en el cuadro de búsqueda del portal del **Catálogo de datos de Azure**. El portal mostrará un icono para cada origen de datos registrado que coincida con el término de búsqueda; los iconos contendrán el nombre, la descripción y las etiquetas asignadas al origen de datos, junto con otra información de alto nivel.

Para filtrar el contenido del **Catálogo de datos de Azure**, simplemente seleccione uno o varios de los filtros que se presentan en el portal del **Catálogo de datos de Azure**. Esto limitará los iconos que se muestran en el portal a solo aquellos que cumplen los criterios de filtro especificados. Puede filtrar los orígenes de datos sin buscar o puede filtrar los resultados de una búsqueda.

Para ver información más completa de un origen de datos y determinar si es adecuado para la tarea en cuestión, simplemente haga clic en el icono del origen de datos; se mostrará el panel de propiedades y contendrá todos sus metadatos.

En la parte superior del panel de propiedades habrá botones adicionales:

1.	Vista previa: si selecciona este botón aparecerá el conjunto estático de registros de vista previa del origen de datos si se ha seleccionado la vista previa durante el registro del origen de datos.
2.	Esquema: si selecciona este botón, aparecerá el esquema del origen de datos, incluidos los nombres de columna y los tipos de datos, así como los metadatos de nivel de columna en el **Catálogo de datos de Azure**.

> [AZURE.NOTE]Es importante recordar que la experiencia **Descubrir** puede ser un punto de entrada a la experiencia de **Enriquecer**, y no solo a la experiencia **Consumir**. El enfoque de micromecenazgo que aporta el **Catálogo de datos de Azure** permite que cualquier usuario que detecte un origen de datos registrado pueda compartir sus opiniones sobre los datos, además de usar los datos que ha detectado.

## Quitar los metadatos del origen de datos

Cuando se registra un origen de datos, a veces puede ser necesario quitar la referencia del origen de datos del **Catálogo de datos de Azure**. Esto puede ser debido a los requisitos empresariales cambiantes o a la retirada del sistema de origen. Independientemente del motivo, el **Catálogo de datos de Azure** facilita la eliminación de orígenes de datos, ya que solo es necesario seleccionarlos y eliminarlos, de forma que dejarán de poderse detectar y consumir.

> [AZURE.IMPORTANT]La eliminación de un origen de datos del **Catálogo de datos de Azure** solo borra los metadatos almacenados en el servicio del **Catálogo de datos de Azure**. El origen de datos original no se ve afectado en modo alguno.

## Consumir orígenes de datos

El objetivo final de la detección de datos es encontrar los datos que necesita y usarlos en la herramienta de datos de su elección. La experiencia de consumo de datos del Catálogo de datos de Azure permite esta capacidad de dos maneras.

1.	Para las aplicaciones cliente que sean directamente compatibles con el **Catálogo de datos de Azure**, los usuarios pueden hacer clic en el menú **Abrir en** del icono del origen de datos que se encuentra en el portal. A continuación, la aplicación cliente se iniciará con una conexión al origen de datos seleccionado.
2.	Para todas las aplicaciones cliente, los usuarios pueden usar la información de conexión que se muestra en el panel de propiedades para un origen de datos seleccionado. Esta información incluye todos los detalles (como el nombre del servidor, el nombre de la base de datos y el nombre del objeto) necesarios para conectarse a los datos y puede copiarse en la experiencia de conexión de la herramienta de cliente. Si se han dado detalles de solicitud de acceso para un origen de datos, esta información se mostrará junto a los detalles de conexión.

> [AZURE.NOTE]Para la vista previa privada del Catálogo de datos de Azure, de forma directa solo se admitirán y estarán disponibles Microsoft Excel y el Administrador de informes de SQL Server Reporting Services en el menú **Abrir en**.

<!---HONumber=AcomDC_1223_2015-->