<properties
	pageTitle="Índices en Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="¿Qué es un índice de Búsqueda de Azure y cómo se usa?"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="heidist"/>

# Índices en Búsqueda de Azure
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Búsqueda de Azure es un servicio de búsqueda hospedado en la nube que proporciona un motor de búsqueda, características de búsqueda que permiten una experiencia similar a Google y Bing en la aplicación personalizada, API de .NET y de REST para integrar Búsqueda de Azure con la aplicación web o aplicación de escritorio y almacenamiento de los datos relacionados con la búsqueda en forma de *índice*.

##¿Qué es un índice?

Un *índice* es un almacenamiento persistente de *documentos* y otras construcciones usadas por el servicio Búsqueda de Azure. Los documentos representan una unidad básica de datos que se pueden buscar. Por ejemplo, un minorista puede tener un documento para cada SKU, una organización de noticias puede tener un documento para cada artículo y una organización de streaming multimedia puede tener un documento para cada vídeo o canción de la biblioteca. Estos conceptos pueden equipararse a equivalentes de base de datos más conocidos: un *índice* es conceptualmente similar a una *tabla* y los *documentos* son más o menos equivalentes a las *filas* de una tabla.

El índice es un conjunto de datos sin formato, normalmente un subconjunto de los datos creado o capturado durante las operaciones normales del negocio mediante procesos informatizados, como transacciones de ventas, publicación de contenido y actividades de compra, que suele almacenarse en bases de datos relacionales o NoSQL. Un índice obtiene sus documentos al insertar o extraer un conjunto de datos que contiene las filas de otros orígenes de datos para el índice.

##¿Cuándo debo crear un índice?

Como parte del aprovisionamiento de un servicio de búsqueda para su uso por la aplicación, deberá crear un índice. Hacer que un índice esté disponible para las operaciones de búsqueda es una tarea de dos partes. En primer lugar, se define el esquema y se publica en Búsqueda de Azure. En segundo lugar, se rellena el índice con un conjunto de datos definidos por el usuario que cumpla con el esquema.

Para crear un esquema que define un índice, puede usar el Portal o escribir código que llama al SDK de .NET o a la API de REST (consulte las pestañas en la parte superior de este tema). Para más información sobre la ingesta de datos después de la creación del índice, consulte [Importación de datos a Búsqueda de Azure](search-what-is-data-import.md).

##Especificación del esquema JSON de un índice

Un índice es un documento JSON que contiene secciones para campos y atributos, perfiles de puntuación, proveedores de sugerencias, un perfil de puntuación predeterminado y opciones de CORS.

 ![][1]

Las secciones principales de un índice de Búsqueda de Azure, como se articula en el formato de intercambio de datos JSON, son las siguientes.

|Sección|Descripción|
|--------------|-----------|
|Fields (recopilación)|Fields define un documento. Un conjunto de datos de inserción o de extracción en un índice debe proporcionar valores o nulos para cada campo, compatible con el tipo de datos y la longitud del campo expresados en el esquema. Los campos tienen [atributos](#index-attributes), que son propiedades o anotaciones usadas para marcar un campo con el fin de habilitar comportamientos relacionados con la búsqueda para ese campo. Por ejemplo, puede especificar si el campo es un campo de búsqueda, si se puede recuperar u ordenar, campo por campo. También puede especificar las modificaciones del analizador de lenguaje en el nivel de campo.
|[Proveedores de sugerencias](https://msdn.microsoft.com/library/azure/mt131377.aspx)|También conocidas como autocompletar o consultas de escritura anticipada, se definen como una sección en el índice.|
|[Perfiles de puntuación](https://msdn.microsoft.com/library/azure/dn798928.aspx)|Criterios que se usan para mejorar la clasificación de un resultado de búsqueda que tiene más de las características establecidas en el perfil. Por ejemplo, supongamos que un término de búsqueda coincide con un nombre de producto y con la descripción del producto, quizá quiera que las coincidencias del nombre del producto se clasifiquen en una posición superior que las que se encuentran en la descripción. Puede crear varios perfiles.|
|Perfil de puntuación predeterminado|Opcionalmente, puede invalidar la lógica integrada que procesa una puntuación de la clasificación de búsqueda mediante la especificación de uno de los perfiles de puntuación como el valor predeterminado.|
|Opciones de CORS|Opcionalmente, permite compartir recursos entre orígenes, donde las solicitudes para un recurso usado por una página web se emiten a través de un límite de dominios. CORS está siempre desactivado, a menos que lo habilite específicamente para su índice.|

<a name="index-attributes"></a>
##Atributos de índice

Los atributos se establecen en campos individuales para especificar cómo se usa el campo. En la tabla siguiente se enumeran los atributos que puede especificar.

|**Propiedad**|Descripción|
|------------|-----------|
|**Retrievable**|Establece si el campo se puede devolver en un resultado de búsqueda.|
|**Filterable**|Permite que el campo se use en consultas **$filter**.|
|**Sortable**|Permite usar el campo como columna de ordenación en un conjunto de resultados.|
|**Facetable**|Permite que un campo se use en una estructura de navegación con facetas para el filtrado autodirigido. Normalmente los campos que contienen valores repetitivos que se pueden usar para agrupar varios documentos (por ejemplo, varios documentos que forman parte de una única categoría de servicio o un único producto) funcionan mejor como facetas.|
|**Clave**|Una cadena que proporciona el identificador único de cada documento, que se usa para buscar los documentos. Todos los índices deben tener una clave. Solo un campo puede ser la clave y se debe establecer en Edm.String.|
|**Searchable**|Marca el campo como campo de búsqueda de texto completo.|


##¿Cómo se usan los índices en Búsqueda de Azure?

Los datos del índice se usan para construir una lista de resultados de búsqueda, una estructura de navegación por facetas o una página de detalles para un único documento. En Búsqueda de Azure, un índice también puede contener datos que no se pueden buscar usados internamente en expresiones de filtro o en los perfiles de puntuación que proporcionan los criterios usados para aumentar una puntuación de clasificación de la búsqueda.

Todas las operaciones relacionadas con datos realizadas con Búsqueda de Azure consumen o devuelven datos de un índice. No hay excepciones: el servicio no puede apuntar a otros orígenes de datos además del índice para devolver datos externos en una respuesta emitida por su servicio de búsqueda.

Obviamente, para las aplicaciones que acumulan datos y operan en ellos, como las transacciones de ventas de una aplicación comercial en línea, un índice de Búsqueda de Azure será un origen de datos adicional en la solución general. Aunque podría parecer redundante tener un almacenamiento de datos dedicado solo para la búsqueda, contar con él ofrece los beneficios siguientes: coherencia en los resultados de búsqueda, volatilidad reducida, menor número de idas y vueltas entre la aplicación y Búsqueda de Azure y rendimiento general mejorado de las operaciones de búsqueda porque no hay contención para los datos o los recursos.

##Guía para definir un esquema

Los esquemas se crean como estructuras JSON. Debe crear un índice para cada solución personalizada que se integra con Búsqueda de Azure. No se pueden vincular o combinar varios índices, pero puede crear índices complejos que adapten una variedad de escenarios de búsqueda necesarios para la solución. Por ejemplo, si las aplicaciones admiten varios idiomas, la colección de campos del índice podría tener variantes específicas del idioma para cada campo.

Al diseñar el índice, tómese su tiempo en la fase de planeación y reflexione cada decisión. El cambio de un índice después de la implementación implica volver a generar y volver a cargar los datos. Si crea el índice en el código, este paso será mucho más fácil que si lo crea manualmente en el portal.

Es esencial contar con una comprensión sólida de los datos de origen originales para crear un buen esquema. Desea hacer coincidir los tipos de datos, saber qué campo usar como *clave* para que identifique de forma única cada documento en el índice y qué campos se deben exponer en una lista de resultados de búsqueda o página de detalles.

**Inicio con datos relacionales**

Los datos relacionales pueden ser difíciles transformar un conjunto de datos sin formato. Si es posible, intente usar el almacenamiento de datos o la base de datos informes de su empresa, si está disponible. Suele ser la mejor opción porque el modelo ya está desnormalizado. De lo contrario, tendrá que se basarse en las consultas para obtener el conjunto de filas que necesita. Para ver un ejemplo y una explicación de cómo hacerlo, consulte la entrada del blog [Modeling the AdventureWorks Inventory Database for Azure Search](http://blogs.technet.com/b/onsearch/archive/2015/09/08/modeling-the-adventureworks-inventory-database-for-azure-search.aspx) (Modelado de la base de datos de inventario de AdventureWorks para Búsqueda de Azure).

**Inclusión de datos binarios**

Puede desear incluir contenido de imágenes, audio o vídeo en los resultados de la búsqueda. En ese caso, los archivos se almacenarán en otra parte, y el índice de incluirá un campo representativo con una dirección URL al recurso. Cualquier contenido, especialmente contenido binario, que no se pueda buscar debe almacenarse en un almacenamiento más barato y después hacerse referencia a él mediante el URI de un campo en el índice.

**Sincronización de datos**

Como se puede imaginar, el índice se debe sincronizar periódicamente con otros orígenes de datos usados en la solución. En un catálogo minorista en línea, la base de datos de inventario que captura las transacciones de ventas debe tener la misma SKU, precios y disponibilidad que los datos mostrados a través de los resultados de la búsqueda. Dependiendo de cuánta latencia sea aceptable para su solución, es posible que la sincronización de datos pueda realizarse una vez por semana, una vez al día o en casi en tiempo real mediante escrituras simultáneas tanto en la base de datos de inventario como en un índice de Búsqueda de Azure. [Importación de datos en Búsqueda de Azure](search-what-is-data-import.md) explica las opciones disponibles con detalle.

## Almacenamiento en la nube

Dado que Búsqueda de Azure es un servicio de búsqueda totalmente administrado, el almacenamiento de datos se trata como una operación interna. Como programador, es importante entender que no se pueden omitir las API de Búsqueda de Azure para conectarse directamente al índice, ni se puede supervisar el tamaño del índice o el mantenimiento excepto a través de notificaciones del portal o llamadas a API. No hay ninguna funcionalidad para realizar copias de seguridad o restaurar un índice, ni para optimizar o ajustar el rendimiento en el nivel de almacenamiento. En segundo plano, los datos se almacenan como almacenamiento de blobs, pero la mejor manera de pensar en el almacenamiento físico y en la administración de un índice es considerarlos un detalle de implementación que podría cambiar en el futuro. Una de las principales ventajas del servicio es el enfoque sin intervención humana para administrar físicamente los recursos de proceso de la aplicación de búsqueda.

Si los requisitos de almacenamiento de datos o el volumen de consultas cambian con el tiempo, puede aumentar o disminuir la capacidad agregando o moviendo particiones y réplicas. Para más información, consulte [Administración del servicio de Búsqueda en Azure](search-manage.md) o [Límites de servicio](search-limits-quotas-capacity.md).

<!--Image References-->
[1]: ./media/search-what-is-an-index/search-JSON-indexSchema.png

<!---HONumber=AcomDC_0224_2016-->