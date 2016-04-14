<properties
   pageTitle="Implementación de la máquina virtual de Azure con Chef | Microsoft Azure"
   description="Obtenga información acerca de cómo usar chef para realizar la implementación y configuración de máquinas virtuales automatizada en Microsoft Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="diegoviso"
   manager="timlt"
   tags="azure-service-management,azure-resource-manager"
   editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services"
ms.tgt_pltfrm="vm-multiple"
ms.devlang="na"
ms.topic="article"
ms.date="05/19/2015"
ms.author="diviso"/>

# Automatización de la implementación de la máquina virtual de Azure con Chef

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Chef es una fantástica herramienta para ofrecer automatización y las configuraciones de estado que desee.

Con nuestra versión de api de nube más reciente, Chef proporciona una perfecta integración con Azure, lo que da la capacidad de aprovisionar e implementar los estados de configuración a través de un único comando.

En este artículo, le mostraré cómo configurar su entorno de Chef para aprovisionar máquinas virtuales de Azure y le guiaré por la creación de una directiva o "CookBook" y, a continuación, la implementación de esta guía en una máquina virtual de Azure.

Vamos a comenzar

## Conceptos básicos de Chef

Antes de comenzar, le sugiero que revise los conceptos básicos de Chef. Hay gran material <a href="http://www.chef.io/chef" target="_blank">aquí</a> y recomiendo una lectura rápida antes de realizar este tutorial. Sin embargo, recapitularé los conceptos básicos antes de empezar.

El diagrama siguiente muestra la arquitectura Chef de alto nivel.

![][2]

Chef tiene tres componentes de arquitectura principales: servidor de Chef, cliente de Chef (nodo) y estación de trabajo de Chef.

El servidor Chef es nuestro punto de administración y hay dos opciones para el servidor Chef: una solución hospedada o una solución local. Vamos a usar una solución hospedada.

El cliente Chef (nodo) es el agente que se encuentra en los servidores que está administrando.

La estación de trabajo Chef es nuestra estación de trabajo de administración donde creamos nuestras directivas y ejecutamos nuestros comandos de administración. Ejecutamos el comando **knife** desde la estación de trabajo Chef para administrar nuestra infraestructura.

También está el concepto de "Guías" y "Recetas". Estas son efectivamente las directivas que definimos y aplicamos a nuestros servidores.

## Preparar la estación de trabajo

En primer lugar, vamos a preparar la estación de trabajo. Estoy usando una estación de trabajo de Windows estándar. Tenemos que crear un directorio para almacenar nuestras guías y archivos de configuración.

En primer lugar, cree un directorio denominado C:\\chef.

A continuación, cree un segundo directorio denominado c:\\chef\\cookbooks.

Ahora necesitamos descargar el archivo de configuración de Azure para que Chef pueda comunicarse con nuestra suscripción de Azure.

Descargue su configuración de publicación [aquí](https://manage.windowsazure.com/publishsettings/).

Guarde el archivo de configuración de publicación en C:\\chef.

##Crear una cuenta de Chef administrada

Regístrese para una cuenta de Chef hospedada [aquí](https://manage.chef.io/signup).

Durante el proceso de registro, se le pedirá que cree una nueva organización.

![][3]

Cuando se cree la organización, descargue el starter kit.

![][4]

> [AZURE.NOTE]Si recibe un mensaje que le advierte que se restablecerán las claves, está bien continuar como si no tuviéramos ninguna infraestructura existente configurada todavía.

El archivo zip del starter kit contiene sus claves y archivos de configuración de la organización.

##Configurar la estación de trabajo de Chef

Extraiga el contenido de chef-starter.zip en C:\\chef.

Copie todos los archivos de chef-starter\\chef-repo.chef en el directorio c:\\chef.

El directorio debería tener ahora un aspecto similar al siguiente ejemplo.

![][5]

Debería tener ahora cuatro archivos, incluido el archivo de publicación de Azure en la raíz de c:\\chef.

Los archivos PEM contienen sus claves privadas de organización y administración para la comunicación mientras que el archivo knife.rb archivo contiene la configuración de knife. Tendremos que editar el archivo knife.rb.

Abra el archivo en el editor que desee y modifique la "ruta de acceso\_cookbook" quitando /../ en la ruta de acceso para que aparezca como se muestra a continuación.

	cookbook_path  ["#{current_dir}/cookbooks"]

Agregue además la siguiente línea que refleja el nombre de su archivo de configuración de publicación de Azure.

	knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

El archivo knife.rb ahora debería ser ahora similar al siguiente ejemplo.

![][6]

Estas líneas asegurarán las referencias de Knife en el directorio de cookbooks en c:\\chef\\cookbooks y también usa nuestro archivo de configuración de publicación de Azure durante las operaciones de Azure.

## Instalar el kit de desarrollo de Chef

A continuación, [descargue e instale](http://downloads.getchef.com/chef-dk/windows) el ChefDK (Kit de desarrollo de Chef) para configurar su estación de trabajo de Chef.

![][7]

Instálelo en su ubicación predeterminada de c:\\opscode. Esta instalación tardará unos 10 minutos.

Confirme que la variable PATH contiene entradas paraC:\\opscode\\chefdk\\bin;C:\\opscode\\chefdk\\embedded\\bin;c:\\users\\yourusername.chefdk\\gem\\ruby\\2.0.0\\bin

Si no están ahí, asegúrese de agregar estas rutas de acceso.

*¡TENGA EN CUENTA QUE EL ORDEN DE LA RUTA DE ACCESO ES IMPORTANTE!* Si las rutas de acceso de opscode no están en el orden correcto, tendrá problemas.

Reinicie la estación de trabajo antes de continuar.

A continuación, instalaremos la extensión de Knife Azure. De esta se proporciona a Knife el "complemento de Azure".

Ejecute el siguiente comando.

	chef gem install knife-azure ––pre

> [AZURE.NOTE]El argumento –pre garantiza que está recibiendo la versión de RC más reciente del complemento Knife Azure que ofrece acceso al conjunto más reciente de API.

Es probable que también se instalen varias dependencias al mismo tiempo.

![][8]


Para garantizar que todo esté configurado correctamente, ejecute el siguiente comando.

	knife azure image list

Si todo está configurado correctamente, verá una lista de imágenes de Azure disponibles en desplazamiento.

¡Enhorabuena! Se ha configurado la estación de trabajo.

##Crear una guía

Chef usa las guías para definir un conjunto de comandos que desea ejecutar en el cliente administrado. La creación de una guía es un proceso sencillo y usamos el comando **chef generate cookbook** para generar nuestra plantilla de Cookbook. Llamaré a mi servidor web de Cookbook ya que me gustaría una directiva que implemente IIS automáticamente.

En el directorio C:\\Chef, ejecute el siguiente comando.

	chef generate cookbook webserver

Esto generará un conjunto de archivos en el directorio C:\\Chef\\cookbooks\\webserver. Ahora tenemos que definir el conjunto de comandos que nos gustaría que nuestro cliente de Chef ejecutara en nuestra máquina virtual administrada.

Los comandos se almacenan en el archivo default.rb. En este archivo, definiré un conjunto de comandos que instala IIS, inicia IIS y copia un archivo de plantilla en la carpeta wwwroot.

Modifique el archivo C:\\chef\\cookbooks\\webserver\\recipes\\default.rb y agregue las siguientes líneas.

	powershell_script 'Install IIS' do
 		action :run
 		code 'add-windowsfeature Web-Server'
	end

	service 'w3svc' do
 		action [ :enable, :start ]
	end

	template 'c:\inetpub\wwwroot\Default.htm' do
 		source 'Default.htm.erb'
 		rights :read, 'Everyone'
	end

Cuando haya terminado, guarde el archivo.

## Crear una plantilla

Como mencionamos anteriormente, tenemos que generar un archivo de plantilla que se usará como nuestra página default.html.

Ejecute el siguiente comando para generar la plantilla.

	chef generate template webserver Default.htm

Ahora navegue al archivo C:\\chef\\cookbooks\\webserver\\templates\\default\\Default.htm.erb. Edite el archivo agregando el código HTML simple "Hello World" y guarde el archivo.



## Cargar la guía en el servidor Chef

En este paso, tomamos una copia de la guía que hemos creado en nuestra máquina local y la cargamos en el servidor hospedado de Chef. Cuando se cargue, la guía aparecerá en la pestaña **Directiva**.

	knife cookbook upload webserver

![][9]

## Implementar una máquina virtual con Knife Azure

Ahora implementaremos una máquina virtual de Azure y aplicaremos la guía "Webserver" que instalará nuestro servicio web de IIS y la página web predeterminada.

Para ello, use el comando **knife azure server create**.

A continuación aparecerá un ejemplo del comando.

	knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Los parámetros son totalmente explicativos. Sustituir sus variables concretas y ejecutar.

> [AZURE.NOTE]Mediante la línea de comandos, estoy automatizando mis reglas de filtro de red de extremos mediante el parámetro –tcp-endpoints. He abierto los puertos 80 y 3389 para proporcionar acceso a mi página web y la sesión de RDP.

Cuando haya ejecutado el comando, pase al Portal de Azure y verá que su máquina comienza a aprovisionar.

![][13]

A continuación aparecerá el símbolo del sistema:

![][10]

Cuando haya finalizado la implementación, deberíamos podremos conectarnos al servicio web en el puerto 80 como habíamos abierto el puerto cuando aprovisionamos la máquina virtual con el comando Knife Azure. Como esta máquina virtual es la única máquina virtual de mi servicio en la nube, la conectaré con la dirección url del servicio en la nube.

![][11]

Como puede observar, me ha entrado la creatividad con mi código HTML.

No olvide que también podemos conectarnos a través de una sesión RDP desde el Portal de Azure clásico mediante el puerto 3389.

Espero que esto les haya resultado útil. Empiece hoy su viaje de su infraestructura como código con Azure.


<!--Image references-->
[2]: ./media/virtual-machines-automation-with-chef/2.png
[3]: ./media/virtual-machines-automation-with-chef/3.png
[4]: ./media/virtual-machines-automation-with-chef/4.png
[5]: ./media/virtual-machines-automation-with-chef/5.png
[6]: ./media/virtual-machines-automation-with-chef/6.png
[7]: ./media/virtual-machines-automation-with-chef/7.png
[8]: ./media/virtual-machines-automation-with-chef/8.png
[9]: ./media/virtual-machines-automation-with-chef/9.png
[10]: ./media/virtual-machines-automation-with-chef/10.png
[11]: ./media/virtual-machines-automation-with-chef/11.png
[13]: ./media/virtual-machines-automation-with-chef/13.png


<!--Link references-->

<!---HONumber=AcomDC_1203_2015-->