<properties
	pageTitle="Actualización del Agente de Linux de Azure desde Github | Microsoft Azure"
	description="Obtenga información acerca de cómo actualizar el agente Linux de Azure para la máquina virtual de Linux en Azure a la versión más reciente de Github"
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="mingzhan"/>


# Actualización del Agente de Linux de Azure en una máquina virtual a la última versión desde Github

Para actualizar el [Agente de Linux de Azure](https://github.com/Azure/WALinuxAgent) en una máquina virtual Linux, debe:

1. Tener en Azure una VM que ejecuta Linux.
2. Estar conectado a esa VM de Linux mediante SSH

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


> [AZURE.NOTE] Si va a realizar esta tarea desde un equipo con Windows, utilice PuTTY para SSH en el equipo Linux. Para obtener más información, consulte [Inicio de sesión en una máquina virtual con Linux](virtual-machines-linux-how-to-log-on.md).

Las distribuciones de Linux aprobadas en Azure han colocado el paquete del agente Linux de Azure en sus repositorios, así que, si es posible, compruebe e instale primero la versión más reciente de ese repositorio de distribuciones.

Para Ubuntu, basta con escribir:

    #sudo apt-get install walinuxagent

En CentOS, escriba:

    #sudo yum install waagent

Para Oracle Linux, asegúrese de que el `Addons` repositorio está habilitado. Elija editar el archivo `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) o `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux) y cambie la línea `enabled=0` a `enabled=1` en **[ol6\_addons]** o **[ol7\_addons]** en este archivo.

Luego, instale la versión más reciente del Agente de Linux de Azure de tipo:

    #sudo yum install WALinuxAgent

Normalmente, esto es todo lo que necesita, pero si por alguna razón necesita instalarlo desde https://github.com directamente, siga estos pasos.


## Instalar wget

Inicie sesión en la máquina virtual con SSH.

Instale wget (hay algunas distribuciones que no lo instalan de forma predeterminada, como las versiones Redhat, CentOS y Oracle Linux 6.4 y 6.5) y escriba `#sudo yum install wget` en la línea de comandos.


## Descargar la versión más reciente

Abra [la versión del Agente de Linux de Azure en Github](https://github.com/Azure/WALinuxAgent/releases) en una página web y compruebe el número de versión más reciente. (Puede buscar la versión actual escribiendo `#waagent --version`).

### Para la versión 2.0.x, escriba:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   La línea siguiente usa la versión 2.0.14 como ejemplo:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

### Para la versión 2.1.x o posteriores, escriba:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   La línea siguiente usa la versión 2.1.0 como ejemplo:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

## Instalación del agente de Linux de Azure

### Para la versión 2.0.x, use:

 Convertir waagent en ejecutable:

    #chmod +x waagent

 Copia del nuevo archivo ejecutable en /usr/sbin/.

  Para la mayoría de las distribuciones de Linux, use:

    #sudo cp waagent /usr/sbin

  Para CoreOS, use:

    #sudo cp waagent /usr/share/oem/bin/

  Si se trata de una nueva instalación del Agente de Linux de Azure, ejecute:

    #sudo /usr/sbin/waagent -install -verbose

### Para la versión 2.1.x, use:

Es posible que necesite instalar primero el paquete `setuptools`. Consulte [aquí](https://pypi.python.org/pypi/setuptools). A continuación, ejecute:

    #sudo python setup.py install

## Reinicio del servicio waagent

Para la mayoría de las distribuciones de Linux:

    #sudo service waagent restart

Para Ubuntu, use:

    #sudo service walinuxagent restart

Para CoreOS, use:

    #sudo systemctl restart waagent

## Confirmación de la versión del agente Linux de Azure

    #waagent -version

Para CoreOS, es posible que el comando anterior no funcione.

Verá que la versión del Agente de Linux de Azure se actualizó a la versión nueva.

Para más información sobre el Agente de Linux de Azure, consulte el [archivo Léame del Agente de Linux de Azure](https://github.com/Azure/WALinuxAgent).

<!---HONumber=AcomDC_0211_2016-->