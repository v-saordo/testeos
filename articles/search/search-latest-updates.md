<properties 
	pageTitle="Novedades en la actualización más reciente de Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube" 
	description="Notas de la versión de Búsqueda de Azure que describen las actualizaciones más recientes del servicio" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="02/10/2016" 
	ms.author="heidist"/>

#Novedades en la actualización más reciente de Búsqueda de Azure#

Búsqueda de Azure es un servicio de búsqueda hospedado en la nube en Microsoft Azure. Está disponible con carácter general y ofrece un contrato de nivel de servicio (SLA) con el 99,9 % de disponibilidad para las configuraciones de la [versión 2015-02-28 de la API de REST de servicio de búsqueda](https://msdn.microsoft.com/library/azure/dn798935.aspx) o [SDK de .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx).

##Novedades en 2016

Característica|Fecha de publicación|Estado|Detalles
-------|--------|------|-------
[SDK de .NET 1.1](https://msdn.microsoft.com/library/azure/dn951165.aspx)|Febrero de 2016|GA|Esta es la primera versión disponible con carácter general de la biblioteca de cliente de .NET `Microsoft.Azure.Search.dll`. Esta versión presenta cambios importantes. Vea [Actualización a la versión 1.1 del SDK de .NET para Búsqueda de Azure](search-dotnet-sdk-migration.md) para obtener instrucciones sobre la migración.
[Compatibilidad con la sintaxis de consulta de Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx)|Febrero de 2016|[GA](search-api-2015-02-28.md)|La sintaxis de consulta de Lucene está disponible con carácter general en la API de REST y el SDK de .NET. Para habilitar la sintaxis de Lucene, establezca el parámetro `queryType` en `full` en la API de REST y la propiedad `SearchParameters.QueryType` en `QueryType.Full` en el SDK de .NET.
[Analizadores personalizados](https://azure.microsoft.com/blog/custom-analyzers-in-azure-search/)|Enero de 2016|[Vista previa](search-api-2015-02-28-preview.md)|Configuraciones definidas por el usuario de Tokenizer y filtros de token. Consulte [Análisis en Búsqueda de Azure](https://msdn.microsoft.com/library/azure/mt605304.aspx) en MSDN.
[Indexador de Almacenamiento de blobs de Azure](search-howto-indexing-azure-blob-storage.md)|Enero de 2016|[Vista previa](search-api-2015-02-28-preview.md)|Los archivos PDF, documentos de Office, XML, HTML o los archivos de audio y vídeo pueden combinarse o introducirse en un índice de Búsqueda de Azure.
[Explorador de búsqueda](search-explorer.md)|Enero de 2016|[Portal](https://portal.azure.com)|Herramienta de consulta integrada para consultas ad hoc en un índice.
[Paquete de contenido de Power BI para Búsqueda de Azure](http://blogs.msdn.com/b/powerbi/archive/2016/01/19/visualizing-azure-search-data-with-power-bi.aspx)|Enero de 2016|Herramienta|Visualización y análisis de datos de servicio con un nuevo paquete de contenido de Power BI para Búsqueda de Azure. Disponible a través de Análisis de búsqueda.
[Análisis de búsqueda](https://azure.microsoft.com/blog/analyzing-your-azure-search-traffic/)|Enero de 2016|Portal|Habilite la recopilación de datos para obtener información sobre la actividad de búsqueda del usuario.

##Novedades en 2015

Característica|Fecha de publicación|Estado|Detalles
-------|--------|------|-------
Analizadores de idioma de Lucene|Octubre de 2015|GA|Esta característica está ahora disponible con carácter general (GA): está disponible en la API de REST del servicio y en el SDK de .NET.
[Procesadores de lenguaje natural de Microsoft](search-api-2015-02-28-Preview.md)|Octubre de 2015|GA|Esta característica está ahora disponible con carácter general (GA): está disponible en la API de REST del servicio y en el SDK de .NET. 
[Compatibilidad con la sintaxis de consulta de Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx)|Septiembre de 2015|[Vista previa](search-api-2015-02-28-preview.md)|Agrega el analizador de consultas de Lucene. Para usar la nueva sintaxis, debe especificar `queryType` en una operación de búsqueda de documentos.
[Procesadores de lenguaje natural](search-language-support.md)|Septiembre de 2015|[Vista previa](search-api-2015-02-28-preview.md)|Se han agregado procesadores del idioma de Microsoft, por lo que ha aumentado el número de idiomas globales y se proporciona una implementación alternativa para los demás.
PUBLICACIÓN en consultas de búsqueda, sugerencias y búsquedas|Septiembre de 2015|[Vista previa](search-api-2015-02-28-preview.md)|Se aplica a la API de REST del servicio.
[API de REST de administración](https://msdn.microsoft.com/library/azure/dn832684.aspx)|Septiembre de 2015|GA|Segunda versión de la API de REST de administración. Incluye comprobaciones checkNameAvailability de si un nombre de servicio determinado ya se está usando, el intervalo de réplica era anteriormente 1-6 y ahora es 1-12, la propiedad de la SKU se ha movido del contenedor de propiedades al nivel superior de la carga del servicio o se ha actualizado el cuerpo de respuesta de la operación de creación del servicio de búsqueda para acomodar la reubicación de la configuración de la SKU.
SDK para .NET 0.10.0-preview|Agosto de 2015|Vista previa|Se trata de la segunda iteración de la biblioteca de cliente .NET, Microsoft.Azure.Search.dll. Esta versión agrega compatibilidad para crear, administrar y usar la [clase DataSource](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.datasource.aspx) y la [clase de indexadores](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx) por medio de clases. NET. Además, para los indizadores de SQL Azure, hay nueva compatibilidad para la indización de puntos geográficos.
Construcciones de fieldMapping|Abril de 2015|Vista previa|Los indizadores ahora admiten construcciones de fieldMapping que proporcionan asignaciones de campo cuando los nombres de campo reales son diferentes entre la base de datos externa y el índice de Búsqueda de Azure. Consulte [Indizadores](search-api-indexers-2015-02-28-Preview.md) para la versión `2015-02-28-preview` de la documentación de indizadores.
transformaciones de tipo de campo|Abril de 2015|Vista previa|Los indizadores admiten ahora transformaciones de tipo de campo para que pueda asignar un campo de cadena de una tabla SQL a un campo de recopilación de cadenas en un índice de búsqueda, suponiendo que el campo de origen represente una matriz JSON.
[API de REST de servicio](https://msdn.microsoft.com/library/azure/dn798935.aspx)|Marzo de 2015|GA|Primera versión disponible con carácter general de la API de REST del servicio Búsqueda de Azure. Esta versión incluye las características anteriores. También incluye [indexadores](http://go.microsoft.com/fwlink/p/?LinkID=528210). Los [proveedores de sugerencias](https://msdn.microsoft.com/library/azure/dn798936.aspx) reemplaza la compatibilidad de consulta con escritura anticipada, más limitada, de la implementación anterior (sólo coincide con los prefijos) agregando compatibilidad para la coincidencia con infijos. Esta implementación puede buscar coincidencias en cualquier parte de un término y también admite la coincidencia aproximada. El [aprovechamiento de etiquetas](http://go.microsoft.com/fwlink/p/?LinkId=528212) permite un nuevo escenario de perfiles de puntuación. En concreto, aprovecha los datos almacenados (por ejemplo, las preferencias de compra), por lo que puede mejorar los resultados de búsqueda para usuarios individuales en función de su información personalizada. 
SDK para .NET 0.9.6-preview|Marzo de 2015|Vista previa|Se trata de la primera versión pública del SDK para .NET de Búsqueda de Azure. Incluye una biblioteca de cliente, Microsoft.Azure.Search.dll, con dos espacios de nombres: [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx) y [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx).
API de REST de administración|Marzo de 2015|GA|La primera versión de la API de REST de administración que pertenece a la versión disponible con carácter general de Búsqueda de Azure. No hay ninguna diferencia entre la versión preliminar anterior y esta.
[Procesadores de lenguaje natural de Microsoft](search-api-2015-02-28-Preview.md)|Marzo de 2015|Vista previa|Agrega más idiomas y lematización expansiva para todos los idiomas compatibles con Office y Bing.
[moreLikeThis=](search-api-2015-02-28-Preview.md)|Marzo de 2015|Vista previa|Un parámetro de búsqueda, mutuamente exclusivo de `search=`, que desencadena una ruta de ejecución de consulta alternativa. En lugar de realizar una búsqueda de texto completo de `search=` basada en una entrada de término de búsqueda, `moreLikeThis=` busca documentos que son similares a un documento determinado mediante la comparación de sus campos de búsqueda.

##Novedades en 2014

Característica|Fecha de publicación|Estado|Detalles
-------|--------|------|-------
Analizadores de idioma de Lucene|Noviembre de 2014|Vista previa|Se agregaron para proporcionar compatibilidad con múltiples idiomas para los analizadores de idiomas personalizados distribuidos con Lucene. 
Compatibilidad del portal incorporada para la creación de índices|Noviembre de 2014|[Portal](https://portal.azure.com)|Pueden crearse índices, analizadores y perfiles de puntuación en el portal.
Versión de API de administración 2014-07-31|Octubre de 2014|Vista previa|Primera versión de vista previa pública de la API de REST de administración. La documentación de esta versión de la API está disponible a petición.
API de REST del servicio de búsqueda|Agosto de 2014|Vista previa|Primera versión de vista previa pública de la API de REST de servicio (vista previa de la versión de la API del 31-07-2014). Se trata de la API de REST para operaciones de documentos e índices, perfiles de puntuación para optimizar los resultados de búsqueda y compatibilidad geoespacial. Esta versión admite el aprovisionamiento del servicio en el Portal de Azure. La documentación de esta versión de la API está disponible a petición. Se actualiza de forma independiente de la API de REST del servicio.

##Cómo se actualizan e implementan las características

Las características se publican por separado o conjuntamente mediante la [API de REST](https://msdn.microsoft.com/library/azure/dn798935.aspx), el [SDK de .NET](http://go.microsoft.com/fwlink/?LinkId=528216) o el panel de servicio del [Portal de Azure](https://portal.azure.com). La biblioteca .NET y la API de REST tienen varias versiones. Las API más antiguas siguen estando operativas cuando se implementan características nuevas. Puede visitar [Versiones del servicio de búsqueda](https://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información acerca de nuestra directiva de control de versiones.

Las características de vista previa y de disponibilidad general (GA) están vinculadas a una versión de la API de la misma categoría.

- Las características de vista previa están en fase de prueba y podrían cambiar o incluso cancelarse antes de alcanzar la disponibilidad general. Las características de vista previa están siempre disponibles en la [versión de vista previa de la API de REST](search-api-2015-02-28-preview.md) y a veces en el [SDK de .NET](http://go.microsoft.com/fwlink/?LinkId=528216). La documentación de la característica siempre explicará cómo obtener acceso a la característica en cuestión.
- Las características de GA son estables y es poco probable que cambien. Cualquier cambio en una característica de GA se anuncia como un cambio importante.

Se espera que las características que se basen exclusivamente en una herramienta o en el portal cambien con el tiempo y no se clasifican como vista previa o GA.














 

<!---HONumber=AcomDC_0211_2016-->