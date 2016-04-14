<properties 
	pageTitle="Creación de una aplicación de búsqueda geoespacial con Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube" 
	description="Cree una aplicación de búsqueda geoespacial con Bing y Búsqueda de Azure, un servicio de búsqueda hospedado en la nube en Microsoft Azure." 
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
	ms.date="02/18/2016" 
	ms.author="heidist"/>

# Crear una aplicación de búsqueda geoespacial usando Búsqueda de Azure

En este tutorial explicamos cómo agregar la búsqueda geoespacial a las aplicaciones web usando la Búsqueda de Azure y Mapas de Bing. Con la búsqueda geoespacial, puede buscar destinos de búsqueda a una distancia determinada de un punto (por ejemplo, todos los restaurantes que estén en un radio de 5 km de su ubicación actual). Esta capacidad geoespacial de Búsqueda de Azure admite técnicas de creación de mapas usadas habitualmente. Por ejemplo, si quiere usar formas poligonales en una aplicación inmobiliaria que muestra casas en venta dentro de unos límites poligonales, puede hacerlo fácilmente usando OData o nuestra sencilla sintaxis de búsqueda.

Si quiere más información, vea este vídeo de Channel 9 sobre [Búsqueda de Azure y datos geoespaciales](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data).

![][7]

Para crear la aplicación, aprovecharemos el servicio de creación de mapas de Bing para obtener las coordenadas geográficas de las direcciones cargadas desde un archivo CSV y luego almacenar los datos resultantes en un índice de búsqueda.

Este tutorial se basa en la [Búsqueda de Azure: demostración de Adventure Works](http://azuresearchadventureworksdemo.codeplex.com). Si todavía no ha visto la demostración, empiece aquí para aprender a crear un índice y llamar a la API de Búsqueda de Azure desde una aplicación web.

<a id="sub-1"></a>
## Requisitos previos

+	Visual Studio 2012 o posterior con ASP.NET MVC 4 y SQL Server instalados. Puede descargar las ediciones Express gratuitas si aún no tiene instalado el software: [Visual Studio 2013 Express](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) y [Microsoft SQL Server 2014 Express](http://msdn.microsoft.com/evalcenter/dn434042.aspx).
+	Un servicio de Búsqueda de Azure. Necesitará el nombre del servicio de búsqueda y la clave de administrador. Consulte [Creación de un servicio Búsqueda de Azure en el portal](search-create-service-portal.md) para obtener más información.
+	Un servicio de mapa de Bing y una clave para obtener acceso. En la sección siguiente verá las instrucciones.
+	[Muestra de búsqueda geográfica de Búsqueda de Azure en CodePlex](https://azuresearchgeospatial.codeplex.com/). En la pestaña Origen, haga clic en **Descargar** para obtener un archivo ZIP de la solución. 

    ![][12]

Esta solución contiene dos proyectos:

+	**StoreIndexer** crea un índice de Búsqueda de Azure y carga datos.
+	**AdventureWorksWebGeo** es una aplicación basada en MVC4 que consulta el índice de Búsqueda de Azure y muestra ubicaciones de tiendas en un mapa de Bing.

[AZURE.INCLUDE [Para completar este tutorial, deberá tener una cuenta de Azure:](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## Mapas de Bing

Vamos a usar la API de Mapas de Bing para dos cosas.

+ **Direcciones de geocodificación**: en los datos tenemos direcciones (ciudad, estado, código postal), pero también queremos las coordenadas de longitud y latitud de una dirección para poder hacer búsquedas geoespaciales. Si queremos obtener las coordenadas, usaremos la API DataFlow de Mapas de Bing para enviar un lote de direcciones y obtener las coordenadas. Al usar la cuenta de prueba de Bing, estamos limitados a enviar 50 direcciones cada vez, pero esto será suficiente para este tutorial.

+ **Mapas de Bing:** cuando se ejecute la aplicación, usaremos Mapas de Bing para mostrar ubicaciones de tiendas superpuestas sobre un mapa de Bing.

### Crear una cuenta para Mapas de Bing

1. Vaya al portal de [Mapas de Bing](https://www.bingmapsportal.com/) y cree una cuenta nueva. Escriba la información para crear la cuenta.

2. Cuando haya creado la cuenta, elija **Crear o ver claves** y escriba la información para crear una clave. En esta demostración, puede elegir **Clave de prueba**.

3. Haga clic en **Enviar**; debería ver los detalles de la clave de su aplicación de Mapas de Bing. Apunte la clave porque la usaremos después.

<a id="sub-3"></a>
## Obtener las coordenadas geográficas de las direcciones en C# usando la API DataFlow de Mapas de Bing

En este paso, usaremos la API DataFlow de Mapas de Bing para obtener las coordenadas geográficas de las direcciones de varias tiendas de bicicletas de todo el mundo.

Estos datos provienen de un archivo CSV llamado store\_locations.csv que se encuentra en el origen que se descargó antes. Si abre este archivo en un editor de texto o en Excel, verá que tiene una columna de identificador para cada tienda, el nombre de la tienda y su dirección.

Analicemos el código que explica cómo funciona esto.

1. Abra la solución AdventureWorksGeo en Visual Studio, expanda el proyecto **StoreIndexer** en el Explorador de soluciones y abra Program.cs. Puesto que ya analizamos la creación de índices en [Búsqueda de Azure: demostración de Adventure Works](http://azuresearchadventureworksdemo.codeplex.com/), se omitirá la discusión sobre cómo funciona en Program.cs.

2. Vaya a la función **Main** y fíjese en que llama a **ApplyStoreData**. Pase a esta función y explore el código.

3. **ApplyStoreData** carga datos de un archivo CSV llamado "store\_locations.csv" en una System.Data.DataTable.

    Este archivo contiene todas las tiendas, incluidas las direcciones que queremos cargar en Búsqueda de Azure. Al procesar una iteración en todas las filas del archivo, podemos crear un conjunto de **indexOperations** que luego se insertan en un índice de Búsqueda de Azure (creado anteriormente en la función **CreateStoresIndex()**).

    Si luego se fija en el índice, verá que el campo **GeoPt** que contendrá la longitud y la latitud de cada tienda está vacío. Esto nos lleva al paso siguiente de la función **Main**.

5. Pase a la función **ExtractAddressInfoToXML()**. Esta función extrae la información de las direcciones del archivo store\_locations.csv y la carga en un archivo XML que tiene un formato que Mapas de Bing puede aceptar para obtener las coordenadas geográficas. Cuando se ha creado el archivo, se envía para su procesamiento a DataFlow de Mapas de Bing llamando a la función **GeoCoding.CreateJob**.

6. Como el proceso de obtención de coordenadas geográficas puede llevar tiempo, existe un bucle que llama a **GeoCoding.CheckStatus** cada 10 segundos para ver si se ha completado el trabajo. Una vez completado, los resultados se descargan llamando a **GeoCoding.DownloadResults** en una clase de direcciones.

7. El último paso es reunir estas direcciones con coordenadas geográficas y enviarlas a Búsqueda de Azure. Vamos a ver cómo se hace esto abriendo la función **UpdateStoreData**.

  **UpdateStoreData** usa la acción **@search.action: merge** para actualizar el campo de ubicación de tipo Edm.GeographyPoint con las coordinadas de longitud y latitud que se acaban de descargar de Mapas de Bing. Esto lo hace buscando el identificador de tienda, que es la clave única del documento que hay en el índice "tiendas", y combinando este dato nuevo en el documento existente.

8. Antes de ejecutar la aplicación, agregue la información de Búsqueda de Azure y de la API de Mapas de Bing abriendo App.config y actualizando los valores de "SearchServiceName", "SearchServiceApiKey" y "BingMapsAPI" con los de su servicio de Búsqueda de Azure y los de la API de Mapas de Bing. En el nombre del servicio de búsqueda, si su servicio es "mysearch.search.windows.net", tendría que escribir "mysearch".

9. Haga clic con el botón derecho en el proyecto **StoreIndexer** y elija **Depurar** | **Iniciar nueva instancia** para ejecutarlo.

<a id="sub-4"></a>
## Agregar la creación de mapas a una aplicación de MVC4 usando Búsqueda de Azure y Mapas de Bing

En este paso, vamos a compilar y ejecutar la aplicación de búsqueda en un explorador web que cargará la búsqueda de tiendas y luego la trazará encima de un mapa de Bing.

1.	En Visual Studio, abra la solución de la demostración de Búsqueda de Azure denominada AdventureWorksGeo.sln. 
	
2.	Haga clic con el botón derecho en **AdventureWorksWebGeo** en el Explorador de soluciones y seleccione **Establecer como proyecto de inicio**.
	
3.	Abra Web.config en este proyecto y actualice los valores de "SearchServiceName", "SearchServiceApiKey" y "BingMapsAPI" con los de su servicio de Búsqueda de Azure y los de la API de Mapas de Bing. En el nombre del servicio de búsqueda, si su servicio es "mysearch.search.windows.net", tendría que escribir "mysearch".

4.	Guarde Web.config.
	
5.	Presione **F5** para abrir el proyecto. Siga estos pasos de [Solución de problemas](#err-mvc) si recibe un error de compilación.

Como puede ver, las tiendas aparecen como puntos superpuestos en el mapa. Haga clic en una de estas tiendas y verá un mensaje emergente que contiene los detalles de la tienda. Toda esta información viene de un índice de Búsqueda de Azure llamado "tiendas" que se creó en los pasos previos.

<a id="sub-5"></a>
## Explorar AdventureWorksWebGeo

El proyecto **AdventureWorksWebGeo** nos muestra cómo se puede usar ASP.NET MVC 4 para interactuar con Búsqueda de Azure para compilar una aplicación de creación de mapas que aproveche la búsqueda geográfica. En esta sección, revisaremos elementos individuales del código de la aplicación para ver lo que hacen.

1.	En el Explorador de soluciones, expanda **AdventureWorksWeb** | **Controlador** y abra HomeController.cs Se llama a la función **Index()** cuando se inicia la aplicación y se carga la página de índice. En esta función, la API de Mapas de Bing se carga desde Web.config y se pasa a la vista Índice como ViewBag.BingAPI.

2.	Abra Index.cshtml desde **Vistas** | **Inicio**.

3.	Este archivo sigue el método típico para agregar Mapas de Bing a una aplicación web, pero debemos destacar algunas cosas:

+	ViewBag del controlador se usa para cargar las credenciales del mapa usando: credenciales: '@ViewBag.BingAPI' 

+	Cuando el mapa se ha cargado, se hace una publicación JQuery $.post en la función **Buscar** de HomeController haciendo referencia a: /home/search

+	La función **Buscar** recupera las ubicaciones de las tiendas, que luego se reciben y agregan como marcadores en el Mapa de Bing.

4.	Abra HomeController.cs en **Controladores** y fíjese en la función **Buscar**. Vea que hace una llamada a \_storeSearch.Search(lat, lon, 10000). Esto hará que se ejecute una consulta para buscar todas las tiendas que se encuentren a 10.000 km de la latitud (lat) y la longitud (lon) especificadas. Los resultados de esta consulta se procesan y luego se envían de vuelta a la vista índice para que se procesen como marcadores superpuestos en el mapa de Bing.

Con esto finaliza la demostración. Ya ha visto las operaciones principales que necesitará conocer para elaborar una aplicación de ASP.NET MVC4 basada en mapas usando Búsqueda de Azure.


<a id="err-mvc"></a>
## Procedimiento para resolver "No se pudo cargar el archivo o el ensamblado System.Web.Mvc"

Al compilar AdventureWorksWeb, si recibe el mensaje "No se puede cargar el archivo o ensamblado 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' ni una de sus dependencias", intente seguir estos pasos para resolver el error.

1. Abra la Consola del administrador de paquetes: **Herramientas** | **Administrador de paquetes de NuGet** | **Consola del administrador de paquetes**
2. En el símbolo del sistema PM>, escriba "Update-package -reinstall Microsoft.AspNet.Mvc".
3. Cuando se le pida volver a cargar el archivo, elija **Sí a todo**.
4. Recompile la solución y presione **F5** de nuevo.

<a id="next-steps"></a>
## Pasos siguientes

Si quiere ampliar conocimientos, puede agregar más capacidades a la aplicación de creación de mapas. Por ejemplo, puede agregar:

+ Un cuadro de búsqueda para permitir a los usuarios buscar determinadas tiendas.

+ Facetas para permitir a los usuarios filtrar por país o provincia.

+ Las áreas de selección dibujadas por el usuario permiten a los usuarios dibujar un área en el mapa para especificar en qué regiones buscar. Luego el área se filtra con Búsqueda de Azure usando la API de intersección geográfica y se traza en el mapa.


<!--Anchors-->
[Prerequisites]: #sub-1
[Bing Maps]: #sub-2
[Geocode Addresses in C# using Bing Maps DataFlow API]: #sub-3
[Add Mapping to an MVC4 Application using Azure Search and Bing Maps]: #sub-4
[Explore AdventureWorksWebGeo]: #sub-5
[Next steps]: #next-steps


<!--Image references-->
[7]: ./media/search-create-geospatial/AzureSearch-geo1-App.PNG
[12]: ./media/search-create-geospatial/AzureSearch_Create2_CodeplexDownload.PNG

<!---HONumber=AcomDC_0224_2016-->