<properties
	pageTitle="Consulta de un índice de Búsqueda de Azure mediante el Explorador de búsqueda en el Portal de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="El Explorador de búsqueda es un enfoque sin código para realizar consultas en un índice de Búsqueda de Azure en el Portal de Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="01/23/2016"
	ms.author="heidist"/>

# Consulta de un índice de Búsqueda de Azure mediante el Explorador de búsqueda en el Portal de Azure
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

El **Explorador de búsqueda** es una herramienta de consulta integrada en el Portal de Azure para realizar consultas sin código en un índice de Búsqueda de Azure. Se conecta a cualquier índice del servicio y proporciona un cuadro de búsqueda para escribir expresiones y cadenas de búsqueda. La sintaxis de consulta válida se genera en función de la entrada que proporcione. Los resultados se muestran en la página del portal.

El Explorador de búsqueda es ideal para aprender la sintaxis de consulta, ejecutar la consulta ad hoc ocasional o refinar una expresión de consulta antes de intentar ponerla en el código. Para usarlo, debe tener un servicio y un índice de Búsqueda de Azure. Consulte [Creación de un servicio Búsqueda de Azure en el Portal de Azure clásico](search-create-service-portal.md) y [Importación de datos en Búsqueda de Azure con el Portal](search-import-data-portal.md) para obtener ayuda con estas tareas.

## Apertura del Explorador de búsqueda

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Abra el panel del servicio Búsqueda de Azure. A continuación se presentan algunas formas de buscar el panel.
	- En la barra de salto, haga clic en **Inicio**. La página principal dispone de iconos para cada servicio de su suscripción. Haga clic en el icono para abrir el panel de servicio.
	- En la barra de salto, haga clic en **Examinar** > **Filtrar por** > **Servicios de búsqueda** para buscar el servicio de búsqueda en la lista.

3. En el panel de servicios, verá una barra de comandos en la parte superior, que incluye uno para el **Explorador de búsqueda**.

  	![][1]

4. Haga clic en el **Explorador de búsqueda** para deslizarlo para abrir la hoja Explorador de búsqueda.
5. Establezca la versión de la API y el índice. El Explorador de búsqueda se conecta automáticamente al primer índice de la lista, pero puede hacer clic en **Índice de cambios** para cambiar a otro. **Definir versión de API** permite especificar las versiones de carácter general o de versión preliminar. Cierta sintaxis de consulta es de solo vista previa.
6. Si ha seguido [Introducción a Búsqueda de Azure](search-get-started-portal.md) para crear y rellenar un índice basado en los datos de Rhode Island del Servicio geológico de Estados Unidos (USGS), puede usar este término de búsqueda para comprobar que los tres mismos resultados se dan en el Explorador de búsqueda: `roger williams +school -building`

Observe la sintaxis de consulta que se genera automáticamente en respuesta a la entrada del término de búsqueda.

![][2]

## Pasos siguientes

En [Buscar documentos](https://msdn.microsoft.com/library/azure/dn798927.aspx), en MSDN, se puede encontrar más información acerca de la sintaxis de consulta y algunos ejemplos de ella.

Visite estos vínculos para conocer otros enfoques sin código para crear o administrar un índice o el servicio de búsqueda:

- [Creación de un servicio de Búsqueda de Azure en el portal](search-create-service-portal.md)
- [Importación de datos en Búsqueda de Azure con el Portal](search-import-data-portal.md)
- [Administrar el servicio de búsqueda en Azure](search-manage.md)

<!--Image References-->
[1]: ./media/search-explorer/AzSearch-SearchExplorer-Btn.png
[2]: ./media/search-explorer/AzSearch-SearchExplorer-Example.png

<!---HONumber=AcomDC_0128_2016-->