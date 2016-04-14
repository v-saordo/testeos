<properties
	pageTitle="Creación de un cuaderno de Jupyter/IPython Notebook | Microsoft Azure"
	description="Obtenga información sobre cómo implementar Jupyter/IPhyton Notebook en una máquina virtual de Linux creada con el modelo de implementación del administrador de recursos en Azure."
	services="virtual-machines"
	documentationCenter="python"
	authors="crwilcox"
	manager="wpickett"
	editor=""
	tags=“azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="python"
	ms.topic="article"
	ms.date="11/10/2015"
	ms.author="crwilcox"/>

# Jupyter Notebook en Azure

El [proyecto Jupyter](http://jupyter.org), anteriormente conocido como el [proyecto IPython](http://ipython.org), proporciona una colección de herramientas para la informática científica mediante eficaces shells interactivos que combinan la ejecución del código con la creación de documentos informáticos activos. Estos archivos del bloc de notas pueden contener texto arbitrario, fórmulas matemáticas, código de entrada, resultados, gráficos, vídeos y cualquier otra clase de contenido multimedia que pueda mostrar cualquier explorador web moderno. Independientemente de si no tiene experiencia con Python y desea aprender en un entorno entretenido e interactivo, o bien desea realizar informática técnica o en paralelo, Jupyter Notebook es una excelente elección.

![Instantánea](./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png) Uso de los paquetes SciPy y Matplotlib para analizar la estructura de una grabación de sonido.


## Dos formas de Jupyter: Azure Notebooks o implementación personalizada
Azure ofrece un servicio que puede usar para [comenzar rápidamente a usar Jupyter ](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx). Si utiliza el servicio de Azure Notebook, puede obtener acceso fácilmente a la interfaz accesible desde la Web de Jupyter a los recursos informáticos escalables con toda la potencia de Python y sus diversas bibliotecas. Debido a que el servicio administra la instalación, los usuarios pueden obtener acceso a estos recursos sin necesidad de que el usuario los administre y configure.

Si el servicio de cuaderno no sirve en el escenario en cuestión, siga leyendo este artículo, que mostrará cómo implementar Jupyter Notebook en Microsoft Azure, mediante máquinas virtuales de Linux.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Creación y configuración de una máquina virtual en Azure

El primer paso es crear una máquina virtual (VM) que se ejecute en Azure. Esta máquina virtual consiste en un sistema operativo completo en la nube y se usará para ejecutar Jupyter Notebook. Azure es capaz de ejecutar máquinas virtuales de Linux y Windows y abarcaremos la configuración de Jupyter en ambos tipos de máquinas virtuales.

### Crear una máquina virtual de Linux y abrir un puerto para Jupyter

Siga las instrucciones que aparecen [aquí][portal-vm-linux] para crear una máquina virtual de la distribución *Ubuntu*. En este tutorial se usa Ubuntu Server 14.04 LTS. Asumiremos que el nombre de usuario es *azureuser*.

Una vez que se implementa la máquina virtual, es necesario abrir una regla de seguridad en el grupo de seguridad de red. En el Portal de Azure, vaya a **Grupos de seguridad de red** y abra la pestaña del grupo de seguridad que corresponde a la máquina virtual. Debe agregar una regla de seguridad de entrada con la configuración siguiente: **TCP** para el protocolo, ***** para el puerto de origen (público) y **9999** para el puerto de destino (privado).

![Instantánea](./media/virtual-machines-python-ipython-notebook/azure-add-endpoint.png)

En el grupo de seguridad de red, haga clic en **Interfaces de red** y anote la **Dirección IP pública** que necesitará para conectarse a la máquina virtual en el paso siguiente.

## Instalación del software necesario en la máquina virtual

Para ejecutar Jupyter Notebook en nuestra máquina virtual, primero debemos instalar Jupyter y sus dependencias. Conéctese a la máquina virtual de Linux con SSH y el par de nombre de usuario/contraseña que eligió cuando creó la máquina virtual. En este tutorial, se usará PuTTY y se conectará desde Windows.

### Instalación de Jupyter en Ubuntu
Instale Anaconda, una popular distribución de Python para ciencia de datos, desde uno de los vínculos proporcionados por [Continuum Analytics](https://www.continuum.io/downloads). En el momento de redactar este documento, los vínculos que aparecen a continuación son las versiones más actualizadas.

#### Instalaciones de Anaconda para Linux
<table>
  <th>Python 3.4</th>
  <th>Python 2.7</th>
  <tr>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh'>64 bits</href>
	</td>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86_64.sh'>64 bits</href>
	</td>
  </tr>
  <tr>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86.sh'>32 bits</href>
	</td>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86.sh'>32 bits</href>
	</td>  
  </tr>
</table>


#### Instalación de Anaconda3 2.3.0 de 64 bits en Ubuntu
Como ejemplo, se trata de cómo puede instalar Anaconda en Ubuntu

	# install anaconda
	cd ~
	mkdir -p anaconda
	cd anaconda/
	curl -O https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh
	sudo bash Anaconda3-2.3.0-Linux-x86_64.sh -b -f -p /anaconda3

	# clean up home directory
	cd ..
	rm -rf anaconda/

	# Update Jupyter to the latest install and generate its config file
	sudo /anaconda3/bin/conda install -f jupyter -y
	/anaconda3/bin/jupyter-notebook --generate-config


![Instantánea](./media/virtual-machines-python-ipython-notebook/anaconda-install.png)


### Configuración de Jupyter y uso de SSL
Después de la instalación, necesitamos un momento para configurar los archivos de configuración de Jupyter. Si experimenta problemas con la configuración de Jupyter, puede resultar útil consultar la [documentación de Jupyter para ejecutar un servidor de Notebook](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html).

A continuación, mediante el comando `cd`, cámbiese al directorio del perfil para crear nuestro certificado SSL y modifique el archivo de configuración de perfiles.

En Linux, use el comando siguiente.

    cd ~/.jupyter

Utilice el comando siguiente para crear el certificado SSL (Linux y Windows).

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Tenga en cuenta que como estamos creando un certificado SSL autofirmado, al conectarse al bloc de notas el explorador le mostrará una advertencia de seguridad. En un uso de producción a largo plazo, querrá utilizar un certificado correctamente firmado asociado a la organización. Como la administración de certificados va más allá del ámbito de esta demostración, de momento nos ceñiremos a un certificado autofirmado.

Además de utilizar un certificado, también debe proporcionar una contraseña para proteger el bloc de notas de un uso no autorizado. Por motivos de seguridad, Jupyter usa contraseñas cifradas en su archivo de configuración, por lo que deberá cifrar primero la contraseña. IPython proporciona una utilidad para hacerlo; en el símbolo del sistema ejecute el comando siguiente.

    /anaconda3/bin/python -c "import IPython;print(IPython.lib.passwd())"

Se le solicitará una contraseña y su confirmación y, luego, se imprimirá la contraseña. Anótela para el paso siguiente.

    Enter password:
    Verify password:
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

A continuación, editaremos el archivo de configuración del perfil, que es el archivo `jupyter_notebook_config.py` del directorio en que se encuentra. Observe que es posible que este archivo no exista. Si es así, simplemente créelo. Estearchivo tiene varios campos que, de manera predeterminada, se convierten en comentario. Puede abrir este archivo con el editor de texto que prefiera y debe asegurase de que al menos tiene el contenido siguiente. Asegúrese de reemplazar la contraseña con el valor sha1 del paso anterior.

    c = get_config()

    # You must give the path to the certificate file.
    c.NotebookApp.certfile = u'/home/azureuser/.jupyter/mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed :wqtraffic on that port
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### Ejecutar Jupyter Notebook

En este momento, estamos preparados para iniciar Jupyter Notebook. Para ello, vaya al directorio en el que desea almacenar los cuadernos e inicie el servidor Jupyter Notebook con el comando siguiente.

    /anaconda3/bin/jupyter-notebook

Ahora debería poder tener acceso a Jupyter Notebook en la dirección `https://[PUBLIC-IP-ADDRESS]:9999`.

La primera vez que se accede a un cuaderno, la página de inicio de sesión pide una contraseña. Y una vez que haya iniciado sesión, verá el "panel de control de Jupyter Notebook", que es el centro de control para todas las operaciones del cuaderno. Desde esta página se pueden crear cuadernos nuevos y abrir los existentes.

![Instantánea](./media/virtual-machines-python-ipython-notebook/jupyter-tree-view.png)

### Uso de Jupyter Notebook

Si hace clic en el botón **Nuevo**, verá que se abre la página siguiente.

![Instantánea](./media/virtual-machines-python-ipython-notebook/jupyter-untitled-notebook.png)

El área marcada con el mensaje `In []:` es el área de entrada y ahí puede escribir cualquier código Python válido, que se ejecutará cuando presione `Shift-Enter` o haga clic en el icono "Play" (el triángulo que apunta a la derecha en la barra de herramientas).

## Los documentos informáticos activos con medios enriquecidos son un potente paradigma.

El propio cuaderno le resultará muy familiar a cualquiera que haya utilizado Python y un procesador de textos, ya que, en cierto modo, es una mezcla de ambas cosas: permite ejecutar bloques de código de Python, pero también permite conservar notas y cualquier otro texto cambiando el estilo de una celda de "Code" a "Markdown" con el menú desplegable de la barra de herramientas.

Jupyter es mucho más que un simple procesador de texto, debido a que permite combinar informática y medios enriquecidos (texto, gráficos, vídeo y prácticamente todos los elementos que se pueden mostrar en un explorador web moderno). Puede combinar texto, código, vídeos, etc.

![Instantánea](./media/virtual-machines-python-ipython-notebook/jupyter-editing-experience.png)

Y con la potencia de las gran cantidad de excelentes bibliotecas de Python para la informática técnica y científica, en la siguiente captura de pantalla, se puede realizar un cálculo simple con la misma facilidad que un análisis de red complejo, todo ello en un único entorno.

Este paradigma de combinar la potencia de la web moderna con la informática activa ofrece muchas posibilidades, y es perfectamente adecuado para la nube; el bloc de notas se puede usar:

* Como bloc de dictado de cálculo para registrar el trabajo de exploración sobre un problema.

* Para compartir resultados con compañeros, ya sea en forma de cálculo "activo" o en formatos para impresión (HTML, PDF),

* Para distribuir y presentar materiales de formación dinámicos que impliquen cálculos, con el fin de que los estudiantes puedan experimentar inmediatamente con el código real, modificarlo y volver a ejecutarlo de manera interactiva.

* Para proporcionar "documentos ejecutables" que presentan los resultados de la investigación de manera que otras puedan reproducirlos, validarlos y ampliarlos inmediatamente.

* Como plataforma de informática de colaboración: varios usuarios pueden iniciar sesión en el mismo servidor de IPython Notebook para compartir una sesión de cálculo dinámica.


Si va al [repositorio][] de código fuente de IPython, encontrará un directorio completo con ejemplos de cuadernos que se pueden descargar para experimentar en su propia máquina virtual de Jupyter de Azure. Solo tiene que descargar los archivos `.ipynb` desde el sitio y cargarlos en el panel de la máquina virtual del bloc de notas de Azure (o descargarlos directamente en la máquina virtual).

## Conclusión

Jupyter Notebook brinda una sólida interfaz para tener acceso de manera interactiva a la potencia del ecosistema Python en Azure. Abarca una amplia variedad de casos de uso que incluye la exploración simple y el aprendizaje de Python, análisis de datos y visualización, simulación e informática en paralelo. Los documentos de Notebook resultantes contienen un registro completo de los cálculos que se realizan y que se pueden compartir con otros usuarios de Jupyter. Jupyter Notebook se puede usar como aplicación local, pero es perfectamente adecuado para implementaciones de nube en Azure.

las características centrales de Jupyter también está disponibles en Visual Studio mediante [Python Tools para Visual Studio][] (PTVS). PTVS es un complemento gratuito y de código abiertode Microsoft que convierte a Visual Studio unentorno de desarrollo avanzado de Python, que incluye un editor avanzado con la integración de IntelliSense, depuración,creación de perfiles y la informática en paralelo.

## Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de Python](/develop/python/).

[portal-vm-linux]: https://azure.microsoft.com/es-ES/documentation/articles/virtual-machines-linux-tutorial-portal-rm/
[repositorio]: https://github.com/ipython/ipython
[Python Tools para Visual Studio]: http://aka.ms/ptvs

<!---HONumber=AcomDC_1203_2015-->