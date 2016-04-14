<properties
	pageTitle="Introducción a Servicios móviles de Azure para aplicaciones de HTML/JavaScript | Microsoft Azure"
	description="Siga este tutorial para introducirse en el uso de Servicios móviles de Azure para el desarrollo de HTML."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html5"
	ms.devlang="javascript"
	ms.topic="get-started-article" 
	ms.date="11/30/2015"
	ms.author="glenga"/>


# <a name="getting-started"> </a>Introducción a Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

##Información general 

En este tutorial se indica cómo agregar un servicio back-end basado en la nube a una aplicación HTML mediante los Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *Lista de pendientes* que almacena datos de la aplicación en el servicio móvil nuevo. Puede ver la siguiente versión en vídeo de este tutorial.

> [AZURE.VIDEO mobile-get-started-html]
 
La siguiente captura de pantalla muestra la aplicación final:

![][0]

Completar este tutorial es un requisito previo para todos los tutoriales de Servicios móviles para aplicaciones HTML. Para una aplicación de PhoneGap/Cordova, vea la [versión de PhoneGap/Cordova](mobile-services-javascript-backend-phonegap-get-started.md) de este tutorial.

##Requisitos previos

Los siguientes requisitos son necesarios para completar este tutorial.

+ Debe tener uno de los siguientes servidores web ejecutándose en su equipo local:

	+  **En Windows**: IIS Express. IIS Express lo instala el [instalador de plataforma web de Microsoft].
	+  **En MacOS X**: Python, que ya debería estar instalado.
	+  **En Linux**: Python. Debe instalar la [última versión de Python] (información en inglés).

	Puede usar cualquier servidor web para hospedar la aplicación, aunque estos son los servidores web que admiten los scripts descargados.

+ Un servidor web compatible con HTML5.
+ Una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-ES%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F"%20target="_blank). 


## <a name="create-new-service"> </a>Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Creación de una aplicación HTML

Después de crear el servicio móvil, puede seguir una sencilla introducción rápida en el Portal de Azure clásico para crear una aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección, creará una aplicación de HTML que se conecta al servicio móvil.

1.  En el [Portal de Azure clásico], haga clic en **Servicios móviles** y luego en el servicio móvil que acaba de crear.


2. En la pestaña de inicio rápido, haga clic en **Windows** en **Seleccionar plataforma** y, a continuación, expanda **Crear una aplicación HTML**.

   	![][6]

   	Con esto se muestran los tres sencillos pasos requeridos para crear y hospedar una aplicación HTML conectada al servicio móvil.

  	![][7]

3. Haga clic en **Crear tabla TodoItems** para crear una tabla donde almacenar los datos de la aplicación.

4. En **Descargar y ejecutar la aplicación**, haga clic en **Descargar**.

  	De este modo se descargan los archivos del sitio web para la aplicación _To do list_ de ejemplo conectada al servicio móvil. Guarde el archivo comprimido en el equipo local y anote dónde lo guardó.

5. En la pestaña **Configurar**, compruebe que `localhost` ya aparezca en la lista **Permitir solicitudes de nombres de host** en **Compartir recursos entre orígenes (CORS)**. Si no es así, escriba `localhost` en el campo **Nombre de host** y luego haga clic en **Guardar**.

  	![][9]

	> [AZURE.IMPORTANT] Si implementa la aplicación de inicio rápido en un servidor web que no sea localhost, debe agregar el nombre de host del servidor web a la lista **Permitir solicitudes de nombres de host**. Para obtener más información, consulte [Uso compartido de recursos entre orígenes](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx).

## Hospedaje y ejecución de su aplicación HTML

La etapa final de este tutorial consiste en hospedar y ejecutar la aplicación nueva en su equipo local.

1. Busque la ubicación donde guardó los archivos comprimidos del proyecto, expanda los archivos en su equipo e inicie uno de los archivos de comandos siguientes desde la subcarpeta **server**.

	+ **launch-windows** (equipos con Windows)
	+ **launch-mac.command** (equipos con Mac OS X)
	+ **launch-linux.sh** (equipos con Linux)

	> [AZURE.NOTE] En un equipo con Windows, escriba `R` cuando PowerShell le pida que confirme que desea ejecutar el script. Su explorador web podría advertirle de no ejecutar el script porque se ha descargado de Internet. Cuando esto ocurra, debe solicitar que el explorador continúe con la carga del script.

	De este modo se inicia un servidor web en su equipo local para hospedar la nueva aplicación.

2. Abra la URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> en un explorador web para iniciar la aplicación.

3. En la aplicación, escriba un texto significativo, como _Realice el tutorial_, en **Introducir nueva tarea** y, a continuación, haga clic en **Agregar**.

   	![][10]

   	Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y se muestran los datos en la segunda columna de la aplicación.

	> [AZURE.NOTE] Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo page.js.

4. De nuevo en el [Portal de Azure clásico], haga clic en la pestaña **Datos** y luego en la tabla **TodoItems**.

   	![][11]

   	Esto le permite examinar los datos que la aplicación inserta en la tabla.

   	![][12]

## <a name="next-steps"> </a>Pasos siguientes
Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles:

* **[Incorporación de autenticación a la aplicación]** Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

* **[Referencia conceptual de Servicios móviles con HTML/JavaScript]** Obtenga más información sobre cómo usar Servicios móviles con HTML/JavaScript.


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png

[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png


<!-- URLs. -->
[Incorporación de autenticación a la aplicación]: mobile-services-html-get-started-users.md

[Portal de Azure clásico]: https://manage.windowsazure.com/
[instalador de plataforma web de Microsoft]: http://go.microsoft.com/fwlink/p/?LinkId=286333
[última versión de Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
[Referencia conceptual de Servicios móviles con HTML/JavaScript]: mobile-services-html-how-to-use-client-library.md
[Cross-origin resource sharing]: http://msdn.microsoft.com/library/azure/dn155871.aspx
 

<!---HONumber=AcomDC_0128_2016-->