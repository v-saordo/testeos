<properties
	pageTitle="Instalación de Python y el SDK - Azure"
	description="Obtenga información para instalar Python y el SDK para usarlos con Azure."
	services=""
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="huvalo"/>

# Instalación de Python y el SDK

Python es muy fácil de configurar en Windows y viene preinstalado en Mac y Linux. En esta guía se realizará un recorrido por la instalación y preparación de la máquina para su uso con Azure.

## ¿Qué es el SDK de Azure para Python?

El SDK de Azure para Python incluye componentes que le permiten desarrollar, implementar y administrar aplicaciones de Python para Azure. Específicamente, el SDK de Azure para Python incluye lo siguiente:

* **Las bibliotecas de clientes Python para Azure**. Estas bibliotecas de clases proporcionan una interfaz para tener acceso a características de Azure, como bus de servicio y almacenamiento y administrar recursos de Azure, como cuentas de almacenamiento, máquinas virtuales, etc.
* **Los emuladores de Azure (solo Windows)**. Los emuladores de proceso y almacenamiento son emuladores locales de los servicios en la nube y los servicios de administración de datos que le permiten probar localmente una aplicación. Los emuladores de Azure solo se ejecutan en Windows.

## Qué es Python y qué versión usar

Existen varios modelos de intérpretes Python disponibles, entre los ejemplos se incluyen los siguientes:

* CPython: El intérprete Python estándar y que se usa con más frecuencia.
* IronPython: El intérprete Python que se ejecuta en .Net/CLR.
* Jython: El intérprete Python que se ejecuta en JVM.

Solo **CPython** se prueba y es compatible con el SDK de Azure para Python y los servicios de Azure como Sitios web y Servicios en la nube. Recomendamos las versiones 2.7 o 3.4.

## ¿Dónde obtener Python?

Existen diferentes formas de obtener CPython:

* Directamente desde [www.python.org][]
* Desde una distribución con reputación como [www.continuum.io][], [www.enthought.com][] o [www.activestate.com][]
* Creación a partir del origen.

A menos que tenga una necesidad específica, recomendamos las dos primeras opciones tal y como se describe a continuación.

## Instalación en Windows, Linux y MacOS (solo bibliotecas de cliente)

Si ya tiene instalado Python, puede usar pip para instalar una agrupación de todas las bibliotecas de cliente en su entorno de Python 2.7 o Python 3.3+ existente. Esto descargará los paquetes del [Índice de paquetes de Python][] (PyPI).

Tenga en cuenta que es posible que deba usar el comando `sudo` en Linux y MacOS. `sudo pip install azure`.

	pip install azure

A partir de la versión 1.0.0, las bibliotecas se han dividido en varios paquetes. Ahora solo puede instalar los paquetes que necesita o la agrupación.

Para instalar las bibliotecas de cliente de tiempo de ejecución de almacenamiento de Azure:

	pip install azure-storage

Para instalar las bibliotecas de cliente de tiempo de ejecución de Bus de servicio de Azure:

	pip install azure-servicebus

Para instalar las bibliotecas de cliente de Administrador de recursos de Azure (ARM):

	pip install azure-mgmt

Para instalar las bibliotecas de cliente de Administración de servicios de Azure (ASM):

	pip install azure-servicemanagement-legacy


## Instalación en Windows (Python, emuladores de Azure y bibliotecas de cliente)

Puede usar el instalador de la plataforma web para simplificar la instalación. Se incluye CPython de [www.python.org][].

* [SDK de Microsoft Azure para Python 2.7][]
* [SDK de Microsoft Azure para Python 3.4][]

**Nota**: En Windows Server, para descargar el instalador de WebPI es posible que tenga que configurar los ajustes ESC de IE (Inicio/Herramientas administrativas/Administrador del servidor/Servidor local), luego hacer clic en **Configuración de seguridad mejorada de IE** y establecerlo como Desactivado).

### Python 2.7

El instalador de WebPI proporciona todo lo que necesita para desarrollar aplicaciones de Azure para Python.

![how-to-install-python-webpi-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

Una vez que se haya completado la instalación, escriba `python` cuando se le solicite para asegurarse de que el proceso se ha realizado sin interrupciones. Según la forma en la que haya realizado la instalación, es posible que tenga que establecer la variable "path" para buscar la versión correcta de Python:

![how-to-install-python-win-run-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

Una vez completada la instalación, debe tener las bibliotecas de clientes y de Python disponibles en la ubicación predeterminada:

		C:\Python27\Lib\site-packages\azure


### Python 3.4

El instalador de WebPI proporciona todo lo que necesita para desarrollar aplicaciones de Azure para Python.

![how-to-install-python-webpi-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)

Una vez que se haya completado la instalación, escriba "python" cuando se le solicite para asegurarse de que el proceso se ha realizado sin interrupciones. Según la forma en la que haya realizado la instalación, es posible que tenga que establecer la variable "path" para buscar la versión correcta de Python:

![how-to-install-python-win-run-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

Una vez completada la instalación, debe tener las bibliotecas de clientes y de Python disponibles en la ubicación predeterminada:

		C:\Python34\Lib\site-packages\azure

### Desinstalación en Windows

Los productos WebPI de **SDK de Azure para Python** no son aplicaciones en el sentido habitual, sino una colección de productos distintos como Python 2.7/3.4 de 32 bits, bibliotecas de cliente de Azure para Python, etc. que se empaquetan juntos. Una consecuencia de esto es que no existe un desinstalador convencional propio, por lo que tendrá que quitar los programas que instale individualmente desde el Panel de control de Windows.

Si desea volver a instalar el **SDK de Azure para Python**, simplemente abra un símbolo del sistema de PowerShell como administrador y ejecute el siguiente comando:

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

A continuación, vuelva a ejecuta WebPI.

## Instalación de otros paquetes

[Python Package Index][] (Índice de paquetes de Python, PyPI) dispone de una abundante selección de bibliotecas de Python. Si ha optado por instalar una distribución, ya dispondrá de los bits más interesantes para diversos escenarios que van desde el desarrollo web a la informática técnica.


## Python Tools para Visual Studio

[Python Tools para Visual Studio][] (PTVS) es un complemento gratuito de OSS de Microsoft que convierte VS en un IDE de Python completo:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

El uso de PTVS es opcional, pero es recomendable, ya que le proporciona compatibilidad con soluciones o proyectos de Web y Python, depuración, creación de perfiles, ventana interactiva, edición de plantillas e IntelliSense.

PTVS también simplifica la implementación en Microsoft Azure, con soporte para la implementación en [Servicios en la nube][] y [Sitios web][].

PTVS funciona con su instalación de Visual Studio 2013 o 2015 existente. Para obtener documentación, descargas y discusiones, consulte [Python Tools para Visual Studio].

## Escenarios de Python Azure para Linux y MacOS

Para Linux o Mac OS, estos son los escenarios principales de Azure que se admiten:

1. Consumo de servicios de Azure mediante bibliotecas de clientes para Python

2. Ejecución de la aplicación en la VM de Linux

3. Desarrollar y publicar en sitios web de Azure mediante Git

El primer escenario le permite crear aplicaciones web enriquecidas que aprovechan las capacidades de PaaS de Azure como [almacenamiento de blobs][], [almacenamiento de colas][], [almacenamiento de tablas][], etc. a través de contenedores de Python para la API de REST de Azure. Estos funcionan de forma idéntica en Windows, Mac y Linux. También puede usar estas bibliotecas de cliente desde su equipo de desarrollo local o en una máquina virtual de Linux que se ejecute en Azure.

En el escenario de VM, simplemente inicie la VM de Linux que elija (Ubuntu, CentOS y Suse) y ejecute o administre lo que desee. Por ejemplo, puede ejecutar el bloc de notas o el REPL de [IPython][] en la máquina de Windows/Mac/Linux y configurar el explorador para que apunte a una máquina virtual multiproceso de Linux o Windows que ejecute el motor de IPython en Azure. Consulte el tutorial [Bloc de notas de IPython en Azure][] para obtener más información.

Para obtener información sobre cómo configurar una máquina virtual de Linux, consulte el tutorial [Creación de una máquina virtual que ejecuta Linux][].

Con la implementación de Git puede desarrollar una aplicación web de Python y publicarla en un sitio web de Azure desde cualquier sistema operativo. Cuando inserte el repositorio en Azure, creará automáticamente un entorno virtual y pip instala los paquetes necesarios.

Para obtener más información acerca del desarrollo y la publicación de Sitios web de Azure, consulte los tutoriales para [Creación de sitios web con Django en Azure][], [Creación de sitios web con Bottle][] y [Creación de sitios web con Flask][]. Para obtener información general sobre el uso de cualquier marco de trabajo compatible con WSGI, consulte [Configuración de Python con Sitios web de Azure][].


## Recursos y software adicionales:

* [Distribución de Python de Continuum Analytics][]
* [Distribución de Python de Enthought][]
* [Distribución de Python de ActiveState][]
* [SciPy: un conjunto de bibliotecas de Scientific Python][]
* [NumPy: biblioteca de tipos numéricos para Python][]
* [Django Project: un CMS/marco de trabajo para web maduro][]
* [IPython: un bloc de notas/REPL avanzado para Python][]
* [Bloc de notas de IPython en Azure][]
* [Python Tools para Visual Studio en GitHub][]
* [Centro para desarrolladores de Python](/develop/python/)

[Distribución de Python de Continuum Analytics]: http://continuum.io
[Distribución de Python de Enthought]: http://www.enthought.com
[Distribución de Python de ActiveState]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy: un conjunto de bibliotecas de Scientific Python]: http://www.scipy.org
[NumPy: biblioteca de tipos numéricos para Python]: http://www.numpy.org
[Django Project: un CMS/marco de trabajo para web maduro]: http://www.djangoproject.com
[IPython: un bloc de notas/REPL avanzado para Python]: http://ipython.org
[IPython]: http://ipython.org
[Bloc de notas de IPython en Azure]: virtual-machines-python-ipython-notebook.md
[Servicios en la nube]: cloud-services-python-ptvs.md
[Sitios web]: web-sites-python-ptvs-django-mysql.md
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Python Tools para Visual Studio en GitHub]: https://github.com/microsoft/ptvs
[Python Package Index]: http://pypi.python.org/pypi
[Índice de paquetes de Python]: http://pypi.python.org/pypi
[SDK de Microsoft Azure para Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[SDK de Microsoft Azure para Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Creación de una máquina virtual que ejecuta Linux]: virtual-machines-linux-tutorial.md
[Creación de sitios web con Django en Azure]: web-sites-python-create-deploy-django-app.md
[Creación de sitios web con Bottle]: web-sites-python-create-deploy-bottle-app.md
[Creación de sitios web con Flask]: web-sites-python-create-deploy-flask-app.md
[Configuración de Python con Sitios web de Azure]: web-sites-python-configure.md
[almacenamiento de tablas]: storage-python-how-to-use-table-storage.md
[almacenamiento de colas]: storage-python-how-to-use-queue-storage.md
[almacenamiento de blobs]: storage-python-how-to-use-blob-storage.md

<!---HONumber=Oct15_HO3-->