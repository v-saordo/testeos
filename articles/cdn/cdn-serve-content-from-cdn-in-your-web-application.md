<properties 
	pageTitle="Entrega de contenido desde la red CDN de Azure en su aplicación web" 
	description="Un tutorial que le enseña a usar el contenido de una CDN para mejorar el rendimiento de su aplicación web." 
	services="cdn" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="cdn" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="cephalin"/>

# Entrega de contenido desde la red CDN de Azure en su aplicación web #

> [AZURE.NOTE] Este tutorial se aplica al servicio CDN clásico. Estamos trabajando muy duro escribiendo una actualización para la versión actual del servicio CDN.

Este tutorial le mostrará cómo sacar el máximo partido de la red CDN de Azure para mejorar el alcance y el rendimiento de su aplicación web. La red CDN de Azure puede ayudarle a mejorar el rendimiento de su aplicación web cuando:

- Tiene varios vínculos a contenido estático o semiestático en las páginas.
- La aplicación la utilizan los clientes globalmente.
- Desea descargar tráfico desde el servicio web.
- Desea reducir el número de conexiones de cliente simultáneas al servidor web (en [Unión y minificación](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification) se habla de ello). 
- Si desea aumentar el tiempo de actualización/carga percibido en las páginas.

## Lo qué aprenderá ##

En este tutorial, aprenderá los siguientes procedimientos:

-	[Entrega de contenido estático desde un extremo de la red CDN de Azure](#deploy)
-	[Carga de contenido automatizada desde la aplicación ASP .NET al extremo de la red CDN](#upload)
-	[Configuración de la memoria caché de la red CDN para reflejar la actualización deseada del contenido](#update)
-	[Entrega inmediata de contenido nuevo mediante cadenas de consulta](#query)

## Qué necesita ##

Este tutorial cuenta con los siguientes requisitos previos:

-	Una [cuenta de Microsoft Azure](/account/) activa Puede registrarse para una cuenta de prueba.
-	Visual Studio 2013 con el [SDK de Azure](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) para GUI de administración de blobs
-	[Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409) (utilizado por [Carga de contenido automatizada desde la aplicación ASP .NET al extremo de la red CDN](#upload))

> [AZURE.NOTE] Necesita una cuenta de Azure para completar este tutorial: + Puede [abrir una cuenta de Azure gratis](/pricing/free-trial/?WT.mc_id=A261C142F): obtenga créditos que puede usar para probar los servicios de pago de Azure, e incluso cuando los haya agotado, podrá conservar la cuenta y usar los servicios de Azure gratis, como Sitios web. + Puede [activar los beneficios de suscriptores de MSDN](/pricing/member-offers/msdn-benefits-details/): su suscripción a MSDN le proporciona créditos todos los meses que puede usar para servicios de Azure de pago.

<a name="static"></a>
## Entrega de contenido estático desde un extremo de la red CDN de Azure ##

En esta sección del tutorial, aprenderá a crear una red CDN y a utilizarla para entregar contenido estático. Los pasos principales implicados son los siguientes:

1. Crear una cuenta de almacenamiento
2. Crear una red CDN vinculada a la cuenta de almacenamiento
3. Crear un contenedor de blobs en la cuenta de almacenamiento
4. Cargar contenido en su contenedor de blobs
5. Vincular al contenido que ha cargado mediante su URL de la red CDN

Vamos a verlo. Siga los pasos siguientes para comenzar a utilizar la red CDN de Azure:

1. Para crear un extremo de la red CDN, inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com/). 
1. Cree una cuenta de almacenamiento haciendo clic en **Nuevo > Servicios de datos > Almacenamiento > Creación rápida**. Especifique una URL, una ubicación y haga clic en **Crear cuenta de almacenamiento**. 

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-1.PNG)

	>[AZURE.NOTE] Tenga en cuenta que estoy usando Asia Oriental como región, ya que está lo suficientemente alejada para probar después mi red CDN desde Norteamérica.

2. Cuando el nuevo estado de la cuenta de almacenamiento es **En línea**, cree un nuevo extremo de red CDN que esté vinculado a la cuenta de almacenamiento que ha creado. Haga clic en **Nuevo > Servicios de aplicaciones > CDN > Creación rápida**. Seleccione la cuenta de almacenamiento que ha creado y haga clic en **Crear**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2.PNG)

	>[AZURE.NOTE] Una vez creada la red CDN, el portal de Azure le mostrará su URL y el dominio de origen al que está vinculada. Sin embargo, la configuración del extremo de red CDN puede tardar un poco en propagarse por completo a todas las ubicaciones del nodo.

3. Pruebe el extremo de red CDN para asegurarse de que está en línea haciendo ping en él. Si el extremo de red CDN no se ha propagado en todos los nodos, verá un mensaje similar al siguiente.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-fail.PNG)

	Espere otra hora y vuelva a probarlo. Cuando el extremo de red CDN ha acabado de propagarse a los nodos, debe responder a los pings.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-succeed.PNG)

4. En este punto, ya puede ver dónde el extremo de red CDN determina cuál es el nodo de red CDN más cercano. Desde mi equipo de sobremesa, la dirección IP de respuesta es** 93.184.215.201**. Conéctese a un sitio como [www.ip-address.org](http://www.ip-address.org) y compruebe que el servidor está ubicado en Washington D.C.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-4.PNG)

	Para obtener una lista actualizada de todas las ubicaciones de nodos de la red CDN, consulte [Ubicaciones del nodo de la red de entrega de contenido (CDN) de Azure](http://msdn.microsoft.com/library/azure/gg680302.aspx).

3. De vuelta en el portal de Azure, en la pestaña **CDN**, haga clic en el nombre del extremo de red CDN que acaba de crear.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequerya.PNG)

3. Haga clic en **Habilitar cadena de consulta** para habilitar cadenas de consulta en el caché de la red CDN de Azure. Una vez realizada la habilitación, el mismo vínculo al que se accede con diferentes cadenas de consulta se almacenará en caché como entradas independientes.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequeryb.PNG)

	>[AZURE.NOTE] Aunque no es necesario habilitar la cadena de consulta para esta parte del tutorial, le conviene hacerlo lo antes posible para mayor comodidad ya que cualquier cambio va a tardar tiempo en propagarse al resto de los nodos y no deseará que cualquier contenido no habilitado para cadenas de consulta atasque el caché de la red CDN (la actualización del contenido de la red CDN se tratará después). Averiguará cómo sacar partido de esto en [Entrega inmediata de contenido nuevo mediante cadenas de consulta](#query).

6. En Visual Studio 2013, en el Explorador de soluciones, haga clic en el botón **Conectar a Microsoft Azure**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-5.PNG)

7.  Siga las indicaciones en pantalla para registrarse en su cuenta de Azure.
8.  Una vez registrado, expanda **Microsoft Azure > Almacenamiento > su cuenta de almacenamiento**. Haga clic con el botón derecho en **Blob** y seleccione **Crear contenedor de blobs**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-6.PNG)

8.	Especifique un nombre de contenedor de blobs y haga clic en **Aceptar**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-7.PNG)

9.	En el Explorador de servidores, haga doble clic en el contenedor de blobs que ha creado para administrarlo. Debería ver la interfaz de administración en el panel central.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-8.PNG)

10.	Haga clic en el botón **Cargar blob** para cargar imágenes, scripts u hojas de estilo que utilizan las páginas web en el contenedor de blob. El progreso de carga se mostrará en el** registro de actividades de Azure** y los blobs aparecerán en la vista del contenedor cuando se cargan.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-9.PNG)

11.	Ahora que ha cargado los blobs, debe hacerlos públicos para que pueda obtener acceso a ellos. En el Explorador de servidores, haga clic con el botón derecho en el contenedor y seleccione **Propiedades**. Establezca la propiedad **Acceso de lectura público** en **Blob**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-10.PNG)

12.	Pruebe el acceso público de los blobs mediante el desplazamiento de la URL para uno de los blobs de un explorador. Por ejemplo, puedo probar la primera imagen en mi lista de carga con `http://cephalinstorage.blob.core.windows.net/cdn/cephas_lin.png`.

	Tenga en cuenta que no estoy utilizando la dirección HTTPS dada en la interfaz de administración de blobs en Visual Studio. Al utilizar HTTP, prueba si se puede obtener acceso al contenido públicamente, lo que es un requisito para la red CDN de Azure.

13.	Si puede ver el blob correctamente representado en el explorador, cambie la URL de `http://<yourStorageAccountName>.blob.core.windows.net` a la URL de la red CDN de Azure. En mi caso, para probar la primera imagen en mi extremo de red CDN, utilizo `http://az623979.vo.msecnd.net/cdn/cephas_lin.png`.

	>[AZURE.NOTE] Puede encontrar la URL del extremo de red CDN en el portal de administración de Azure, en la pestaña CDN.

	Si compara el rendimiento del acceso directo de blobs y el acceso a la red CDN, puede ver la ganancia de rendimiento al utilizar la red CDN de Azure. A continuación se muestra la captura de pantalla de las herramientas de desarrollador F12 de Internet Explorer 11 para el acceso de la URL del blob de mi imagen:

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-blob.PNG)

	Y para el acceso de la URL de la red CDN de la misma imagen.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-cdn.PNG)

 	Preste atención a las cifras del período de la **solicitud**, que es el período al primer byte o el tiempo dedicado a enviar la solicitud y recibir la primera respuesta del servidor. Cuando accedo al blob, que está hospedado en la región de Asia Oriental, tarda 266 ms para mí, ya que la solicitud debe atravesar el Océano Pacífico para llegar al servidor. Sin embargo, cuando obtengo acceso a la red CDN de Azure, tarda solo 16 ms, lo que representa prácticamente una **ganancia 20 veces mayor del rendimiento**.
	
15.	Ahora, es solo cuestión de utilizar el nuevo vínculo a la página web. Por ejemplo, puedo agregar la siguiente etiqueta de imagen:

		<img alt="Mugshot" src="http://az623979.vo.msecnd.net/cdn/cephas_lin.png" />

En esta sección, ha aprendido a crear un extremo de red CDN, cargar contenido en él y vincular el contenido de la red CDN en cualquier página web.

<a name="upload"></a>
## Carga de contenido automatizada desde la aplicación ASP .NET al extremo de la red CDN ##

Si desea cargar fácilmente todo el contenido estático a su aplicación web de ASP .NET a su extremo de red CDN, o si implementa su aplicación web usando la entrega continua (para ver un ejemplo, consulte [Entrega continua para Servicios en la nube de Azure](../cloud-services/cloud-services-dotnet-continuous-delivery.md)), puede usar Azure PowerShell para automatizar la sincronización de los últimos archivos de contenido en los blobs de Azure cada vez que implemente la aplicación web. Por ejemplo, puede ejecutar el script en [Carga de archivos de contenido desde la aplicación ASP .NET a los blobs de Azure](http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a) para cargar todos los archivos de contenido en una aplicación de ASP .NET. Para utilizar este script:

4. En el menú **Inicio**, ejecute **Microsoft Azure PowerShell**.
5. En la ventana de Azure PowerShell, ejecute `Get-AzurePublishSettingsFile` para descargar un archivo de configuración de publicación para la cuenta de Azure.
6. Cuando haya descargado el archivo de configuración de publicación, ejecute lo siguiente: 

		Import-AzurePublishSettingsFile "<yourDownloadedFilePath>"

	>[AZURE.NOTE] Cuando haya importado el archivo de configuración de publicación, será la cuenta de Azure predeterminada la que se utilizará en todas las sesiones de Azure PowerShell. Esto significa que los pasos anteriores solo tienen que realizarse una vez.
	
1. Descargue el script desde la [página de descargas](http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a). Guárdelo en la carpeta de proyecto de la aplicación ASP.NET.
2. Haga clic con el botón derecho en el archivo descargado y haga clic en **Propiedades**.
3. Haga clic en **Desbloquear**.
4. Abra una ventana de PowerShell y ejecute lo siguiente:

		cd <ProjectFolder>
		.\UploadContentToAzureBlobs.ps1 -StorageAccount "<yourStorageAccountName>" -StorageContainer "<yourContainerName>"

Este script carga todos los archivos desde las carpetas *\\Content* y *\\Scripts* en la cuenta y contenedor de almacenamiento especificados. Tiene las ventajas siguientes:

-	Replicar automáticamente la estructura del archivo de su proyecto de Visual Studio
-	Crear automáticamente contenedores de blobs cuando se necesite
-	Reutilizar la misma cuenta de almacenamiento de Azure y el extremo de red CDN para varias aplicaciones web, cada una en un contenedor de blobs separado
-	Actualizar fácilmente la red CDN de Azure con nuevo contenido. Para obtener más información sobre la actualización del contenido, consulte [Configuración del caché de la red CDN para reflejar la actualización deseada del contenido](#update).

Para el parámetro `-StorageContainer`, tiene sentido utilizar el nombre de la aplicación web, o el nombre del proyecto de Visual Studio. Aunque he utilizado el "cdn" genérico como el nombre anterior del contenedor, si se utiliza el nombre de la aplicación web, se permite que el contenido relacionado se organice en el mismo contenedor fácilmente identificable.

Cuando el contenido haya finalizado la carga, puede vincularlo a cualquier elemento de su carpeta *\\Content* y *\\Scripts* en el código HTML, como en sus archivos .cshtml, mediante `http://<yourCDNName>.vo.msecnd.net/<containerName>`. Este es un ejemplo de algo que puedo usar en una vista Razor:

	<img alt="Mugshot" src="http://az623979.vo.msecnd.net/MyMvcApp/Content/cephas_lin.png" />

Para un ejemplo de la integración de scripts de PowerShell en su configuración de entrega continua, consulte [Entrega continua para Servicio en la nube de Azure](../cloud-services/cloud-services-dotnet-continuous-delivery.md).

<a name="update"></a>
## Configuración de la memoria caché de la red CDN para reflejar la actualización deseada del contenido ##

Ahora, supongamos que después de haber cargado los archivos estáticos desde la aplicación web en un contenedor de blobs, realiza un cambio en uno de los archivos el proyecto y lo carga de nuevo en el contenedor de blobs. Puede pensar que se actualiza automáticamente en el extremo de red CDN, pero en realidad se siente desconcertado porque no ve la actualización reflejada cuando obtenga acceso a la URL de la red CDN del contenido.

La verdad es que al red CDN no se actualiza automáticamente desde su almacenamiento de blobs, pero lo hace al aplicar una regla de almacenamiento en caché de siete días predeterminada al contenido. Esto significa que una vez que un nodo de red CDN extrae el contenido desde el almacenamiento de blobs, el mismo contenido no se actualiza hasta que expira en el caché.

Las buenas noticias es que puede personalizar la expiración del caché. De igual forma a la mayoría de los exploradores, la red CDN de Azure respecta el período de expiración especificado en el encabezado Cache-Control del contenido. Puede especificar un valor de encabezado Cache-Control personalizado desplazándose al contenedor de blobs en el portal de Azure y modificando las propiedades de los blobs. La captura de pantalla siguiente muestra la expiración del caché establecida en 1 hora (3600 segundos).

![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-updates-1.PNG)

También puede hacer en el script de PowerShell para establecer todos los encabezados de Cache-Control de los blobs. Para el script de la [Carga de contenido automatizada desde la aplicación ASP .NET al extremo de la red CDN](#upload), encuentre el siguiente fragmento de código:

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType} `
        -Force

y modifíquelo de la forma siguiente:

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType; CacheControl="public, max-age=3600"} `
        -Force

Todavía puede esperar al contenido en caché completo de 7 días en la red CDN de Azure para expirar antes de que extraiga el nuevo contenido, con el nuevo encabezado Cache-Control. Esto ilustra el hecho de que los valores de almacenamiento en caché personalizados no ayudan si desea que la actualización del contenido se active de inmediato, como actualizaciones de JavaScript o CSS. Sin embargo, puede resolver este problema cambiando el nombre de los archivos o controlando las versiones a través de cadenas de consulta. Para obtener más información, consulte [Entrega inmediata de contenido nuevo mediante cadenas de consulta](#query).

Por supuesto, hay un tiempo y un lugar para el almacenamiento en caché. Por ejemplo, puede tener contenido que no requiera la actualización frecuente, como los próximos juegos mundiales de fútbol que pueden actualizarse cada tres horas, pero obtener el suficiente tráfico global que desea descargar desde su propio servidor web. Esto puede ser un buen candidato a utilizar el almacenamiento en caché de la red CDN de Azure.

<a name="query"></a>
## Entrega inmediata de contenido nuevo mediante cadenas de consulta ##

En la red CDN de Azure, puede habilitar cadenas de consulta de tal forma que el contenido de URL con cadenas de consulta específicas se almacenen en caché de forma independiente. Esto es una excelente característica que puede utilizar si desea insertar inmediatamente determinadas actualizaciones de contenido a los exploradores de cliente en lugar que el contenido de la red CDN en caché expire. Supongamos que publico mi página web con un número de versión en la URL.
  
	<link href="http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=3.0.0" rel="stylesheet"/>

Cuando publico una actualización de CSS y utilizo un número de versión diferente en mi dirección URL de CSS:

	<link href="http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=3.1.1" rel="stylesheet"/>

Para un extremo de red de CDN con cadenas de consulta habilitadas, las dos URL son únicas y se realizará una nueva solicitud a mi servidor web para recuperar el nuevo *bootstrap.css*. Sin embargo, en un extremo de red de CDN que no tenga cadenas de consulta habilitadas, son las mismas URL y simplemente entregarán el *bootstrap.css* en caché.

El truco aquí consiste en actualizar el número de versión de manera automática. En Visual Studio, esto es fácil de hacer. En un archivo .cshtml donde me gustaría usar el vínculo anterior, puedo especificar un número de versión en función del número de ensamblado.

	@{
	    var cdnVersion = System.Reflection.Assembly.GetAssembly(
	        typeof(MyMvcApp.Controllers.HomeController))
	        .GetName().Version.ToString();
	}
	
	...
	
	<link href="http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=@cdnVersion" rel="stylesheet"/>

Si cambia el número de ensamblado como parte de cada ciclo de publicación, puede asegurarse igualmente de obtener un número de versión único cada vez que publique su aplicación web, que permanecerá igual hasta el siguiente ciclo de publicación. O bien, puede hacer que Visual Studio incremente automáticamente el número de versión de ensamblado cada vez que la aplicación web se crea abriendo *Properties\\AssemblyInfo.cs* en su proyecto de Visual Studio y utilizar `*` en `AssemblyVersion`. Por ejemplo:

	[assembly: AssemblyVersion("1.0.0.*")]

## Información acerca de scripts y hojas de estilos integrados en ASP.NET ##

Con [Aplicaciones web del Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) y [Servicios en la nube de Azure](/services/cloud-services/), obtendrá la mejor integración de red CDN de Azure con [Unión y minificación ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification).

La integración de Servicio de aplicaciones de Azure o Servicios en la nube de Azure con la red CDN de Azure le ofrece las siguientes ventajas:

- Integración de la implementación de contenido (imágenes, scripts y hojas de estilo) como parte del proceso de [implementación continua](../web-sites-publish-source-control.md) de la aplicación web de Azure
- Actualización sencilla de los paquetes de NuGet servidos por CDN, como versiones de jQuery o Bootstrap 
- Administración de la aplicación web y del contenido servido por CDN desde la misma interfaz de Visual Studio

Para ver tutoriales relacionados, consulte: - [Uso de la red CDN de Azure del Servicio de aplicaciones de Azure](../cdn-websites-with-cdn.md) - [Integración de un servicio en la nube con la Red de entrega de contenido (CDN) de Azure](cdn-cloud-service-with-cdn.md)

Sin la integración con Aplicaciones web del Servicio de aplicaciones de Azure o Servicios en la nube de Azure, es posible utilizar la red CDN de Azure para sus uniones de scripts, con las siguientes reservas:

- Debe cargar manualmente los scripts agrupados al almacenamiento de blobs. Una solución programática se propone en [stackoverflow](http://stackoverflow.com/a/13736433).
- En los archivos .cshtml, transforme las etiquetas de CSS/scripts representadas para utilizar la red CDN de Azure. Por ejemplo:

		@Html.Raw(Styles.Render("~/Content/css").ToString().Insert(0, "http://<yourCDNName>.vo.msecnd.net"))

## Más información ##
- [Información general de la red de entrega de contenido (CDN) de Azure](cdn-overview.md)
- [Uso de la red CDN de Azure del Servicio de aplicaciones de Azure](../cdn-websites-with-cdn.md)
- [Integración de un servicio en la nube con la Red de entrega de contenido (CDN) de Azure](cdn-cloud-service-with-cdn.md)
- [Asignación del contenido de la Red de entrega de contenido (CDN) a un dominio personalizado](http://msdn.microsoft.com/library/azure/gg680307.aspx)
- [Uso de la red CDN en Azure](cdn-how-to-use-cdn.md)
 

<!---HONumber=AcomDC_0128_2016-->