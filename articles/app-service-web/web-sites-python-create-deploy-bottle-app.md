<properties 
	pageTitle="Aplicaciones web de Python con Bottle en Azure" 
	description="Un tutorial que indica cómo ejecutar una aplicación web de Python en Aplicaciones web del Servicio de aplicaciones de Azure." 
	services="app-service\web" 
	documentationCenter="python" 
	tags="python"
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/19/2016"
	ms.author="huvalo"/>


# Creación de aplicaciones web con Bottle en Azure

En este tutorial, se describe cómo empezar a ejecutar Python en Aplicaciones web del Servicio de aplicaciones de Azure. Aplicaciones web ofrece hospedaje gratuito limitado y una implementación rápida. Además, ahora también se puede usar Python. A medida que su aplicación crece, puede cambiar a un tipo de hospedaje de pago e integrar el resto de los servicios de Azure.

Creará una aplicación web con el marco web de Bottle (consulte las versiones alternativas de este tutorial para [Django](web-sites-python-create-deploy-django-app.md) y [Flask](web-sites-python-create-deploy-flask-app.md)). Creará la aplicación web en Azure Marketplace, configurará la implementación Git y clonará el repositorio en modo local. A continuación, ejecutará la aplicación web localmente, realizará cambios, los confirmará y los insertará en [Aplicaciones web del Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714). El tutorial muestra cómo llevarlo a cabo en Windows o Mac/Linux.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Requisitos previos

- Windows, Mac o Linux
- Python 2.7 o 3.4
- setuptools, pip, virtualenv (solo en Python 2.7)
- Git
- [Python Tools 2.2 para Visual Studio][] (PTVS) - Nota: opcional

**Nota:** la publicación TFS no se admite actualmente para los proyectos de Python.

### Windows

Si aún no tiene Python 2.7 o 3.4 instalado (32 bits), se recomienda instalar [Azure SDK para Python 2.7] o [Azure SDK para Python 3.4] mediante el instalador de plataforma web. Se instala la versión de 32 bits de Python, setuptools, pip, virtualenv, etc. (Python de 32 bits es lo que se instala en los equipos host de Azure). También puede obtener Python en [python.org].

Para Git, recomendamos [Git para Windows] o [GitHub para Windows]. Si utiliza Visual Studio, puede utilizar la compatibilidad integrada con Git.

También se recomienda instalar [Python Tools 2.2 para Visual Studio]. Esto es opcional, pero si tiene [Visual Studio], incluidas las versiones gratuitas Visual Studio Community 2013 o Visual Studio Express 2013 para web, obtendrá un excelente IDE de Python.

### Mac o Linux:

Debe tener Python y Git instalados, pero asegúrese de que tiene Python 2.7 o 3.4.


## Creación de una aplicación web en el Portal de Azure

El primer paso para crear la aplicación consiste en crear la aplicación web a través del [Portal de Azure](https://portal.azure.com).

1. Inicie sesión en el Portal de Azure, haga clic en el botón **Nuevo** situado en la esquina inferior izquierda. 
3. En el cuadro de búsqueda, escriba "python".
4. En los resultados de búsqueda, seleccione **Bottle** y, a continuación, haga clic en **Crear**.
5. Configure la nueva aplicación Flask, por ejemplo, la creación de un nuevo plan para el Servicio de aplicaciones y un nuevo grupo de recursos para él. A continuación, haga clic en **Crear**.
6. Configure la publicación de Git para la aplicación web recién creada siguiendo las instrucciones de [Implementación continua con GIT en el Servicio de aplicaciones de Azure](web-sites-publish-source-control.md).
 
## Información general de la aplicación

### Contenido del repositorio de Git

A continuación se muestra información general de los archivos que encontrará en el repositorio de Git inicial, que se va a clonar en la sección siguiente.

    \routes.py
    \static\content\
    \static\fonts\
    \static\scripts\
    \views\about.tpl
    \views\contact.tpl
    \views\index.tpl
    \views\layout.tpl

Orígenes principales de la aplicación. Consta de tres páginas (índice, acerca de, contacto) con un diseño principal. El contenido estático y los scripts incluyen bootstrap, jquery, modernizr y respond.

    \app.py

Compatibilidad con servidor de desarrollo local. Úselo para ejecutar la aplicación localmente.

    \BottleWebProject.pyproj
    \BottleWebProject.sln

Archivos de proyecto para su uso con [Python Tools para Visual Studio].

    \ptvs_virtualenv_proxy.py

Proxy IIS para entornos virtuales y compatibilidad con la depuración remota de PTVS.

    \requirements.txt

Paquetes externos necesarios para esta aplicación. El script de implementación instalará pip en los paquetes incluidos en este archivo.
 
    \web.2.7.config
    \web.3.4.config

Archivos de configuración de IIS. El script de implementación utilizará el web.x.y.config adecuado y lo copiará como web.config.

### Archivos opcionales - Implementación de personalización

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../../includes/web-sites-python-customizing-deployment.md)]

### Archivos opcionales - Tiempo de ejecución de Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### Archivos adicionales en el servidor

Algunos archivos existen en el servidor pero no se agregan al repositorio de Git. Estos se crean mediante el script de implementación.

    \web.config

Archivo de configuración de IIS. Se crea desde web.x.y.config en cada implementación.

    \env\

Entorno virtual de Python. Se crea durante la implementación si todavía no existe un entorno virtual compatible en la aplicación web. En los paquetes que aparecen en requirements.txt se instala pip, pero pip omitirá la instalación si los paquetes ya están instalados.

En las tres secciones siguientes se describe cómo continuar con el desarrollo de aplicaciones web en tres entornos diferentes:

- Windows, con Python Tools para Visual Studio
- Windows, con línea de comandos
- Mac/Linux, con línea de comandos


## Web de desarrollo de aplicaciones - Windows - Herramientas de Python para Visual Studio

### Clonación del repositorio

En primer lugar, clone el repositorio mediante la dirección URL proporcionada en el Portal de Azure. Para obtener más información, consulte [Implementación continua mediante GIT en el Servicio de aplicaciones de Azure](web-sites-publish-source-control.md).

Abra el archivo de la solución (.sln) que se incluye en la raíz del repositorio.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-solution-bottle.png)

### Creación de un entorno virtual

Ahora vamos a crear un entorno virtual para el desarrollo local. Haga clic con el botón secundario en **Entornos de Python** y elija **Agregar entorno virtual...**

- Asegúrese de que el nombre del entorno sea `env`.

- Seleccione el intérprete base. Asegúrese de utilizar la misma versión de Python que la seleccionada para la aplicación web (en runtime.txt o en la hoja **Configuración de la aplicación** de la aplicación web en el Portal de Azure).

- Asegúrese de que esté activada la opción para descargar e instalar paquetes.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-add-virtual-env-27.png)

Haga clic en **Crear**. Esto creará el entorno virtual e instalará las dependencias mostradas en requirements.txt.

### Ejecución con el servidor de desarrollo

Presione F5 para iniciar la depuración y el explorador web abrirá automáticamente la página que se ejecuta localmente.

![](./media/web-sites-python-create-deploy-bottle-app/windows-browser-bottle.png)

Puede establecer puntos de interrupción en los orígenes, utilizar las ventanas Inspección, etc. Consulte la [Documentación sobre Python Tools para Visual Studio] para obtener más información sobre las distintas características.

### Realización de cambios

Ahora puede experimentar mediante la realización de cambios en los orígenes o plantillas de la aplicación.

Una vez probados los cambios, confírmelos en el repositorio de Git:

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-commit-bottle.png)

### Instalación de más paquetes

La aplicación puede tener otras dependencias, aparte de Python y Bottle.

Puede instalar paquetes adicionales con pip. Para instalar un paquete, haga clic con el botón secundario en el entorno virtual y elija **Instalar paquete de Python**.

Por ejemplo, para instalar el SDK de Azure para Python, que proporciona acceso al almacenamiento de Azure, al bus de servicio y a otros servicios de Azure, escriba `azure`:

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-install-package-dialog.png)

Haga clic con el botón secundario en el entorno virtual y elija **Generar requirements.txt** para actualizar requirements.txt.

A continuación, confirme los cambios de requirements.txt en el repositorio de Git.

### Implementación en Azure

Para desencadenar una implementación, haga clic en **Sincronizar** o **Insertar**. La sincronización realiza una inserción y una extracción.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-git-push.png)

La primera implementación tardará algún tiempo, ya que crea un entorno virtual, paquetes de instalación, etc.

Visual Studio no muestra el progreso de la implementación. Si desea revisar la salida, consulte la sección [Solución de problemas - Implementación](#troubleshooting-deployment).

Vaya a la dirección URL de Azure para ver los cambios.


## Desarrollo de aplicaciones web - Windows - Línea de comandos

### Clonación del repositorio

En primer lugar, clone el repositorio mediante la dirección URL proporcionada en el Portal de Azure y agregue el repositorio de Azure como remoto. Para obtener más información, consulte [Implementación continua mediante GIT en el Servicio de aplicaciones de Azure](web-sites-publish-source-control.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Creación de un entorno virtual

Vamos a crear un nuevo entorno virtual para fines de desarrollo (no lo agregue al repositorio). Los entornos virtuales de Python no son reubicables, por lo que cada desarrollador que trabaja en la aplicación creará su propio entorno localmente.

Asegúrese de usar la misma versión de Python que la seleccionada para la aplicación web (en runtime.txt o en la hoja Configuración de la aplicación de la aplicación web en el Portal de Azure).

Para Python 2.7:

    c:\python27\python.exe -m virtualenv env

Para Python 3.4:

    c:\python34\python.exe -m venv env

Instale los paquetes externos requeridos por la aplicación. Puede utilizar el archivo requirements.txt en la raíz del repositorio para instalar los paquetes en su entorno virtual:

    env\scripts\pip install -r requirements.txt

### Ejecución con el servidor de desarrollo

Puede iniciar la aplicación en un servidor de desarrollo con el siguiente comando:

    env\scripts\python app.py

La consola mostrará la dirección URL y el puerto al que escucha el servidor:

![](./media/web-sites-python-create-deploy-bottle-app/windows-run-local-bottle.png)

A continuación, abra el explorador web para esa dirección URL.

![](./media/web-sites-python-create-deploy-bottle-app/windows-browser-bottle.png)

### Realización de cambios

Ahora puede experimentar mediante la realización de cambios en los orígenes o plantillas de la aplicación.

Una vez probados los cambios, confírmelos en el repositorio de Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Instalación de más paquetes

La aplicación puede tener otras dependencias, aparte de Python y Bottle.

Puede instalar paquetes adicionales con pip. Por ejemplo, para instalar el SDK de Azure para Python, que proporciona acceso al almacenamiento de Azure, al bus de servicio y a otros servicios de Azure, escriba:

    env\scripts\pip install azure

Asegúrese de actualizar requirements.txt:

    env\scripts\pip freeze > requirements.txt

Confirme los cambios:

    git add requirements.txt
    git commit -m "Added azure package"

### Implementación en Azure

Para desencadenar una implementación, inserte los cambios en Azure:

    git push azure master

Verá la salida del script de implementación, incluida la creación del entorno virtual, la instalación de paquetes y la creación de web.config.

Vaya a la dirección URL de Azure para ver los cambios.


## Desarrollo de aplicaciones web: Mac/Linux - Línea de comandos

### Clonación del repositorio

En primer lugar, clone el repositorio mediante la dirección URL proporcionada en el Portal de Azure y agregue el repositorio de Azure como remoto. Para obtener más información, consulte [Implementación continua mediante GIT en el Servicio de aplicaciones de Azure](web-sites-publish-source-control.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Creación de un entorno virtual

Vamos a crear un nuevo entorno virtual para fines de desarrollo (no lo agregue al repositorio). Los entornos virtuales de Python no son reubicables, por lo que cada desarrollador que trabaja en la aplicación creará su propio entorno localmente.

Asegúrese de utilizar la misma versión de Python que la seleccionada para la aplicación web (en runtime.txt o en la hoja Configuración de la aplicación de la aplicación web en el Portal de Azure).

Para Python 2.7:

    python -m virtualenv env

Para Python 3.4:

    python -m venv env
o pyvenv env

Instale los paquetes externos requeridos por la aplicación. Puede utilizar el archivo requirements.txt en la raíz del repositorio para instalar los paquetes en su entorno virtual:

    env/bin/pip install -r requirements.txt

### Ejecución con el servidor de desarrollo

Puede iniciar la aplicación en un servidor de desarrollo con el siguiente comando:

    env/bin/python app.py

La consola mostrará la dirección URL y el puerto al que escucha el servidor:

![](./media/web-sites-python-create-deploy-bottle-app/mac-run-local-bottle.png)

A continuación, abra el explorador web para esa dirección URL.

![](./media/web-sites-python-create-deploy-bottle-app/mac-browser-bottle.png)

### Realización de cambios

Ahora puede experimentar mediante la realización de cambios en los orígenes o plantillas de la aplicación.

Una vez probados los cambios, confírmelos en el repositorio de Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Instalación de más paquetes

La aplicación puede tener otras dependencias, aparte de Python y Bottle.

Puede instalar paquetes adicionales con pip. Por ejemplo, para instalar el SDK de Azure para Python, que proporciona acceso al almacenamiento de Azure, al bus de servicio y a otros servicios de Azure, escriba:

    env/bin/pip install azure

Asegúrese de actualizar requirements.txt:

    env/bin/pip freeze > requirements.txt

Confirme los cambios:

    git add requirements.txt
    git commit -m "Added azure package"

### Implementación en Azure

Para desencadenar una implementación, inserte los cambios en Azure:

    git push azure master

Verá la salida del script de implementación, incluida la creación del entorno virtual, la instalación de paquetes y la creación de web.config.

Vaya a la dirección URL de Azure para ver los cambios.


## Solución de problemas - Instalación de un paquete

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## Solución de problemas - Entorno virtual

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## Pasos siguientes

Siga estos vínculos para obtener más información acerca de Bottle y Python Tools para Visual Studio:
 
- [Documentación de Bottle]
- [Documentación sobre Python Tools para Visual Studio]

Para obtener información sobre el uso de Almacenamiento de tablas de Azure y MongoDB:

- [Bottle y MongoDB en Azure con Python Tools para Visual Studio]
- [Bottle y Almacenamiento de tablas de Azure en Azure con Python Tools para Visual Studio]

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)


<!--Link references-->
[Bottle y MongoDB en Azure con Python Tools para Visual Studio]: web-sites-python-ptvs-bottle-table-storage.md
[Bottle y Almacenamiento de tablas de Azure en Azure con Python Tools para Visual Studio]: web-sites-python-ptvs-bottle-table-storage.md

<!--External Link references-->
[Azure SDK para Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK para Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git para Windows]: http://msysgit.github.io/
[GitHub para Windows]: https://windows.github.com/
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Visual Studio]: http://www.visualstudio.com/
[Documentación sobre Python Tools para Visual Studio]: http://aka.ms/ptvsdocs
[Documentación de Bottle]: http://bottlepy.org/docs/dev/index.html
 

<!---HONumber=AcomDC_0224_2016-->