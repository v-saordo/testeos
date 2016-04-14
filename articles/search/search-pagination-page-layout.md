<properties 
	pageTitle="Cómo paginar los resultados de la búsqueda en Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube" 
	description="Paginación de Búsqueda de Azure, un servicio de búsqueda hospedado en la nube en Microsoft Azure." 
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
	ms.date="02/04/2016" 
	ms.author="heidist"/>

#Cómo paginar los resultados de la búsqueda en Búsqueda de Azure#

Este artículo proporciona orientación sobre la forma de usar la API de REST del servicio Búsqueda de Azure para implementar los elementos habituales de una página de resultados de búsqueda, como recuentos totales, recuperación de documentos, criterios de ordenación y navegación.
 
En todos los casos que se mencionan a continuación, las opciones relacionadas con la página que aportan datos o información a la página de resultados de búsqueda se especifican a través de solicitudes [Buscar documento](http://msdn.microsoft.com/library/azure/dn798927.aspx) que se envían a su servicio Búsqueda de Azure. Las solicitudes incluyen un comando GET, ruta de acceso y parámetros de consulta que informan el servicio de lo que se solicita y de cómo formular la respuesta.

> [AZURE.NOTE] Una solicitud válida incluye una serie de elementos, como una dirección URL del servicio y la ruta de acceso, el verbo HTTP, `api-version`, etc. Para mayor brevedad, hemos acortado los ejemplos para resaltar solo la sintaxis que resulta relevante para la paginación. Consulte la documentación de [API de REST del Servicio de Búsqueda de Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx) para obtener más información acerca de la sintaxis de solicitud.

## Total de resultados y recuentos de página ##

Muestra el número total de resultados devueltos por una consulta y, a continuación, devolver los resultados en bloques más pequeños, es fundamental para casi todas las páginas de búsqueda.

![][1]
 
En Búsqueda de Azure se utilizan los parámetros `$count`, `$top` y `$skip` para devolver esos valores. En el ejemplo siguiente se muestra un ejemplo de solicitud del total de resultados, que se devuelve como `@OData.count`:

    	GET /indexes/onlineCatalog/docs?$count=true

Recuperar documentos en grupos de 15 y mostrar también el total de resultados, comenzando por la primera página:

		GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

Paginar resultados requiere `$top` y `$skip`, donde `$top` especifica cuántos elementos se devuelven en un lote y `$skip` especifica cuántos elementos se omiten. En el ejemplo siguiente, cada página muestra los siguientes 15 elementos, indicado por los saltos incrementales en el parámetro `$skip`.

    	GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

    	GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

    	GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## Diseño  ##

En una página de resultados de búsqueda, puede ser deseable mostrar una imagen en miniatura, un subconjunto de campos y un vínculo a una página de producto completa.

 ![][2]
 
En Búsqueda de Azure se utiliza `$select` y un comando de búsqueda para implementar esta experiencia.

Para devolver un subconjunto de campos con un diseño en mosaico:

    	GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

Las imágenes y los archivos multimedia no se pueden buscar directamente y se deben almacenar en otra plataforma de almacenamiento, como Almacenamiento de blobs de Azure, para reducir los costes. En el índice y los documentos, defina un campo que almacene la dirección URL del contenido externo. Después puede utilizar el campo como referencia de imagen. La dirección URL de la imagen debe estar en el documento.

Para recuperar una página de descripción de producto para un evento **onClick**, use [Buscar documento](http://msdn.microsoft.com/library/azure/dn798929.aspx) para pasar la clave del documento que se va a recuperar. El tipo de datos de la clave es `Edm.String`. En este ejemplo, es *246810*.
   
    	GET /indexes/onlineCatalog/docs/246810

## Ordenar por relevancia, clasificación o precio ##

A menudo, el orden predeterminado se basa en la relevancia, pero es habitual poner a disposición de los clientes otros criterios de ordenación para que puedan reorganizar rápidamente los resultados existentes en un orden diferente.

 ![][3]

En Búsqueda de Azure, la ordenación se basa en la expresión `$orderby` para todos los campos que se indizan como `"Sortable": true.`

La relevancia está estrechamente asociada con perfiles de puntuación. Puede utilizar la puntuación predeterminada, que se basa en el análisis de texto y las estadísticas para ordenar todos los resultados, con las puntuaciones más altas destinadas a documentos con más coincidencias de un término de búsqueda o con coincidencias más importantes.

Los criterios de ordenación alternativos se suelen asociar a eventos **onClick** que llaman a un método que compila el criterio de ordenación. Por ejemplo, con este elemento de página:

 ![][4]

Deberá crear un método que acepte la opción de ordenación seleccionada como entrada y devuelva una lista ordenada de los criterios asociados a esa opción.

 ![][5]
 
> [AZURE.NOTE] Aunque la puntuación predeterminada es suficiente para muchos escenarios, se recomienda basar la relevancia en un perfil de puntuación personalizado. Un perfil personalizado de puntuación le ofrece una forma de aumentar los elementos que son más útiles para su negocio. Consulte [Incorporación de un perfil de puntuación](http://msdn.microsoft.com/library/azure/dn798928.aspx) para obtener más información.

## Navegación por facetas ##

La navegación de búsqueda es habitual en una página de resultados; a menudo se encuentra en un lado o en la parte superior de una página. En Búsqueda de Azure, la navegación por facetas proporciona una búsqueda autodirigida basándose en filtros predefinidos. Consulte [Navegación por facetas en Búsqueda de Azure](search-faceted-navigation.md) para obtener más detalles

## Filtros en el nivel de página ##

Si el diseño de la solución incluye páginas de búsqueda dedicadas para determinados tipos de contenido (por ejemplo, una aplicación comercial en línea que enumera los departamentos en la parte superior de la página), puede insertar una expresión de filtro junto con un evento **onClick** para abrir una página en un estado prefiltrado.

Puede enviar un filtro con o sin expresión de búsqueda. Por ejemplo, la siguiente solicitud filtrará por el nombre de la marca y devolverá solamente los documentos que coincidan con él.

    	GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

Consulte [Buscar documentos (API de Búsqueda de Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx) para obtener más información acerca de las expresiones `$filter`.

## Otras referencias ##

- [API de REST del Servicio Búsqueda de Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx)
- [Operaciones de índice](http://msdn.microsoft.com/library/azure/dn798918.aspx)
- [Operaciones del documento](http://msdn.microsoft.com/library/azure/dn800962.aspx)
- [Vídeo y tutoriales acerca de Búsqueda de Azure](search-video-demo-tutorial-list.md)
- [Navegación por facetas en Búsqueda de Azure](search-faceted-navigation.md)


<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png

<!---HONumber=AcomDC_0224_2016-->