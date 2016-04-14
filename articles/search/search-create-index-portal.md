<properties
	pageTitle="Creación de un índice de Búsqueda de Azure en el Portal | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Agregue un índice al servicio Búsqueda de Azure, un servicio de búsqueda hospedado en la nube, rellenando definiciones de campo en el Portal de Azure."
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
	ms.date="02/08/2016"
	ms.author="heidist"/>

# Creación de un índice de Búsqueda de Azure en el Portal de Azure
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST API](search-create-index-rest-api.md)

Puede crear rápidamente un índice en Búsqueda de Azure mediante la creación de uno en el Portal de Azure. Usar el portal resulta excelente para las pruebas de prueba de concepto, pero también se puede usar para ver las definiciones de esquema y el uso de recursos para cualquier índice implementado en el servicio.

Para completar esta tarea, asegúrese de disponer de un servicio de Búsqueda de Azure que esté listo para funcionar. Consulte [Crear un servicio de Búsqueda de Azure en el portal](search-create-service-portal.md) si necesita ayuda para configurarlo.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Abra el panel del servicio Búsqueda de Azure. A continuación se presentan algunas formas de buscar el panel.
	- En la barra de salto, haga clic en **Inicio**. La página principal dispone de iconos para cada servicio de su suscripción. Haga clic en el icono para abrir el panel de servicio.
	- En la barra de salto, haga clic en **Examinar** > **Filtrar por** > **Servicios de búsqueda** para buscar el servicio de búsqueda en la lista.

3. En el panel del servicio, verá una barra de comandos en la parte superior, incluso uno para **Agregar índice**.

	Compruebe el nivel de precios. Si tiene la versión gratuita, puede tener hasta tres índices. Es posible que tenga que eliminar uno para liberar espacio.

     ![][1]

4. Para eliminar un índice, haga clic en uno para abrir una hoja. Hacer clic en **Eliminar**.

     ![][2]

5. Para crear un nuevo índice en el portal, haga clic en **Agregar índice** y asígnele un nombre, como *hoteles*.

	Es posible que el índice tarde un minuto en crearse, pero cuando esté listo para funcionar, aparecerá en la lista de índices.

6. Haga clic en **hoteles** para abrir la hoja de la definición del índice.

	Cuando se crea un índice en el portal, se crea un campo obligatorio (Id.) para usted. Este es el campo clave, usado para identificar de forma única cada documento. Hay solo un campo por clave (no hay claves compuestas) y siempre es una cadena.

	Si desea cambiar el nombre del campo de clave, es importante que realice este paso ahora, durante la creación del índice. No podrá cambiar el nombre del campo después de crear el índice.

	![][3]

7. Para editar el nombre del campo, haga clic en la flecha derecha en la lista de campos.

8. Reemplace **id** por **hotelId**.

9. Haga clic en **Aceptar** en cada hoja (campos e índice) para crear el índice.

## Agregar campos

En la Búsqueda de Azure, los atributos de índice, como buscable, facetable y filtrable están habilitados de forma predeterminada. Normalmente, al establecer estos atributos, es habitualmente para desactivar los comportamientos de búsqueda que no tienen sentido (por ejemplo, ordenar o hacer faceting en una descripción).

El portal es diferente. En el portal, los comportamientos de búsqueda están desactivados de forma predeterminada para que pueda seleccionar todos los comportamientos que se aplican en cada campo.

1. Haga clic en **Agregar o editar campos** para agregar más campos. En este ejercicio, recrearemos el índice *hoteles* mencionado en el artículo [Uso de Fiddler con Búsqueda de Azure](search-fiddler.md).

	![][4]

2. Agregar y configurar campos para completar el esquema.

	![][5]

	Consulte [Reglas de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx) y [Tipos de datos compatibles](https://msdn.microsoft.com/library/azure/dn798938.aspx) para obtener información de referencia sobre los nombres y tipos de campo.

    Los atributos de índice constan de los siguientes elementos:

	- **Retrievable** establece si el campo se puede devolver en un resultado de búsqueda.
	- **Filterable** permite el campo que se usará en consultas **$filter**.
	- **Sortable** permite que el campo se use como una opción de ordenación.
	- **Facetable** permite que un campo se use en una estructura de navegación con facetas para el filtrado autodirigido. Normalmente los campos que contienen valores repetitivos que se pueden usar para agrupar varios documentos (por ejemplo, varios documentos que forman parte de una categoría de servicio o producto único) funcionan mejor como facetas.
	- **Key** es el identificador único de cada documento, que se utiliza para buscar documentos. Todos los índices deben tener una clave. Solo un campo puede ser la clave y se debe establecer en Edm.String.
	- **Searchable**: marca el campo como de búsqueda de texto completo.

3. Para quitar los campos que no desee, haga clic con el botón derecho y seleccione **Eliminar**.

4. Haga clic en **Aceptar** para guardar el índice recién definido y luego haga clic en **Aceptar** en la página de Agregar índice para generarlo.


## Pasos siguientes

Aunque el índice esté definido, no estará listo para usarse hasta que cargue los documentos. Suponiendo que se está volviendo a crear el índice de hoteles, utilizado con fines de prueba, puede cargar fácilmente el número pequeño de documentos para ese índice en [Fiddler](search-fiddler.md), según las instrucciones de la sección Cargar documentos de [Uso de Fiddler con Búsqueda de Azure](search-fiddler.md). A continuación, puede seguir los pasos restantes de ese artículo para ejecutar algunas consultas.

Una vez que se encuentre cómodo con el índice básico, considere la posibilidad de agregar un analizador de lenguaje o un proveedor de sugerencias para agregar compatibilidad con varios idiomas o sugerencias de escritura. Ambas funciones se especifican en el esquema de índice. Consulte [Compatibilidad con idiomas](https://msdn.microsoft.com/elibrary/azure/dn879793.aspx) y [Crear índice](https://msdn.microsoft.com/library/azure/dn798941.aspx) para obtener más información.

<!--Image references-->
[1]: ./media/search-create-index-portal/AzureSearch-PortalIndex-1.PNG
[2]: ./media/search-create-index-portal/AzureSearch-PortalIndex-2.PNG
[3]: ./media/search-create-index-portal/AzureSearch-PortalIndex-3.PNG
[4]: ./media/search-create-index-portal/AzureSearch-PortalIndex-4.PNG
[5]: ./media/search-create-index-portal/AzureSearch-PortalIndex-5.PNG

<!---HONumber=AcomDC_0211_2016-->