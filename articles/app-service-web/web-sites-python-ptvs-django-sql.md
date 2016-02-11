<properties 
	pageTitle="Django y Base de datos SQL en Azure con Python Tools 2.2 para Visual Studio" 
	description="Obtenga información acerca de cómo usar las herramientas de Python para Visual Studio para crear una aplicación web Django que almacene datos en una instancia de base de datos SQL y se pueda implementar en Aplicaciones web del Servicio de aplicaciones de Azure." 
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
	ms.date="11/13/2015"
	ms.author="huguesv"/>




# Django y Base de datos SQL en Azure con Python Tools 2.2 para Visual Studio 

En este tutorial, usaremos las [Herramientas de Python para Visual Studio] a fin de crear una aplicación web de sondeos sencilla mediante una de las plantillas de ejemplo de PTVS. Este tutorial también se encuentra disponible como [vídeo](https://www.youtube.com/watch?v=ZwcoGcIeHF4).

Vamos a aprender a usar una base de datos SQL hospedada en Azure, configurar la aplicación web para usar una base de datos SQL y publicar la aplicación web en [Aplicaciones web del Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Consulte el [Centro para desarrolladores de Python] para tener acceso a más artículos que tratan sobre el desarrollo de Aplicaciones web del Servicio de aplicaciones de Azure con PTVS mediante el uso de marcos web Bottle, Flask y Django, con MongoDB, Almacenamiento de tablas de Azure y los servicios de Base de datos MySQL y SQL. Si bien estos artículos se centran en el Servicio de aplicaciones, los pasos son similares a los que se aplican para desarrollar [Servicios en la nube de Azure].

## Requisitos previos

 - Visual Studio 2013 o 2015
 - [Python Tools 2.2 para Visual Studio]
 - [Python Tools 2.2 para archivos VSIX de ejemplo de Visual Studio]
 - [Herramientas del SDK de Azure para 2013] o [Herramientas del SDK de Azure para VS 2015]
 - [Python 2.7 de 32 bits]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Creación del proyecto

En esta sección, vamos a crear un proyecto de Visual Studio con la utilización de una plantilla de ejemplo. Vamos a crear un entorno virtual e instalar los paquetes necesarios. Vamos a crear una base de datos local con sqlite. A continuación, ejecutaremos la aplicación web localmente.

1.  En Visual Studio, seleccione **Archivo** y **Nuevo proyecto**.

1.  Las plantillas de proyecto de los archivos VSIX de ejemplo de PTVS se encuentran disponibles en **Python**, **Samples** (Ejemplos). Seleccione **Polls Django Web Project** (Proyecto web de Django para sondeos) y haga clic en OK (Aceptar) para crear el proyecto.

  	![Cuadro de diálogo Nuevo proyecto](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  Se le pedirá que instale paquetes externos. Seleccione **Instalar en un entorno virtual**.

  	![Cuadro de diálogo Paquetes externos](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  Seleccione **Python 2.7** como intérprete de base.

  	![Cuadro de diálogo Agregar entorno virtual](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  Haga clic con el botón derecho en el nodo del proyecto y seleccione **Python**, **Django Sync DB** (Base de datos de sincronización de Django).

  	![Comando Base de datos de sincronización de Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  A continuación, se abrirá la consola de administración de Django. Siga las indicaciones para crear un usuario.

    Se creará una base de datos sqlite en la carpeta del proyecto.

  	![Ventana de la Consola de administración de Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Presione <kbd>F5</kbd> para confirmar que la aplicación funciona.

1.  Haga clic en **Iniciar sesión** en la barra de navegación de la parte superior.

  	![Explorador web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Escriba las credenciales del usuario que ha creado al sincronizar la base de datos.

  	![Explorador web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Haga clic en **Create Sample Polls** (Crear sondeos de ejemplo).

  	![Explorador web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  Haga clic en un sondeo y vote.

  	![Explorador web](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

## una Base de datos SQL

Para la base de datos, vamos a crear una base de datos SQL de Azure.

Siga estos pasos para crear una base de datos.

1.  Inicie sesión en el [Portal de Azure].

1.  En la parte inferior del panel de navegación, haga clic en **NUEVO**, haga clic en **Datos + almacenamiento** > **Base de datos SQL**.

  	<!-- ![New Button](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png) -->

1.  Configure la nueva Base de datos SQL mediante la creación de un nuevo grupo de recursos y seleccione la ubicación adecuada para él.

  	<!-- ![Quick Create SQL Database](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png) -->

1.  Una vez creada la Base de datos SQL, haga clic en **Abrir en Visual Studio** en la hoja de la base de datos.
2.  Haga clic en **Configurar el firewall**.
3.  En la hoja **Configuración de firewall**, agregue una regla de firewall con **IP INICIAL** e **IP FINAL** establecidos en la dirección IP pública de su equipo de desarrollo. Haga clic en **Guardar**.

	De esta forma, se permitirán las conexiones al servidor de la base de datos desde el equipo de desarrollo.

4.  De nuevo en la hoja de la base de datos, haga clic en **Propiedades** y en **Mostrar cadenas de conexión de la base de datos**.

2.  Use el botón Copiar para colocar el valor de **ADO.NET** en el Portapapeles.

## Configuración del proyecto

En esta sección, vamos a configurar nuestra aplicación web para usar la base de datos SQL que acabamos de crear. También vamos a instalar paquetes adicionales de Python necesarios para utilizar bases de datos SQL con Django. A continuación, ejecutaremos la aplicación web localmente.

1.  En Visual Studio, abra **settings.py** desde la carpeta *ProjectName*. Pegue temporalmente la cadena de conexión en el editor. La cadena de conexión tiene el siguiente formato:

        Server=<ServerName>,<ServerPort>;Database=<DatabaseName>;User ID=<UserName>;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

Edite la definición de `DATABASES` para usar los valores anteriores.

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<UserName>',
                'PASSWORD': '{your_password_here}',
                'HOST': '<ServerName>',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

1.  En el Explorador de soluciones, en **Entornos de Python**, haga clic con el botón derecho en el entorno virtual y seleccione **Instalar paquete de Python**.

1.  Instale el paquete `pyodbc` con **pip**.

  	![Cuadro de diálogo Instalar paquete de Python](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  Instale el paquete `django-pyodbc-azure` con **pip**.

  	![Cuadro de diálogo Instalar paquete de Python](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  Haga clic con el botón derecho en el nodo del proyecto y seleccione **Python**, **Django Sync DB** (Base de datos de sincronización de Django).

    De esta forma, se crearán las tablas para la base de datos SQL que hemos creado en la sección anterior. Siga las indicaciones para crear un usuario, que no tiene que coincidir con el usuario de la base de datos sqlite creada en la primera sección.

  	![Ventana de la Consola de administración de Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Presione `F5` para ejecutar la aplicación. Los sondeos creados con **Create Sample Polls** (Crear sondeos de ejemplo) y los datos enviados al votar se serializarán en la base de datos SQL.


## Publicación de aplicación web del Servicio de aplicaciones de Azure

El SDK de Azure .NET ofrece una forma fácil de implementar la aplicación web en Aplicaciones web del Servicio de aplicaciones de Azure.

1.  En el **Explorador de soluciones**, haga clic con el botón derecho en el nodo del proyecto y seleccione **Publicar**.

  	<!-- ![Publish Web Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png) -->

1.  Haga clic en **Aplicaciones web de Microsoft Azure**.

1.  Haga clic en **Nuevo** para crear una aplicación web nueva.

1.  Rellene los campos siguientes y haga clic en **Crear**.
	-	**Nombre de aplicación web**
	-	**Plan del Servicio de aplicaciones**
	-	**Grupos de recursos**
	-	**Región**
	-	Deje **Servidor de base de datos** establecido en **No hay base de datos**

  	<!-- ![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png) -->

1.  Acepte todos los valores predeterminados y haga clic en **Publicar**.

1.  El explorador web se abrirá automáticamente en la aplicación web publicada. La aplicación web ahora debe funcionar según lo previsto, con el uso de la base de datos **SQL** hospedada en Azure.

    ¡Enhorabuena!

  	![Explorador web](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

## Pasos siguientes

Siga estos vínculos para obtener más información sobre las herramientas de Python para Visual Studio, Django y Base de datos SQL.

- [Documentación sobre Python Tools para Visual Studio]
  - [Proyectos web]
  - [Proyectos de servicio en la nube]
  - [Depuración remota en Microsoft Azure]
- [Documentación de Django]
- [Base de datos SQL]

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)


<!--Link references-->
[Centro para desarrolladores de Python]: /develop/python/
[Servicios en la nube de Azure]: ../cloud-services-python-ptvs.md

<!--External Link references-->
[Portal de Azure]: https://portal.azure.com
[Herramientas de Python para Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python Tools 2.2 para archivos VSIX de ejemplo de Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Herramientas del SDK de Azure para 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Herramientas del SDK de Azure para VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190
[Documentación sobre Python Tools para Visual Studio]: http://aka.ms/ptvsdocs
[Depuración remota en Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Proyectos web]: http://go.microsoft.com/fwlink/?LinkId=624027
[Proyectos de servicio en la nube]: http://go.microsoft.com/fwlink/?LinkId=624028
[Documentación de Django]: https://www.djangoproject.com/
[Base de datos SQL]: /documentation/services/sql-database/

<!---HONumber=AcomDC_1203_2015-->