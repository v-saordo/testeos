<properties
	pageTitle="Importación de datos a Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Carga de datos en un índice en Búsqueda de Azure"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="heidist"/>

# Importación de datos a Búsqueda de Azure
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

En Búsqueda de Azure, el servicio funciona con datos persistentes (un índice) que proporciona documentos e información que se usa para procesar un índice, ejecutar consultas o formular los resultados de la búsqueda. Para rellenar un índice, puede usar un modelo de inserción o de extracción para cargar los datos.

Para poder importar datos, el índice debe existir. Para obtener más información, consulte [Índices de Búsqueda de Azure](search-what-is-an-index.md).

##Inserción de datos en un índice

Este enfoque supone tomar un conjunto de datos existente que se ajuste al esquema de índice y publicarlo en un servicio de búsqueda. En el caso de las aplicaciones con requisitos de latencia muy baja (por ejemplo, si se necesita que las operaciones de búsqueda estén sincronizadas con las bases de datos del inventario), la única opción es un modelo de inserción.

Para insertar datos en un índice, se pueden usar la API de REST o el SDK de .NET. Actualmente no se admite ninguna herramienta para insertar datos mediante el portal.

Este enfoque es más flexible que un modelo de extracción, ya que los documentos se cargan individualmente o en lotes (hasta 1000 por lote o 16 MB, lo que ocurra primero).

##Extracción (rastreo) de datos 

Los modelos de extracción rastrean los orígenes de datos admitidos y cargan los índices automáticamente. En Búsqueda de Azure, esta capacidad se implementa mediante *indizadores*, que actualmente están disponibles para Base de datos SQL de Azure, DocumentDB o SQL Server en Máquinas virtuales de Azure. Para obtener información sobre cómo cargar datos SQL de Azure, consulte [Indizadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) .

Para extraer datos e introducirlos en un índice, se pueden usar el portal, la API de REST o el SDK de .NET.

##Requisitos de los conjuntos de datos

No existen restricciones sobre el tipo de datos que se cargan, siempre que el esquema y los conjuntos de datos se formulen como estructuras JSON.

Los datos deben provenir de la base de datos o del origen de datos que su aplicación personalizada cree o consuma. Por ejemplo, si la aplicación es un catálogo de venta minorista en línea, el índice que se crea para Búsqueda de Azure debe dibujar datos del inventario de productos o las bases de datos de ventas admitidos por la aplicación.

El conjunto de datos debe derivarse de una sola tabla, vista, contenedor de blobs o equivalente. Puede ser necesario crear una estructura de datos en la base de datos o aplicación no SQL que proporciona los datos en Búsqueda de Azure. O bien, para algunos orígenes de datos como Base de datos de SQL Azure o DocumentDB, puede crear un indexador que rastrea datos en una tabla externa, una vista o un contenedor de blob para cargarlos en Búsqueda de Azure.

##Elección de un enfoque para la importación de datos

|Criterios|Enfoque recomendado|
|------------|---------------|
|Sincronización de datos casi en tiempo real|Código .NET o API de REST para insertar las actualizaciones en un índice. Un enfoque de extracción para la ingesta de datos es una operación programada, que no se puede ejecutar lo suficientemente rápido para mantener los cambios rápidos en un origen de datos principal.|
|Base de datos SQL de Azure, DocumentDB o SQL Server en Máquinas virtuales de Azure|Los indexadores están unidos a tipos de orígenes de datos específicos. Si los orígenes de datos principales son un tipo de origen de datos compatibles, un indizador es la forma más sencilla de cargar un índice. Se puede programar que la actualización de los datos se realice cada hora. Puede configurar un indexador en el portal o en código.|
|Actualización de datos programada|Use un indexador (consulte más arriba).|
|Creación de prototipos sin código ni edición|El portal incluye a un Asistente para la importación de datos que configura un indexador; para ello, a veces genera un esquema preliminar si hay suficiente información en la base de datos principal para hacerlo. El asistente incluye opciones para configurar la actualización de datos programada. Si lo desea, puede agregar los analizadores de lenguaje u opciones de CORS. Existen algunos aspectos negativos: no se puede agregar perfiles de puntuación, ni tampoco puede exportar un esquema creado en el portal a un archivo JSON para su uso en el código.| 

<!---HONumber=AcomDC_0224_2016-->