<properties
	pageTitle="Instalación de la pila LAMP en una máquina virtual con Linux | Microsoft Azure"
	description="Aprenda a instalar la pila LAMP en una máquina virtual de Linux en Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor=""
	tags=“azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/29/2015"
	ms.author="szark"/>



#Instalación de la pila LAMP en una máquina virtual con Linux en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


Una pila LAMP consta de los siguientes elementos diferentes:

- **L**inux - Sistema operativo
- **A**pache - Servidor web
- **M**ySQL - Servidor de base de datos
- **P**HP - Lenguaje de programación


##Instalación en Ubuntu

Necesitará los siguientes paquetes instalados:

- `apache2`
- `mysql-server`
- `php5`
- `php5-mysql`

Después de ejecutar `apt-get update` para actualizar la lista local de paquetes, puede instalar estos paquetes con un solo comando `apt-get install`:

	# sudo apt-get update
	# sudo apt-get install apache2 mysql-server php5 php5-mysql

Tras ejecutar el comando anterior, se le pedirá que instale estos paquetes y una serie de dependencias. Pulse 'y' seguido por 'Enter' para continuar, y siga cualquier otra instrucción que aparezca para establecer un contraseña administrativa para MySQL.

Esto instalará las extensiones PHP mínimas necesarias para utilizar PHP con MySQL. Ejecute el siguiente comando para ver otras extensiones PHP disponibles como paquetes:

	# apt-cache search php5


##Instalación en CentOS y Oracle Linux

Necesitará los siguientes paquetes instalados:

- `httpd`
- `mysql`
- `mysql-server`
- `php`
- `php-mysql`

Puede instalar estos paquetes con un solo comando `yum install`:

	# sudo yum install httpd mysql mysql-server php php-mysql

Tras ejecutar el comando anterior, se le pedirá que instale estos paquetes y una serie de dependencias. Pulse 'y' seguido por 'Enter' para continuar.

Esto instalará las extensiones PHP mínimas necesarias para utilizar PHP con MySQL. Ejecute el siguiente comando para ver otras extensiones PHP disponibles como paquetes:

	# yum search php


## Instalación en el servidor SUSE Linux Enterprise

Necesitará los siguientes paquetes instalados:

- apache2
- mysql
- apache2-mod\_php53
- php53-mysql

Puede instalar estos paquetes con un solo comando `zypper install`:

	# sudo zypper install apache2 mysql apache2-mod_php53 php53-mysql

Tras ejecutar el comando anterior, se le pedirá que instale estos paquetes y una serie de dependencias. Pulse 'y' seguido por 'Enter' para continuar.

Esto instalará las extensiones PHP mínimas necesarias para utilizar PHP con MySQL. Ejecute el siguiente comando para ver otras extensiones PHP disponibles como paquetes:

	# zypper search php


Instalación
----------

1. Instalación de **Apache**

	- Ejecute el siguiente comando para asegurarse de que arranca el servidor web Apache:

		- Ubuntu y SLES: `sudo service apache2 restart`

		- CentOS y Oracle: `sudo service httpd restart`

	- Apache escucha en el puerto 80 de manera predeterminada. Tal vez necesite abrir un extremo para acceder a su servidor Apache de manera remota. Para obtener información más detallada, consulte la documentación sobre la [configuración de extremos](virtual-machines-set-up-endpoints.md).

	- Ahora ya puede comprobar si Apache funciona y proporciona contenido. En su explorador, vaya a `http://[MYSERVICE].cloudapp.net`, donde **[MYSERVICE]** es el nombre del servicio en la nube en el que reside su máquina virtual. Es posible que en algunas distribuciones reciba la bienvenida con una página web predeterminada que sencillamente diga "Funciona". En otras puede tal vez vea una página web más completa con enlaces a documentación y contenido adicional para configurar el servidor Apache.

2. Instalación de **MySQL**

	- Tenga en cuenta que este paso no es necesario en Ubuntu, que le pedirá una contraseña `root` de MySQL cuando el paquete mysql-server se instaló.

	- En otras distribuciones establezca la contraseña raíz para MySQL mediante la ejecución del siguiente comando:

			# mysqladmin -u root -p password yourpassword

	- A continuación puede administrar MySQL con las utilidades `mysql` o `mysqladmin`.


##Lecturas adicionales

Suponga que desea automatizar estos pasos para implementar aplicaciones en máquinas virtuales remotas Linux. Para ello, puede usar la extensión de Linux CustomScript. Consulte [Implementación de una aplicación LAMP con la extensión CustomScript para Linux](virtual-machines-linux-script-lamp.md).

Existen muchos recursos para instalar una pila LAMP en Ubuntu.

- [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)
 

<!---HONumber=Oct15_HO3-->