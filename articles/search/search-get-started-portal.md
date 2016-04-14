<properties 
	pageTitle="Introducción a Búsqueda de Azure | Microsoft Azure | Introducción a Búsqueda de Azure | DocumentDB | Servicio de búsqueda en la nube" 
	description="Cree su primera solución de Búsqueda de Azure con este tutorial. Aprenda a crear un índice de Búsqueda de Azure con datos de DocumentDB. Este es un ejercicio basado en portal, sin código, mediante el Asistente para la importación de datos." 
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
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na" 
	ms.date="02/10/2016" 
	ms.author="heidist"/>

# Introducción a Búsqueda de Azure en el Portal
> [AZURE.SELECTOR]
- [Portal](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Este artículo es una introducción sin código a Búsqueda de Azure de Microsoft, mediante el uso de las funcionalidades integradas en el Portal.

Este tutorial se da por hecho que cuenta con una [base de datos de DocumentDB de Azure de ejemplo](#apdx-sampledata) que puede crear fácilmente con nuestras instrucciones y nuestros datos de ejemplo, pero también puede adaptar estos pasos a los datos existentes, bien en DocumentDB o en Base de datos SQL.

> [AZURE.NOTE] Este tutorial de instrucción necesita una [suscripción de Azure](../../includes/free-trial-note.md) y un [servicio Búsqueda de Azure](search-create-service-portal.md). Si no está listo para registrarse para obtener una suscripción de prueba, puede omitir este tutorial y optar por [probar el Servicio de aplicaciones de Azure](search-tryappservice.md). Esta opción alternativa ofrece el servicio Búsqueda de Azure con una aplicación web de ASP.NET de forma gratuita (una hora por sesión); no es necesaria ninguna suscripción.
 
## Búsqueda del servicio

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Abra el panel del servicio Búsqueda de Azure. A continuación se presentan algunas formas de buscar el panel.
	- En la barra de accesos directos, haga clic en **Servicios de búsqueda**. La barra de accesos directos enumera todos los servicios aprovisionados en la suscripción. Si se ha definido un servicio de búsqueda, podrá ver **Servicios de búsqueda** en la lista.
	- En la barra de accesos directos, haga clic en **Examinar** y, después, escriba "búsqueda" en el cuadro de búsqueda para generar una lista de todos los servicios de búsqueda creados en las suscripciones.

## Búsqueda de espacio

Muchos clientes comienzan con el servicio gratis. Esta versión está limitada a tres índices, tres orígenes de datos y tres indexadores. Asegúrese de que tiene espacio para elementos adicionales antes de empezar. En este tutorial creará uno de cada objeto.

## Creación de un índice y carga de datos

Las consultas de búsqueda recorren en iteración un *índice* que contiene datos de búsqueda, metadatos y construcciones usados para optimizar determinados comportamientos de búsqueda. Como primer paso, definirá y rellenará un índice.

Hay varias maneras de crear un índice. Si sus datos están en un almacén que Búsqueda de Azure puede rastrear, como Base de datos SQL de Azure, SQL Server en una máquina virtual de Azure o DocumentDB, puede crear y rellenar un índice muy fácilmente mediante un *indexador*.

Para mantener esta tarea basada en el portal, se da por hecho que contamos con datos de DocumentDB que se pueden rastrear usando un indexador mediante el asistente **Importar datos**.

Antes de continuar, cree una [base de datos de DocumentDB de ejemplo](#apdx-sampledata) para usar con este tutorial y, después, vuelva a esta sección para completar los pasos siguientes.

<a id="defineDS"></a>
#### Paso 1: Definición del origen de datos

1. En el panel del servicio Búsqueda de Azure, haga clic en **Importar datos** en la barra de comandos para iniciar un asistente que crea y rellena un índice.

  ![][7]

2. En el asistente, haga clic en **Origen de datos** > **DocumentDB** > **Nombre** y escriba un nombre para el origen de datos. Un origen de datos es un objeto de conexión en Búsqueda de Azure que puede usarse con otros indexadores. Una vez creado, queda disponible como un "origen de datos existente" en el servicio.

3. Elija su cuenta de DocumentDB existente y la base de datos y colección. Si usa los datos de ejemplo que se proporcionan, la definición del origen de datos tendrá este aspecto:

  ![][2]

Tenga en cuenta que estamos omitiendo la consulta. Esto es porque en esta ocasión no estamos implementando el seguimiento de cambios en nuestro conjunto de datos. Si el conjunto de datos incluye un campo que realiza un seguimiento de la actualización de un registro, puede configurar un indexador de Búsqueda de Azure para usar el seguimiento de cambios para actualizaciones selectivas de su índice.

Haga clic en **Aceptar** para completar este paso del asistente.

#### Paso 2: Definición del índice

En el asistente, haga clic en **Índice** y eche un vistazo a la superficie de diseño usada para crear un índice de Búsqueda de Azure. Como mínimo, un índice requiere un nombre y una colección de campos, con un campo marcado como clave de documento. Como estamos usando un conjunto de datos de DocumentDB, el Asistente detecta automáticamente los campos y el índice se carga previamente con los campos y asignaciones de tipo de datos.

  ![][3]

Aunque los campos y tipos de datos están configurados, todavía hay que asignar atributos. Las casillas en la parte superior de la lista de campos son *atributos de índice* que controlan cómo se utiliza el campo.

- **Retrievable** significa que se muestra en la lista de resultados de búsqueda. Puede desactivar esta casilla para marcar los campos individuales como fuera de los resultados de búsqueda, por ejemplo, cuando los campos se usan solo en expresiones de filtro. 
- **Filterable**, **Sortable** y **Facetable** determinan si un campo se puede usar en un filtro, una ordenación o una estructura de navegación de facetas. 
- **Searchable** significa que se incluye un campo en la búsqueda de texto completo. Las cadenas normalmente permiten búsquedas. Los campos numéricos y los booleanos a menudo se marcan como no utilizables en búsquedas. 

Antes de salir de esta página, marque los campos en el índice para utilizar las siguientes opciones (Searchable, Retrievable, etc.). La mayoría de los campos son Retrievable. La mayoría de los campos de cadena son Searchable (no es necesario hacer que se pueda buscar la clave). Algunos campos como género, orderableOnline, rating y tags también son Filterable, Sortable y Facetable.
	
Campo | Tipo | Opciones |
------|------|---------|
key | Edm.String | |
albumTitle | Edm.String | Retrievable, Searchable |
albumUrl | Edm.String | Retrievable, Searchable |
genre | Edm.String | Retrievable, Searchable, Filterable, Sortable, Facetable |
genreDescription | Edm.String | Retrievable, Searchable |
artistName | Edm.String | Retrievable, Searchable |
orderableOnline | Edm.Boolean | Retrievable, Filterable, Sortable, Facetable |
etiquetas | Collection(Edm.String) | Retrievable, Filterable, Facetable |
price | Edm.Double | Retrievable, Filterable, Facetable |
margin | Edm.Int32 | |
rating | Edm.Int32 | Retrievable, Filterable, Sortable, Facetable |
inventory | Edm.Int32 | Retrievable |
lastUpdated | Edm.DateTimeOffset | |

Como punto de comparación, la captura de pantalla siguiente muestra un índice creado para la especificación de la tabla anterior.

 ![][4]

Haga clic en **Aceptar** para completar este paso del asistente.

#### Paso 3: Definición del indexador

Todavía en el **Asistente para importar datos**, haga clic en **Indexador** > **Nombre**, escriba un nombre para el indexador y use los valores predeterminados para todos los demás valores. Este objeto define un proceso ejecutable. Una vez creado, puede colocarlo en una programación periódica pero, de momento, use la opción predeterminada para ejecutar el indexador una vez, inmediatamente, al hacer clic en **Aceptar**.

Las entradas de datos de importación deben rellenarse todas y estar listas para su uso.

  ![][5]

Para ejecutar el asistente, haga clic en **Aceptar** para iniciar la importación y cierre el asistente.

## Comprobación del progreso

Para comprobar el progreso, vuelva al panel del servicio, desplácese hacia abajo y haga doble clic en el icono **Indexadores** para abrir la lista correspondiente. Debería ver el indexador que acaba de crear en la lista así como su estado que indica "en curso" o "correcto", junto con el número de documentos indexados en Búsqueda de Azure.

  ![][6]

## Consultas al índice

Ahora tiene un índice de búsqueda listo para su consulta.

**Explorador de búsqueda** es una herramienta de consulta integrada en el portal. Proporciona un cuadro de búsqueda para que se pueda comprobar que una entrada de búsqueda devuelve los datos esperados.

1. Haga clic en **Explorador de búsqueda** en la barra de comandos.
2. Observe qué índice está activo. Si no es el que acaba de crear, haga clic en **Cambiar índice** en la barra de comandos para seleccionar el que desee.
2. Deje vacío el cuadro de búsqueda y, a continuación, haga clic en el botón **Buscar** para ejecutar una búsqueda con caracteres comodín que devuelva todos los documentos.
3. Escriba algunas consultas de búsqueda de texto completo. Puede revisar los resultados en la búsqueda con caracteres comodín para familiarizarse con los intérpretes, álbumes y géneros que se van a consultar.
4. Para obtener ideas, pruebe otra sintaxis de consulta con los [ejemplos proporcionados al final de este artículo](https://msdn.microsoft.com/library/azure/dn798927.aspx) y modifique la consulta para utilizar cadenas de búsqueda que es probable que se encuentren en el índice.

## Pasos siguientes

Después de ejecutar el Asistente una vez, puede volver atrás y ver o modificar componentes individuales: índice, indexador u origen de datos. No se permiten algunas modificaciones en el índice, como cambiar el tipo de datos de campo, pero la mayoría de las propiedades y configuraciones son modificables. Para ver los componentes individuales, haga clic en los iconos **Índice**, **Indexador** o bien en **Orígenes de datos** en el panel para mostrar una lista de objetos existentes.

Para más información sobre otras características que se mencionan en este artículo, visite los vínculos siguientes:

- [Indexadores](search-indexer-overview.md)
- [Crear índice (incluye una explicación detallada de los atributos de índice)](https://msdn.microsoft.com/library/azure/dn798941.aspx)
- [Explorador de búsqueda](search-explorer.md)
- [Buscar documentos (incluye ejemplos de sintaxis de consulta)](https://msdn.microsoft.com/library/azure/dn798927.aspx)

Puede probar este mismo flujo de trabajo mediante el Asistente para importar datos en otros orígenes de datos, como Base de datos SQL de Azure o SQL Server en máquinas virtuales de Azure.

> [AZURE.NOTE] Recientemente anunciado es la compatibilidad del indexador para el seguimiento de Almacenamiento de blobs de Azure, pero esta característica está en versión preliminar y aún no es una opción del portal. Para probar ese indexador, debe escribir código. Consulte [Indexación de documentos en Almacenamiento de blobs de Azure con Búsqueda de Azure](search-howto-indexing-azure-blob-storage.md) para obtener más información. <a id="apdx-sampledata"></a>


## Apéndice: Crear datos de ejemplo en DocumentDB

En esta sección se crea una pequeña base de datos de DocumentDB que puede usarse para completar las tareas de este tutorial.

Las instrucciones siguientes proporcionan indicaciones generales, pero no son exhaustivas. Si necesita más ayuda con las tareas o la navegación del portal de DocumentDB, puede consultar la documentación de DocumentDB, pero la mayoría de los comandos que necesita están en la barra de comandos de servicio, en la parte superior del panel, o en la hoja de la base de datos.

  ![][1]

### Crear musicstoredb para este tutorial

1. [Haga clic aquí](https://github.com/HeidiSteen/azure-search-get-started-sample-data) para descargar un archivo ZIP que contenga los archivos de datos JSON de la tienda de música. Ofrecemos 246 documentos JSON para este conjunto de datos.
2. Agregue DocumentDB a su suscripción y después abra el panel de servicio.
2. Haga clic en **Agregar base de datos** para crear una base de datos nueva con un identificador de `musicstoredb`. Se mostrará en una lista de bases de datos más abajo en la página después de haberse creado.
2. Haga clic en el nombre de la base de datos para abrir la hoja de base de datos.
3. Haga clic en **Agregar colección** para crear una colección con un identificador de `musicstorecoll`.
3. Haga clic en **Explorador de documentos**.
4. Haga clic en **Agregar documentos**.
5. En **Agregar documento**, cargue los archivos JSON en lotes de 100 o menos.
	- 386\.json
	- 387\.json
	- . . .
6. Haga clic en **Explorador de consultas** para comprobar que se cargan los datos para cumplir los requisitos de carga del Explorador de documentos.

Una manera fácil de hacerlo consiste en modificar la consulta predeterminada para que seleccione los 300 primeros (hay menos de 300 elementos en este conjunto de datos), o bien escriba `select * from musicstorecoll` y, a continuación, haga clic en **Ejecutar consulta**.

Obtendrá un JSON de salida, que comienza por el número de documento 386 y termina en el documento 669. Una vez cargados los datos, puede [volver a los pasos de este tutorial](#defineDS) para generar un índice mediante el **Asistente para importación**.


<!--Image references-->
[1]: ./media/search-get-started-portal/AzureSearch-GetStart-Docdbmenu1.png
[2]: ./media/search-get-started-portal/AzureSearch-GetStart-DataSource.png
[3]: ./media/search-get-started-portal/AzureSearch-GetStart-DefaultIndex.png
[4]: ./media/search-get-started-portal/AzureSearch-GetStart-FinishedIndex.png
[5]: ./media/search-get-started-portal/AzureSearch-GetStart-ImportReady.png
[6]: ./media/search-get-started-portal/AzureSearch-GetStart-IndexerList.png
[7]: ./media/search-get-started-portal/search-data-import-wiz-btn.png

<!---HONumber=AcomDC_0309_2016-->