<properties
	pageTitle="Importación de datos en Búsqueda de Azure con el Portal | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Carga de datos en un índice de Búsqueda de Azure con el Portal"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="Azure Portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="heidist"/>

# Importación de datos en Búsqueda de Azure con el Portal
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST API](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

El Portal de Azure incluye un comando **Importar datos** en el panel de Búsqueda de Azure que lo guía por la ingesta de datos de Búsqueda de Azure. El comando se basa en la característica de indexadores integrados que rastrea un origen de datos existente, y crea y carga documentos basados en el conjunto de filas que se encuentra en el origen de datos.

Con el Asistente, la importación de datos es una construcción con tres partes:

- una conexión de origen de datos
- un índice de destino en el que se cargan los datos (el asistente puede generarlo automáticamente)
- una programación que se ejecuta ahora o a intervalos regulares

Para usar un indexador o el comando **Importar datos**, el origen de datos principal debe ser uno de los orígenes de datos compatibles: Base de datos SQL de Azure, bases de datos relacionales de SQL Server en una máquina virtual de Azure o DocumentDB de Azure.

Solo se puede importar desde una única tabla, vista o estructura de datos equivalente. Primero debe crear esta estructura de datos en el origen de datos de la aplicación para obtener las entradas de metadatos y datos correctas en el índice de búsqueda.

Puede probar este flujo de trabajo con datos de ejemplo. Visite [Introducción a Búsqueda de Azure en el Portal de Azure](search-get-started-portal.md) para empezar.

##Configuración de la importación de datos

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Abra el panel del servicio Búsqueda de Azure. A continuación se presentan algunas formas de buscar el panel.
	- En la barra de salto, haga clic en **Inicio**. La página principal dispone de iconos para cada servicio de su suscripción. Haga clic en el icono para abrir el panel de servicio.
	- En la barra de salto, haga clic en **Examinar** > **Filtrar por** > **Servicios de búsqueda** para buscar el servicio de búsqueda en la lista.

3. En el panel del servicio, verá una barra de comandos en la parte superior, incluso uno para **Importar datos**. Haga clic en **Importar datos** para abrir la hoja Importar datos.

4. Haga clic en **Conectar a los datos** para especificar una definición de origen de datos usada por un indexador. Las opciones incluyen:
	- 	El origen de datos existente hace referencia a una definición de origen de datos creada anteriormente para un indexador. Si ya tiene indexadores definidos en el servicio de búsqueda, puede volver a usar una definición de origen de datos para otra importación.
	- 	Se usa SQL Azure para especificar una conexión de origen de datos a una Base de datos SQL en Azure o a una base de datos de SQL Server en una máquina virtual de Azure. 
	- 	DocumentDB se usa para especificar una conexión de origen de datos para ese tipo de origen de datos. 

   Para SQL Azure y DocumentDB, la base de datos debe existir en la suscripción. Puede pegar una cadena de conexión si la conoce o elegir un origen de datos creado anteriormente por alguien que tenga privilegios de escritura para la suscripción.

5. Haga clic en **Personalizar índice de destino** al finalizar el índice predeterminado.
	- Es obligatorio la propiedad **Key**. El campo que se selecciona para la clave debe ser un campo de cadena que contiene valores únicos.
	- El tipo y el nombre de campo suelen rellenarse automáticamente. Puede cambiar el tipo de datos.
	- Seleccione los atributos para cada campo:
		- Retrievable devuelve el campo en los resultados de búsqueda.
		- Filterable permite que se haga referencia al campo en las expresiones de filtro.
		- Sortable permite que el campo se use en una ordenación.
		- Facetable permite que el campo se use en la navegación con facetas.
		- Searchable permite la búsqueda de texto completo.
	- Haga clic en la pestaña **Analizador** si desea especificar un analizador de lenguaje en el nivel de campo. Consulte [Creación de un índice para documentos en varios idiomas](search-language-support.md) para obtener más información.
	- Haga clic en **Proveedor de sugerencias** si desea habilitar las sugerencias de autocompletar o de consulta anticipada.

6. Haga clic en **Importar los datos** para ejecutar la operación de importación mediante la opción Ejecutar ahora, o configurar una programación periódica.

La operación de importación de datos que acaba de completar ha creado un indexador en segundo plano. Ahora puede editar el indexador directamente para cambiar cualquiera de sus componentes.
	
##Edición de un indexador existente

En el panel de servicios, haga doble clic en el icono del indexador para mostrar una lista de todos los indexadores creados para su suscripción. Haga doble clic en uno de ellos para ejecutarlo, editarlo o eliminarlo.

<!---HONumber=AcomDC_0224_2016-->