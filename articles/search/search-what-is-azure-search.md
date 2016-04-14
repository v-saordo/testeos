<properties
	pageTitle="¿Qué es Búsqueda de Azure? | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Búsqueda de Azure es un servicio de búsqueda completamente administrado hospedado en la nube. Conozca más en la información general de esta característica."
	services="search"
	authors="ashmaka"
	documentationCenter=""/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="03/02/2016"
	ms.author="ashmaka"/>

# ¿Qué es Búsqueda de Azure?

Búsqueda de Azure es una solución de búsqueda como servicio en la nube que delega la administración de los servidores y la infraestructura a Microsoft, dejando así un servicio listo para usar que puede completar con sus propios datos y usar para buscar en la aplicación web o móvil. Búsqueda de Azure le permite agregar fácilmente una sólida experiencia de búsqueda a las aplicaciones usando una sencilla [API de REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) o [SDK de .NET](search-howto-dotnet-sdk.md) sin necesidad de administrar la infraestructura de búsqueda o convertirse en un experto en esta materia.

## Ofrezca a los usuarios una eficaz experiencia de búsqueda

Se pueden formular **consultas eficaces** con la [sintaxis de consulta simple](https://msdn.microsoft.com/library/azure/dn798920.aspx), que ofrece operadores lógicos, operadores de búsqueda de frase, operadores de sufijo y operadores de precedencia. Además, la [sintaxis de consulta Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) puede habilitar la búsqueda aproximada, la búsqueda de proximidad, la priorización de términos y las expresiones regulares. Búsqueda de Azure también admite analizadores léxicos personalizados para permitir que su aplicación administre consultas de búsqueda complejas mediante la coincidencia fonética y las expresiones regulares.

Se incluye **compatibilidad** con [56 idiomas diferentes](https://msdn.microsoft.com/library/azure/dn879793.aspx). Al usar analizadores de Lucene y analizadores de Microsoft (refinados gracias a varios años de procesamiento de lenguaje natural en Office y Bing), Búsqueda de Azure puede analizar el texto en el cuadro de búsqueda de la aplicación para controlar de manera inteligente la lingüística específica del idioma, como por ejemplo, tiempos verbales, género, nombres plurales irregulares (por ejemplo, 'mouse' frente a 'ratones'), separación de palabras compuestas, separación de palabras (para idiomas sin espacios) y mucho más.

Las **sugerencias de búsqueda** pueden habilitarse las barras de búsqueda de autocompletado y las consultas de escritura automática. [Se sugieren los documentos correspondientes en el índice](https://msdn.microsoft.com/library/azure/dn798936.aspx) a medida que los usuarios escriben entradas de búsqueda parciales.

**El resaltado de referencias** [permite](https://msdn.microsoft.com/library/azure/dn798927.aspx) a los usuarios ver el fragmento de texto de cada resultado que contenga las coincidencias para su consulta. Se puede elegir qué campos devuelven fragmentos resaltados.

**La navegación por facetas** se agrega fácilmente a la página de resultados de búsqueda con Búsqueda de Azure. Usando [simplemente un parámetro de consulta](https://msdn.microsoft.com/library/azure/dn798927.aspx), Búsqueda de Azure devolverá toda la información necesaria para construir una experiencia de búsqueda por facetas en la interfaz de usuario de la aplicación para permitir que los usuarios profundicen en los resultados de la búsqueda y los filtren (por ejemplo, filtrar elementos de un catálogo por rango de precios o marca).

**El soporte** [geoespacial](search-create-geospatial.md) permite procesar, filtrar y mostrar de forma inteligente las ubicaciones geográficas. Búsqueda de Azure permite a los usuarios explorar datos basados en la proximidad de un resultado de búsqueda a una ubicación determinada o en una región geográfica específica.

Los **filtros** se pueden usar para incorporar fácilmente una navegación por facetas en la interfaz de usuario de la aplicación, mejorar la formulación de consultas y filtrar según los criterios especificados por el usuario o el desarrollador. Cree filtros eficaces mediante la [sintaxis de OData](https://msdn.microsoft.com/library/azure/dn798921.aspx).

## Faculte a los desarrolladores con un servicio de fácil de usar

La **alta disponibilidad** garantiza una experiencia de servicio de búsqueda muy confiable. Cuando se escala correctamente, [Búsqueda de Azure ofrece un contrato de nivel de servicio del 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

**Completamente administrada** como una solución de un extremo a otro, Búsqueda de Azure no requiere absolutamente ninguna administración de la infraestructura. El servicio se puede adaptar fácilmente a sus necesidades mediante el escalado en dos dimensiones para controlar más almacenamiento de documentos, mayores cargas de consultas o ambos.

La **integración de datos** con [indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx) permite a Búsqueda de Azure rastrear automáticamente Base de datos SQL de Azure, Azure DocumentDB o [Almacenamiento de blobs de Azure](search-howto-indexing-azure-blob-storage.md) para sincronizar el contenido del índice de la búsqueda con el almacén de datos principal.

La **averiguación de documentos** está disponible (actualmente en versión preliminar) para [leer e indexar los principales formatos de archivo](search-howto-indexing-azure-blob-storage.md), por ejemplo, documentos de Microsoft Office, PDF y HTML.

El **análisis de tráfico de búsqueda** se [recopila y analiza fácilmente](search-traffic-analytics.md) para conocer qué usuarios están escribiendo en el cuadro de búsqueda.

La **puntuación simple** es una ventaja clave de Búsqueda de Azure. Los [perfiles de puntuación](https://msdn.microsoft.com/library/azure/dn798928.aspx) se utilizan para permitir que las organizaciones modelen la relevancia en función de los valores de los propios documentos. Por ejemplo, tal vez se desea que los productos más recientes o con descuento aparezcan arriba en los resultados de búsqueda. También se pueden crear perfiles de puntuación mediante etiquetas para puntuaciones personalizadas, según las preferencias de búsqueda de los clientes de las que se ha hecho seguimiento y se han almacenado por separado.

La **clasificación** se ofrece para varios campos mediante el esquema de índice y luego se activa o se desactiva en el momento de consulta con un único parámetro de búsqueda.

La **paginación** y limitación de los resultados de búsqueda se [aplican fácilmente con el control adaptado](search-pagination-page-layout.md) que Búsqueda de Azure ofrece a través de los resultados de búsqueda.

El **explorador de Búsqueda** permite emitir consultas sobre todos los índices directamente desde el Portal de Azure de su cuenta para que pueda probar consultas y perfeccionar los perfiles de puntuación fácilmente.

## Cómo funciona

### 1\. Servicio de aprovisionamiento
Puede poner en marcha un servicio de Búsqueda de Azure mediante el [Portal de Azure](https://portal.azure.com/) o la [API de administración de recursos de Azure](https://msdn.microsoft.com/library/azure/dn832684.aspx).

Según cómo se configure el servicio de búsqueda, usará el nivel Gratis que se comparte con otros suscriptores de Búsqueda de Azure, o el [nivel de pago](https://azure.microsoft.com/pricing/details/search/) que ofrece recursos dedicados en exclusividad para su servicio. Al aprovisionar el servicio, también se elige la región del centro de datos que hospeda el servicio.

Según el nivel de servicio que elija, puede escalar el servicio en dos dimensiones: 1) agregar réplicas para aumentar su capacidad a fin de manejar grandes cargas de consultas y 2) agregar particiones para agregar almacenamiento para más documentos. Al controlar el almacenamiento de documentos y el rendimiento de consultas por separado, puede personalizar el servicio de búsqueda para sus necesidades específicas.

### 2\. Creación de índice
Para cargar el contenido en el servicio Búsqueda de Azure, primero debe definir un índice de Búsqueda de Azure. Un índice es como una tabla de base de datos que contiene los datos y puede aceptar consultas de búsqueda. El esquema de índice se define para su asignación a la estructura de los documentos que desea buscar, igual que los campos de una base de datos.

Es posible crear el esquema de estos índices en el Portal de Azure, o mediante programación [utilizando el SDK de .NET](search-howto-dotnet-sdk.md) o la [API de REST](https://msdn.microsoft.com/library/azure/dn798941.aspx). Una vez que se ha definido el índice, se pueden cargar los datos en el servicio Búsqueda de Azure, donde se indexan seguidamente.

### 3\. Datos de índice
Una vez que haya definido los campos y los atributos del índice, estará listo para cargar el contenido en el índice. Se pueden emplear modelos de inserción o de extracción para la carga de los datos en el índice.

El modelo de extracción se proporciona a través de indexadores que se pueden configurar para actualizaciones bajo demanda o programadas (consulte [Operaciones de indexador (API de REST del servicio de Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn946891.aspx)), lo que permite introducir fácilmente datos y cambios de datos de Azure DocumentDB, Base de datos SQL de Azure, Almacenamiento de blobs de Azure o SQL Server hospedado en una máquina virtual de Azure.

El modelo de inserción se proporciona a través del SDK o las API de REST usadas para enviar documentos actualizados a un índice. Se pueden insertar datos desde prácticamente cualquier conjunto de datos usando el formato JSON. Consulte [Agregar, actualizar o eliminar documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx) o [Cómo usar Búsqueda de Azure desde una aplicación .NET](search-howto-dotnet-sdk.md) para obtener instrucciones sobre la carga de datos.

### 4\. Search
Una vez que se ha completado el índice de Búsqueda de Azure, podrá [emitir consultas de búsqueda](https://msdn.microsoft.com/library/azure/dn798927.aspx) al punto de conexión de servicio mediante solicitudes HTTP sencillas con la API de REST o el SDK. NET.

## Pruébelo ahora (¡gratis!)
Puede probar búsqueda de Azure hoy mismo. Si ya tiene una cuenta de Azure, puede [aprovisionar un servicio en el nivel Gratis](search-create-service-portal.md).

Si no tiene una cuenta de Azure, puede probar una sesión gratuita de 60 minutos sin necesidad de registrarse. Vaya a [Try Azure App Service](http://go.microsoft.com/fwlink/p/?LinkId=618214) y seleccione "Aplicación web". A continuación, seleccione la plantilla "ASP.NET + Azure Search" para empezar.

<!---HONumber=AcomDC_0302_2016-->