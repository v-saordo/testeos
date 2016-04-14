<properties
	pageTitle="Use la extensión CustomScript en una máquina virtual Linux | Microsoft Azure"
	description="Obtenga información acerca de cómo usar la extensión CustomScript para implementar aplicaciones en máquinas virtuales de Linux en Azure creadas mediante el modelo de implementación clásica."
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	services="virtual-machines"
	authors="gbowerman"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2015"
	ms.author="guybo"/>

#Implementación de una aplicación LAMP con la extensión CustomScript de Azure para Linux#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


La extensión CustomScript de Microsoft Azure para Linux ofrece una manera de personalizar sus máquinas virtuales mediante la ejecución de código arbitrario escrito en cualquier lenguaje de scripting compatible con la máquina virtual (por ejemplo, Python y Bash). Esto proporciona una forma muy flexible de automatizar la implementación de aplicaciones en varios equipos.

Puede implementar la extensión CustomScript mediante el Portal de Azure clásico, Windows PowerShell o la interfaz de la línea de comandos de Azure (CLI de Azure).

En este artículo usaremos la CLI de Azure para implementar una aplicación LAMP sencilla en una máquina virtual Ubuntu creada mediante el modelo de implementación clásica.

## Requisitos previos

Para este ejemplo, cree primero dos máquinas virtuales de Azure con Ubuntu 14.04 o una versión posterior. Las máquinas virtuales se denominan *script-vm* y *lamp-vm*. Use nombres únicos cuando cree las máquinas virtuales. Una se usa para ejecutar los comandos de CLI y la otra, para implementar la aplicación LAMP.

También necesita una cuenta de Almacenamiento de Azure y una clave para tener acceso a ella (se puede obtener desde el Portal de Azure clásico).

Si necesita ayuda para crear máquinas virtuales Linux en Azure, consulte [Creación de una máquina virtual que ejecuta Linux](virtual-machines-linux-tutorial.md).

Los comandos de instalación asumen Ubuntu, pero puede adaptar la instalación para cualquier distribución de Linux admitida.

La máquina virtual script-vm debe tener instalada la CLI de Azure, con una conexión activa a Azure. Para obtener ayuda con este procedimiento, consulte [Instalación y configuración de la interfaz de la línea de comandos de Azure](../xplat-cli-install.md).

## Cargar un script

Usaremos la extensión CustomScript para ejecutar un script en una máquina virtual remota para instalar la pila LAMP y crear una página PHP. Para tener acceso a la secuencia de comandos desde cualquier lugar, la cargaremos como un blob de Azure.

### Información general del script

El ejemplo de script instala una pila LAMP en Ubuntu (incluida la configuración de una instalación silenciosa de MySQL), escribe un simple archivo PHP e inicia Apache:

	#!/bin/bash
	# set up a silent install of MySQL
	dbpass="mySQLPassw0rd"

	export DEBIAN_FRONTEND=noninteractive
	echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
	echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

	# install the LAMP stack
	apt-get -y install apache2 mysql-server php5 php5-mysql  

	# write some PHP
	echo <center><h1>My Demo App</h1><br/></center> > /var/www/html/phpinfo.php
	echo <\?php phpinfo()\; \?> >> /var/www/html/phpinfo.php

	# restart Apache
	apachectl restart

### Cargar script

Guarde el script como un archivo de texto, por ejemplo, *lamp\_install.sh* y luego cárguelo en Almacenamiento de Azure. Puede hacerlo fácilmente con la CLI de Azure. En el ejemplo siguiente se carga el archivo en un contenedor de almacenamiento denominado "scripts". Si el contenedor no existe, deberá crearlo primero.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

También debe crear un archivo JSON que describa cómo descargar el script del almacenamiento de Azure. Guárdelo como *public\_config.json* (reemplazando "mystorage" por el nombre de la cuenta de almacenamiento):

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## Implementar la extensión

Ahora puede usar el siguiente comando para implementar la extensión CustomScript de Linux en la máquina virtual remota usando la CLI de Azure.

    azure vm extension set -c "./public_config.json" lamp-vm CustomScriptForLinux Microsoft.OSTCExtensions 1.*

El comando anterior descargar y ejecutar el script *lamp\_install.sh* en la máquina virtual denominada *lamp-vm*.

Dado que la aplicación incluye un servidor web, no olvide abrir un puerto de escucha HTTP en la máquina virtual remota con el siguiente comando.

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## Supervisión y solución de problemas

Para comprobar el progreso de la ejecución del script personalizado, puede examinar el archivo de registro en la máquina virtual remota. SSH en *lamp-vm* y el archivo de registro en la cola con el siguiente comando.

    cd /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.3.0.0/
    tail -f extension.log

Después de ejecutar la extensión CustomScript, puede examinar hasta la página PHP que ha creado para obtener información. Por ejemplo, en este artículo, la página PHP es **http://lamp-vm.cloudapp.net/phpinfo.php*.

## Recursos adicionales

Puede usar los mismos pasos básicos para implementar aplicaciones más complejas. En este ejemplo, la secuencia de comandos de instalación se guardó como un blob público en el almacenamiento de Azure. Una opción más segura sería almacenar el script de instalación como un blob seguro con una [firma de acceso seguro](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS).

A continuación, se ofrecen algunos recursos adicionales para la CLI de Azure, Linux y la extensión CustomScript.

[Automatización de las tareas de personalización de VM de Linux con la extensión CustomScript](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Extensiones de Linux de Azure (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Informática de código abierto y Linux en Azure](virtual-machines-linux-opensource.md)

<!---HONumber=AcomDC_0128_2016-->