<properties
	pageTitle="Creación de una aplicación web de Java en Servicio de aplicaciones de Azure | Microsoft Azure"
	description="En este tutorial se muestra cómo implementar una aplicación web de Java en el Servicio de aplicaciones de Azure."
	services="app-service\web"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>
<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="03/04/2016"
	ms.author="robmcm"/>

# Creación de una aplicación web de Java en el Servicio de aplicaciones de Azure

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

En este tutorial se muestra cómo crear una [aplicación web de Java en el Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) mediante el [Portal de Azure](https://portal.azure.com/). El Portal de Azure es una interfaz web que puede usarse para administrar los recursos de Azure.

> [AZURE.NOTE] Necesita una cuenta de Microsoft Azure para completar este tutorial. Si aún no la tiene, puede [activar los beneficios de suscripción a Visual Studio] o bien [registrarse para obtener una evaluación gratuita].
>
> Si desea empezar a usar el Servicio de aplicaciones de Azure antes de suscribirse para obtener una cuenta de Azure, vaya a la [prueba gratuita del Servicio de aplicaciones]. Ahí puede crear de forma inmediata una aplicación web de corta duración para iniciarse en Servicio de aplicaciones, no se requiere tarjeta de crédito y no se establece ningún compromiso.

## Opciones de la aplicación Java

Hay varias maneras de configurar una aplicación de Java en una aplicación web de Servicio de aplicaciones.

1. Cree una aplicación y después configure **Configuración de la aplicación**.

	El Servicio de aplicaciones proporciona varias versiones de Tomcat y Jetty, con la configuración predeterminada. Si la aplicación que va a hospedar funcionará con una de las versiones integradas, este método de configuración de un contenedor web es el más sencillo y cumple su función si todo lo que necesita es cargar un archivo .war en un contenedor web. Para este método, va a crear una aplicación en el Portal de Azure y, después, va a ir a la hoja **Configuración de la aplicación** de la aplicación para elegir la versión de Java junto con el contenedor web de Java deseado. Cuando se usa este método, tanto el código Java como el contenedor web se ejecutan desde Archivos de programa. Los demás métodos colocan el contenedor web y posiblemente el código JVM en espacio del disco. Cuando usa este modelo, no tendrá acceso para editar archivos en esta parte del sistema de archivos, lo que significa que no puede hacer cosas como configurar el archivo *server.xml* o colocar archivos de biblioteca en la carpeta */lib*. Para obtener más información, consulte la sección [Creación y configuración de una aplicación web de Java](#appsettings) más adelante en este tutorial.
	
2. Use una plantilla en Azure Marketplace.

	Azure Marketplace incluye plantillas que crean y configuran automáticamente aplicaciones web de Java con contenedores web de Tomcat o Jetty. Los contenedores web que configuran las plantillas son configurables. Para obtener más información, consulte la sección [Uso de una plantilla de Java en Azure Marketplace](#marketplace) de este tutorial.
 
  
3. Creación de una aplicación y, después, copia y edición manual de los archivos de configuración

	Es posible que quiera hospedar una aplicación Java personalizada que no se implemente en ninguno de los contenedores web proporcionados por el Servicio de aplicaciones. Por ejemplo, estas son algunas de las razones para hacerlo:
	
	* Su aplicación Java requiere una versión de Tomcat o Jetty que no es directamente compatible con el Servicio de aplicaciones o que no se proporciona en la galería.
	* La aplicación Java toma las solicitudes HTTP y no se implementa como un WAR en un contenedor web ya existente.
	* Desea configurar el contenedor web desde el principio. 
	* Quiere usar una versión de Java que no es compatible con el Servicio de aplicaciones y desea cargarla.

	En todos estos casos, puede crear una aplicación mediante el Portal de Azure y después proporcionar los archivos en tiempo de ejecución adecuados manualmente. En este caso, los archivos se deducirán de las cuotas de espacio de almacenamiento para su plan de Servicio de aplicaciones. Para obtener más información, consulte [Carga de una aplicación web de Java personalizada en Azure](web-sites-java-custom-upload.md).

## <a name="portal"></a> Creación y configuración de una aplicación web de Java

En esta sección se muestra cómo crear una aplicación web y configurarla para Java mediante la hoja **Configuración de la aplicación** del portal.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. Haga clic en **Nuevo > Web y móvil > Aplicación web**.

	![](./media/web-sites-java-get-started/newwebapp.png)

4. Escriba un nombre para la aplicación web en el cuadro **Aplicación web**.

	Este nombre debe ser único en el dominio azurewebsites.net porque la dirección URL de la aplicación web será {nombre}.azurewebsites.net. Si el nombre especificado no es único, se muestra un signo de exclamación rojo en el cuadro de texto.

5. Seleccione un **Grupo de recursos** o cree uno nuevo.

	Para obtener más información acerca de los grupos de recursos, consulte [Uso del Portal de Azure para administrar los recursos de Azure](../resource-group-portal.md).

6. Seleccione un **Plan de servicio de aplicaciones/Ubicación** o cree uno nuevo.

	Para obtener más información sobre los planes del Servicio de aplicaciones, consulte [Información general sobre los planes del Servicio de aplicaciones de Azure](../azure-web-sites-web-hosting-plans-in-depth-overview.md).

7. Haga clic en **Crear**.

	![](./media/web-sites-java-get-started/newwebapp2.png)
 
8. Cuando se haya creado la aplicación web, haga clic en **Aplicaciones web > {su aplicación web}**.
 
	![](./media/web-sites-java-get-started/selectwebapp.png)

9. En la hoja **Aplicación web**, haga clic en **Configuración**.

10. Haga clic en **Configuración de la aplicación**.

11. Elija la **versión Java** deseada.

12. Elija la **versión secundaria de Java** deseada. Si selecciona **Más reciente**, la aplicación usará la versión secundaria más reciente que está disponible en el Servicio de aplicaciones para esa versión principal de Java. El elemento **Más reciente** es único para las aplicaciones Java creadas desde **Configuración de la aplicación**. Si crea su aplicación Java desde la galería, tendrá que administrar su propio contenedor y los cambios de JVM.

12. Elija el **contenedor web** deseado. Si selecciona un nombre de contenedor que comienza con **Más reciente**, la aplicación se mantendrá en la versión más reciente de esa versión principal del contenedor web que está disponible en el Servicio de aplicaciones.

	![](./media/web-sites-java-get-started/versions.png)

13. Haga clic en **Guardar**.

	En unos momentos, su aplicación web estará basada en Java y estará configurada para usar el contenedor web seleccionado.

14. Haga clic en **Aplicaciones web > {su nueva aplicación web}**.

15. Haga clic en la **URL** para buscar el nuevo sitio.

	La página web confirma que ha creado una aplicación web basada en Java.


## <a name="marketplace"></a> Uso de una plantilla Java en Azure Marketplace

En esta sección se muestra cómo usar Azure Marketplace para crear una aplicación web de Java. También puede usarse el mismo flujo general para crear una aplicación de API o una aplicación móvil basada en Java.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. Haga clic en **Nuevo > Marketplace**.

	![](./media/web-sites-java-get-started/newmarketplace.png)

3. Haga clic en **Web y móvil**.

	Puede que tenga que desplazarse a la izquierda para ver la hoja **Marketplace** donde puede seleccionar **Web y móvil**.

4. En el cuadro de texto Buscar, escriba el nombre de un servidor de aplicaciones Java, como **Apache Tomcat** o **Jetty** y luego presione Entrar.

5. En los resultados de búsqueda, haga clic en el servidor de aplicaciones Java.

	![](./media/web-sites-java-get-started/webmobilejetty.png)

6. En la primera hoja **Apache Tomcat** o **Jetty**, haga clic en **Crear**.

	![](./media/web-sites-java-get-started/jettyblade.png)

7. En la siguiente hoja **Apache Tomcat** o **Jetty**, escriba un nombre para la aplicación web en el cuadro **Aplicación web**.

	Este nombre debe ser único en el dominio azurewebsites.net porque la dirección URL de la aplicación web será {nombre}.azurewebsites.net. Si el nombre especificado no es único, se muestra un signo de exclamación rojo en el cuadro de texto.

8. Seleccione un **Grupo de recursos** o cree uno nuevo.

	Para obtener más información acerca de los grupos de recursos, consulte [Uso del Portal de Azure para administrar los recursos de Azure](../resource-group-portal.md).

9. Seleccione un **Plan de servicio de aplicaciones/Ubicación** o cree uno nuevo.

	Para obtener más información sobre los planes del Servicio de aplicaciones, consulte [Información general sobre los planes del Servicio de aplicaciones de Azure](../azure-web-sites-web-hosting-plans-in-depth-overview.md).

10. Haga clic en **Crear**.

	![](./media/web-sites-java-get-started/jettyportalcreate2.png)

	En poco tiempo, normalmente menos de un minuto, Azure termina de crear la nueva aplicación web.

11. Haga clic en **Aplicaciones web > {su nueva aplicación web}**.

12. Haga clic en la **URL** para buscar el nuevo sitio.

	![](./media/web-sites-java-get-started/jettyurl.png)

	Tomcat incluye un conjunto predeterminado de páginas, por tanto, si eligió Tomcat, verá una página similar a la del ejemplo siguiente.

	![Aplicación web con Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)

	Si eligió Jetty, verá una página similar al ejemplo siguiente. Jetty no tiene un conjunto de páginas predeterminadas, por lo que se vuelve a usar aquí el mismo JSP usado para un sitio Java vacío.

	![Aplicación web con Jetty](./media/web-sites-java-get-started/jetty.png)

Ahora que ha creado la aplicación web con un contenedor de aplicaciones, consulte la sección [Pasos siguientes](#next-steps) para obtener información sobre cómo cargar la aplicación en la aplicación web.


## Pasos siguientes

En este momento, dispone de un servidor de aplicaciones Java que se ejecuta en la aplicación web en el Servicio de aplicaciones de Azure. Para implementar su propio código en la aplicación web, consulte [Incorporación de una aplicación o página web a la aplicación web de Java](web-sites-java-add-app.md).

Para obtener más información sobre el desarrollo de aplicaciones Java en Azure, consulte el [Centro de desarrolladores de Java](/develop/java/).

<!-- External Links -->
[activar los beneficios de suscripción a Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[registrarse para obtener una evaluación gratuita]: http://go.microsoft.com/fwlink/?LinkId=623901
[prueba gratuita del Servicio de aplicaciones]: http://go.microsoft.com/fwlink/?LinkId=523751

<!---HONumber=AcomDC_0309_2016-->