<properties 
	pageTitle="Flask y Almacenamiento de tablas de Azure en Azure con Python Tools 2.2 para Visual Studio" 
	description="Obtenga información acerca de cómo usar las herramientas de Python para Visual Studio para crear una aplicación web de Flask que almacene datos en Almacenamiento de tablas de Azure y se pueda implementar en Aplicaciones web del Servicio de aplicaciones de Azure." 
	services="app-service\web"
	tags="python"
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/20/2016"
	ms.author="huvalo"/>




# Flask y Almacenamiento de tablas de Azure en Azure con Python Tools 2.2 para Visual Studio 

En este tutorial, usaremos las [Herramientas de Python para Visual Studio] a fin de crear una aplicación web de sondeos sencilla mediante una de las plantillas de ejemplo de PTVS. Este tutorial también se encuentra disponible como [vídeo](https://www.youtube.com/watch?v=qUtZWtPwbTk).

La aplicación web de sondeos define una abstracción para su repositorio, por lo que puede alternar fácilmente entre los diferentes tipos de repositorios (en memoria, almacenamiento de tablas de Azure y MongoDB).

Facilitaremos información acerca de cómo crear una cuenta de almacenamiento de Azure, cómo configurar la aplicación web para usar el Almacenamiento de tablas de Azure y cómo publicar la aplicación web en [Aplicaciones web del Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Consulte el [Centro para desarrolladores de Python] para tener acceso a más artículos que tratan sobre el desarrollo de Aplicaciones web del Servicio de aplicaciones de Azure con PTVS mediante el uso de marcos web Bottle, Flask y Django, con MongoDB, Almacenamiento de tablas de Azure y los servicios de Base de datos MySQL y SQL. Si bien estos artículos se centran en el Servicio de aplicaciones, los pasos son similares a los que se aplican para desarrollar [Servicios en la nube de Azure].

## Requisitos previos

 - Visual Studio 2013 o 2015
 - [Python Tools 2.2 para Visual Studio]
 - [Python Tools 2.2 para archivos VSIX de ejemplo de Visual Studio]
 - [Herramientas del SDK de Azure para 2013] o [Herramientas del SDK de Azure para VS 2015]
 - [Python 2.7 de 32 bits] o [Python 3.4 de 32 bits]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Creación del proyecto

En esta sección, vamos a crear un proyecto de Visual Studio con la utilización de una plantilla de ejemplo. Vamos a crear un entorno virtual e instalar los paquetes necesarios. A continuación, vamos a ejecutar la aplicación localmente con el repositorio en memoria predeterminado.

1.  En Visual Studio, seleccione **Archivo** y **Nuevo proyecto**.

1.  Las plantillas de proyecto de los archivos VSIX de ejemplo de PTVS se encuentran disponibles en **Python**, **Samples** (Ejemplos). Seleccione **Polls Flask Web Project** (Proyecto web de Flask para sondeos) y haga clic en OK (Aceptar) para crear el proyecto.

  	![Cuadro de diálogo Nuevo proyecto](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)

1.  Se le pedirá que instale paquetes externos. Seleccione **Instalar en un entorno virtual**.

  	![Cuadro de diálogo Paquetes externos](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)

1.  Seleccione **Python 2.7** o **Python 3.4** como intérprete de base.

  	![Cuadro de diálogo Agregar entorno virtual](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)

1.  Presione `F5` para confirmar que la aplicación funciona. De forma predeterminada, la aplicación usa un repositorio en memoria que no requiere ninguna configuración. Todos los datos se pierden cuando el servidor web se detiene.

1.  Haga clic en **Create Sample Polls** (Crear sondeos de ejemplo) y, a continuación, haga clic en un sondeo y vote.

  	![Explorador web](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

## Creación de una cuenta de almacenamiento de Azure

Necesita una cuenta de almacenamiento de Azure para usar operaciones de almacenamiento. Siga estos pasos para crear una cuenta de almacenamiento.

1.  Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. Haga clic en el icono **Nuevo** situado en la parte superior izquierda del portal y haga clic en **Datos y almacenamiento** > **Cuenta de almacenamiento**. Haga clic en **Crear** y, a continuación, asigne un nombre único a la cuenta de almacenamiento y cree un [grupo de recursos](../resource-group-overview.md) nuevo para ella.

  	<!-- ![New Button](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzurePlusNew.png) -->Una vez creada la cuenta de almacenamiento, el botón **Notificaciones** emitirá el mensaje **CORRECTO** en color verde y la hoja de la cuenta de almacenamiento se abrirá para mostrar que pertenece al nuevo grupo de recursos que ha creado.

5. Haga clic en la parte **Configuración** de la hoja de la cuenta de almacenamiento. Anote el nombre de la cuenta y la clave principal.

	Necesitamos esta información para configurar el proyecto en la sección siguiente.

## Configuración del proyecto

En esta sección, vamos a configurar nuestra aplicación para usar la cuenta de almacenamiento que acabamos de crear. Observaremos cómo obtener la configuración de la conexión en el Portal de Azure. A continuación, ejecutaremos la aplicación localmente.

1.  En Visual Studio, haga clic con el botón derecho en el Explorador de soluciones y seleccione **Propiedades**. Haga clic en la pestaña **Depurar**.

  	![Configuración de depuración del proyecto](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)

1.  Establezca los valores de las variables del entorno que necesita la aplicación en **Depurar comando del servidor**, **Entorno**.

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    De esta forma se definirán las variables del entorno para **Iniciar depuración**. Si desea que las variables se definan al **Iniciar sin depurar**, establezca los mismos valores en **Ejecutar comando del servidor**.

    De forma alternativa, puede definir las variables del entorno con el Panel de control de Windows. Se trata de una opción más conveniente si desea evitar que las credenciales se almacenen en el archivo de proyecto / de código fuente. Tenga en cuenta que necesitará reiniciar Visual Studio para que los nuevos valores del entorno estén disponibles en la aplicación.

1.  El código que implementa el repositorio de Almacenamiento de tablas de Azure se encuentra en **models/azuretablestorage.py**. Consulte la [documentación] para obtener más información sobre cómo usar el servicio Tabla en Python.

1.  Presione `F5` para ejecutar la aplicación. Los sondeos creados con **Create Sample Polls** (Crear sondeos de ejemplo) y los datos enviados al votar se serializarán en el Almacenamiento de tablas de Azure.

	> [AZURE.NOTE] El entorno virtual de Python 2.7 pueden producir una interrupción de excepción en Visual Studio. Presione `F5` para continuar cargando el proyecto web.

1.  Vaya a la página **Acerca de** para comprobar que la aplicación usa el repositorio de **Almacenamiento de tablas de Azure**.

  	![Explorador web](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

## Exploración del Almacenamiento de tablas de Azure

Es fácil ver y editar tablas de almacenamiento con el Explorador de servidores en Visual Studio. En esta sección, vamos a utilizar el Explorador de servidores para ver el contenido de las tablas de la aplicación de sondeos.

> [AZURE.NOTE] Para esto es necesario que estén instaladas las Herramientas de Microsoft Azure, que se encuentran disponibles como parte del [SDK de Azure para .NET].

1.  Abra el **Explorador de servidores**. Expanda **Azure**, **Almacenamiento**, la cuenta de almacenamiento y, a continuación, **Tablas**.

  	<!-- ![Server Explorer](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorer.png) -->

1.  Haga doble clic en la tabla **sondeos** u **opciones** para ver el contenido de la tabla en una ventana de documento, así como las entidades agregar/quitar/editar.

  	<!-- ![Table Query Results](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorerTable.png) -->

## Publicación de aplicación web del Servicio de aplicaciones de Azure

El SDK de Azure .NET ofrece una forma fácil de implementar la aplicación web en el Servicio de aplicaciones de Azure.

1.  Haga clic en **Aplicaciones web de Microsoft Azure**.

1.  Haga clic en **Nuevo** para crear una aplicación web nueva.

1.  Rellene los campos siguientes y haga clic en **Crear**.
	-	**Nombre de aplicación web**
	-	**Plan del Servicio de aplicaciones**
	-	**Grupos de recursos**
	-	**Región**
	-	Deje **Servidor de base de datos** establecido en **No hay base de datos**

  	<!-- ![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonCreateWebSite.png) -->

1.  Acepte todos los valores predeterminados y haga clic en **Publicar**.

1.  El explorador web se abrirá automáticamente en la aplicación web publicada. Si navega hasta la página Acerca de, observará que usa el repositorio **En memoria** y no el repositorio de **Almacenamiento de tablas de Azure**.

    Esto se debe a que las variables del entorno no están definidas en la instancia de Aplicaciones web del Servicio de aplicaciones de Azure, por lo que usa los valores predeterminados especificados en **settings.py**.

## Configuración de la instancia de Aplicaciones web

En esta sección, vamos a configurar las variables del entorno para la instancia de Aplicaciones web.

1.  En el **Portal de Azure**, abra la hoja de la aplicación web haciendo clic en [Examinar](https://portal.azure.com) > **Aplicaciones web** > el nombre de la aplicación web.

1.  En la hoja de la aplicación web, haga clic en **Toda la configuración** y en **Configuración de la aplicación**.

  	<!-- ![Top Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteTopMenu.png) -->

1.  Desplácese hacia abajo hasta la sección **Configuración de la aplicación** y defina los valores para **REPOSITORY\_NAME**, **STORAGE\_NAME** y **STORAGE\_KEY**, como se describe en la sección anterior.

  	<!-- ![App Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png) -->

1. Haga clic en **GUARDAR**, en **REINICIAR** y, por último, en **EXAMINAR**.

  	<!-- ![Bottom Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureBottomMenu.png) -->

1.  La aplicación web ahora debe funcionar según lo previsto, con la utilización del repositorio de **Almacenamiento de tablas de Azure**.

    ¡Enhorabuena!

  	![Explorador web](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

## Pasos siguientes

Siga estos vínculos para obtener más información acerca de Python Tools para Visual Studio, Flask y Almacenamiento de Tabla de Azure.

- [Documentación sobre Python Tools para Visual Studio]
  - [Proyectos web]
  - [Proyectos de servicio en la nube]
  - [Depuración remota en Microsoft Azure]
- [Documentación de Flask]
- [Almacenamiento de Azure]
- [SDK de Azure para Python]
- [Uso del servicio de almacenamiento de tablas desde Python]

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)


<!--Link references-->
[Centro para desarrolladores de Python]: /develop/python/
[Servicios en la nube de Azure]: ../cloud-services-python-ptvs.md
[documentación]: ../storage-python-how-to-use-table-storage.md
[Uso del servicio de almacenamiento de tablas desde Python]: ../storage-python-how-to-use-table-storage.md

<!--External Link references-->
[Azure Portal]: https://portal.azure.com
[SDK de Azure para .NET]: http://azure.microsoft.com/downloads/
[Herramientas de Python para Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python Tools 2.2 para archivos VSIX de ejemplo de Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Herramientas del SDK de Azure para 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Herramientas del SDK de Azure para VS 2015]: http://go.microsoft.com/fwlink/?linkid=518003
[Python 2.7 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentación sobre Python Tools para Visual Studio]: http://aka.ms/ptvsdocs
[Documentación de Flask]: http://flask.pocoo.org/
[Depuración remota en Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Proyectos web]: http://go.microsoft.com/fwlink/?LinkId=624027
[Proyectos de servicio en la nube]: http://go.microsoft.com/fwlink/?LinkId=624028
[Almacenamiento de Azure]: http://azure.microsoft.com/documentation/services/storage/
[SDK de Azure para Python]: https://github.com/Azure/azure-sdk-for-python
 

<!---HONumber=AcomDC_0224_2016-->