<properties
	pageTitle="Hospedar un sitio web Ruby on Rails en una máquina virtual Linux | Microsoft Azure"
	description="Configure y hospede un sitio web basado en Ruby on Rails en Azure usando una máquina virtual de Linux."
	services="virtual-machines"
	documentationCenter="ruby"
	authors="rmcmurray"
	manager="wpickett"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="web"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="01/09/2016"
	ms.author="robmcm"/>





#Aplicación web de Ruby on Rails en una máquina virtual de Azure

En este tutorial se muestra cómo hospedar un sitio web Ruby on Rails en Azure usando una máquina virtual de Linux.

Este tutorial se validó con Ubuntu Server 14.04 LTS. Si utiliza una distribución de Linux diferente, es posible que tenga que modificar los pasos para instalar Rails.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


## Creación de una máquina virtual de Azure

Empiece por crear una máquina virtual de Azure con una imagen de Linux.

Para crear la máquina virtual, puede usar el Portal de Azure clásico o la interfaz de línea de comandos (CLI) de Azure.

### Portal de administración de Azure

1. Iniciar sesión en el [Portal de Azure clásico](http://manage.windowsazure.com)
2. Haga clic en **Nuevo** > **Proceso** > **Máquina virtual** > **Creación rápida**. Seleccione una imagen de Linux.
3. Escriba una contraseña.

Después de aprovisionar la máquina virtual, haga clic en el nombre de la máquina virtual y en **Panel**. Encuentre el extremo de SSH, que aparece en **Detalles de SSH**.

### Azure CLI

Siga los pasos de [Creación rápida de una máquina virtual que ejecuta Linux][vm-instructions]

Después de aprovisionar la máquina virtual, puede obtener el extremo de SSH ejecutando el comando siguiente:

	azure vm endpoint list <vm-name>  

## Instalación de Ruby on Rails

1. Use SSH para conectarse a la máquina virtual.

2. En la sesión de SSH, use los siguientes comandos para instalar Ruby en la máquina virtual:

		sudo apt-get update -y
		sudo apt-get upgrade -y
		sudo apt-get install ruby ruby-dev build-essential libsqlite3-dev zlib1g-dev nodejs -y

	La instalación puede tardar unos minutos. Cuando se complete, use el siguiente comando para comprobar que está instalado Ruby:

		ruby -v

	Devuelve la versión de Ruby que se instaló.

3. Use el siguiente comando para instalar Rails:

		sudo gem install rails --no-rdoc --no-ri

	Utilice las marcas --no-rdoc y --no-ri para omitir la instalación de la documentación, lo que es más rápido.

## Creación y ejecución de una aplicación

Mientras sigue conectado a través de SSH, ejecute los siguientes comandos:

	rails new myapp
	cd myapp
	rails server -b 0.0.0.0 -p 3000

El comando [new](http://guides.rubyonrails.org/command_line.html#rails-new) crea una nueva aplicación Rails. El comando [server](http://guides.rubyonrails.org/command_line.html#rails-server) inicia el servidor web WEBrick que viene con Rails. (Para su uso en producción, quizá desee utilizar un servidor diferente, como Unicorn o Passenger.)

Debería ver una salida similar a la siguiente.

	=> Booting WEBrick
	=> Rails 4.2.1 application starting in development on http://0.0.0.0:3000
	=> Run `rails server -h` for more startup options
	=> Ctrl-C to shutdown server
	[2015-06-09 23:34:23] INFO  WEBrick 1.3.1
	[2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
	[2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000


## Agregación de un extremo

1. Vaya al [Portal de Azure clásico][management-portal] y seleccione su máquina virtual.

	![Lista de máquinas virtuales][vmlist]

2. Seleccione **ENDPOINTS** en la parte superior de la página y, a continuación, haga clic en **+ ADD ENDPOINT** en la parte inferior de la página.

	![Página de extremos][endpoints]

3. En el cuadro de diálogo **ADD ENDPOINT**, seleccione "Add a standalone endpoint" y haga clic en la flecha **Siguiente**

	![Cuadro de diálogo de extremo nuevo][new-endpoint1]

3. En la siguiente página del cuadro de diálogo, escriba la siguiente información:

	* **NAME**: HTTP

	* **PROTOCOL**: TCP

	* **PUBLIC PORT**: 80

	* **PRIVATE PORT**: 3000

	De esta manera se crea un puerto público 80 que dirigirá el tráfico hasta el puerto privado 3000, donde el servidor de Rails está escuchando.

	![Cuadro de diálogo de extremo nuevo][new-endpoint]

4. Haga clic en la marca de verificación para guardar el extremo.

5. Debe aparecer un mensaje que muestre **UPDATE IN PROGRESS**. Después de que el mensaje desaparece, el extremo está activo. Ahora puede probar su aplicación dirigiéndose al nombre de DNS de la máquina virtual. El sitio web que aparece debe ser similar al siguiente:

	![Página predeterminada de Rails][default-rails-cloud]


##<a id="next"></a>Pasos siguientes

En este tutorial, hizo la mayoría de los pasos manualmente. En un entorno de producción, escribiría la aplicación en un equipo de desarrollo y la implementaría en la máquina virtual de Azure. Además, la mayoría de los entornos de producción hospedan la aplicación de Rails conjuntamente con otro proceso de servidor, como Apache o NginX, que controla el enrutamiento de solicitudes a varias instancias de la aplicación de Rails y el servicio a recursos estáticos. Para obtener más información, consulte http://rubyonrails.org/deploy/.

Para obtener más información sobre Ruby on Rails, visite [Ruby on Rails Guides][rails-guides] (Guías de Ruby on Rails).

Para utilizar servicios de Azure desde su aplicación de Ruby, consulte:

* [Almacenamiento de datos no estructurados con blobs][blobs]

* [Almacenamiento de parejas de clave/valor con tablas][tables]

* [Servicio de contenido de alto ancho de banda con la Red de entrega de contenido][cdn-howto]



<!-- WA.com links -->
[blobs]: ../storage-ruby-how-to-use-blob-storage.md

[cdn-howto]: /develop/ruby/app-services/

[management-portal]: https://manage.windowsazure.com/

[tables]: /develop/ruby/how-to-guides/table-service/

[vm-instructions]: virtual-machines-linux-tutorial.md


<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/

[sqlite3]: http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailscloud.png

[vmlist]: ./media/virtual-machines-ruby-rails-web-app-linux/vmlist.png

[endpoints]: ./media/virtual-machines-ruby-rails-web-app-linux/endpoints.png

[new-endpoint]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint.png

[new-endpoint1]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint1.png

<!---HONumber=AcomDC_0114_2016-->