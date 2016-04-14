<properties
	pageTitle="Descarga del SDK de Azure para PHP"
	description="Obtenga información acerca de cómo descargar e instalar el SDK de Azure para PHP."
	documentationCenter="php"
	services="app-service\web"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="12/16/2015"
	ms.author="tomfitz"/>

#Descarga del SDK de Azure para PHP

## Información general

El SDK de Azure para PHP incluye componentes que le permiten desarrollar, implementar y administrar aplicaciones PHP para Azure. Específicamente, el SDK de Azure para PHP incluye lo siguiente:

* **Las bibliotecas de clientes PHP para Azure**. Estas bibliotecas de clases proporcionan una interfaz para tener acceso a características de Azure, como los servicios de administración de datos y los servicios en la nube.  
* **La interfaz de la línea de comandos de Azure para Mac, Linux y Windows (CLIC de Azure).** Este es un conjunto de herramientas que sirve para implementar y administrar servicios de Azure, como Sitios web Azure y Red virtual de Azure. La interfaz de la línea de comandos de Azure funciona en cualquier plataforma, incluidas las plataformas Mac, Linux y Windows.
* **Azure PowerShell (solo Windows)**. Este es un conjunto de cmdlets de PowerShell para implementar y administrar servicios de Azure, como Servicios en la nube y Máquinas virtuales.
* **Los emuladores de Azure (solo Windows)**. Los emuladores de proceso y almacenamiento son emuladores locales de los servicios en la nube y los servicios de administración de datos que le permiten probar localmente una aplicación. Los emuladores de Azure solo se ejecutan en Windows.

Las secciones que vienen a continuación describen cómo descargar e instalar los componentes descritos.

En las instrucciones de este tema se asume que tiene instalado [PHP][install-php].

> [AZURE.NOTE]Debe tener instalado PHP 5.3 o superior para utilizar las bibliotecas de clientes PHP para Azure.

##Bibliotecas de clientes PHP para Azure

Las bibliotecas de clientes PHP para Azure proporcionan una interfaz para tener acceso a características de Azure, como los servicios de administración de datos y los servicios en la nube, desde cualquier sistema operativo. Estas bibliotecas se pueden instalar manualmente o a través de administradores de paquetes PEAR o el compositor.

Si desea obtener información acerca del uso de las bibliotecas de clientes PHP para Azure, consulte [Uso del servicio BLOB][blob-service], [Uso del servicio Tabla][table-service] y [Uso del servicio Cola][queue-service].

###Instalación mediante el compositor

1. [Instalación de Git][install-git].


	> [AZURE.NOTE]En Windows, también tendrá que agregar el archivo ejecutable Git a la variable de entorno PATH.

2. Cree un archivo con el nombre **composer.json** en la raíz del proyecto y agréguele el código siguiente:

        {
            "repositories": [
                {
                    "type": "pear",
                    "url": "http://pear.php.net"
                }
            ],
            "require": {
                "pear-pear.php.net/mail_mime" : "*",
                "pear-pear.php.net/http_request2" : "*",
                "pear-pear.php.net/mail_mimedecode" : "*",
                "microsoft/windowsazure": "*"
            }
        }

3. Descargue **[composer.phar][composer-phar]** en la raíz del proyecto.

4. Abra un símbolo del sistema y ejecute esto en la raíz del proyecto

		php composer.phar install

###Instalación como paquete PEAR

Para instalar las bibliotecas de clientes PHP para Azure como paquete PEAR, siga estos pasos:

1. [Instale PEAR][install-pear].
2. Configure el canal PEAR para Azure:

		pear channel-discover pear.windowsazure.com
3. Instale el paquete PEAR:

		pear install pear.windowsazure.com/WindowsAzure-0.4.1

Una vez que se haya completado la instalación, puede hacer referencia a las bibliotecas de clases desde su aplicación.

###Instalación manual

Para descargar e instalar las bibliotecas de clientes PHP para Azure manualmente, siga estos pasos:

1. Descargue un archivo .zip que contenga las bibliotecas desde [GitHub][php-sdk-github]. También puede bifurcar el repositorio y clonarlo en su máquina local. La última opción requiere una cuenta GitHub y tener Git instalado localmente.

	> [AZURE.NOTE]Las bibliotecas de clientes PHP para Azure tienen una dependencia en los paquetes [HTTP\_Request2](http://pear.php.net/package/HTTP_Request2), [Mail\_mime](http://pear.php.net/package/Mail_mime) y [Mail\_mimeDecode](http://pear.php.net/package/Mail_mimeDecode). La forma recomendada de resolver estas dependencias es instalar esos paquetes con el [administrador de paquetes PEAR](http://pear.php.net/manual/en/installation.php).

2. Copie el directorio `WindowsAzure` del archivo descargado en la estructura de directorios de la aplicación y haga referencia a clases desde su aplicación.

##Azure PowerShell y emuladores de Azure

Azure PowerShell es un conjunto de cmdlets de PowerShell para implementar y administrar servicios de Azure (como Servicios en la nube y Máquinas virtuales). Los emuladores de Azure son emuladores de servicios en la nube y servicios de administración de datos que le permiten probar localmente una aplicación. Estos componentes solo son compatibles con Windows.

La manera recomendada de instalar Azure PowerShell y los emuladores de Azure es utilizar el [instalador de plataforma web de Microsoft][download-wpi]. Observe que también puede elegir instalar otros componentes de desarrollo, como PHP, SQL Server, los controladores de Microsoft para SQL Server para PHP y WebMatrix.

Para obtener más información acerca del uso de Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell][powershell-tools].

##Azure CLI

La interfaz de la línea de comandos de Azure es un conjunto de herramientas que sirve para implementar y administrar servicios de Azure, como Sitios web Azure y Red virtual de Azure. Para obtener información acerca de cómo instalar la CLI de Azure, consulte [Instalar la CLI de Azure](xplat-cli-install.md).

## Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de PHP](/develop/php/).


[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[pear-net]: http://pear.php.net/
[http-request2-package]: http://pear.php.net/package/HTTP_Request2
[mail-mimedecode-package]: http://pear.php.net/package/Mail_mimeDecode
[mail-mime-package]: http://pear.php.net/package/Mail_mime
[install-pear]: http://pear.php.net/manual/en/installation.getting.php
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git

<!---HONumber=AcomDC_1223_2015-->