<properties
	pageTitle="Instalación de MySQL en una máquina virtual Linux de OpenSUSE en Microsoft Azure"
	description="Aprenda a instalar MySQL en una máquina virtual en Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="cynthn"/>

# Instalación de MySQL en una máquina virtual que ejecuta OpenSUSE Linux en Azure

[MySQL][MySQL] es una conocida base de datos SQL de código abierto. En este tutorial se muestra cómo crear una máquina virtual que disponga de OpenSUSE Linux y, a continuación, instale MySQL.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


<br>


## Creación de una máquina virtual que ejecuta OpenSUSE Linux

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Instalación y ejecución de MySQL en la máquina virtual

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Pasos siguientes
Para obtener más información sobre MySQL, vea la [Documentación de MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com

<!---HONumber=AcomDC_0128_2016-->