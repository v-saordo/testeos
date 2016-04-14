<properties 
	pageTitle="Uso de privilegios raíz en máquinas virtuales de Linux | Microsoft Azure" 
	description="Aprenda a usar privilegios raíz en una máquina virtual de Linux en Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/17/2015" 
	ms.author="szark"/>


# Uso de privilegios raíz en máquinas virtuales con Linux en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

De forma predeterminada, el usuario `root` está deshabilitado en las máquinas virtuales Linux en Azure. Los usuarios pueden ejecutar comandos con privilegios elevados mediante el comando `sudo`. Sin embargo, la experiencia puede variar dependiendo de la manera en que se aprovisionó el sistema.

1. **Clave SSH y contraseña o solo contraseña**: La máquina virtual se aprovisionó con un certificado (archivo `.CER`) o una clave SSH, además de una contraseña, o solo un nombre de usuario y una contraseña. En este caso `sudo` solicitará la contraseña del usuario antes de ejecutar el comando.

2. **Solo clave SSH**: la máquina virtual se aprovisionó con un certificado (archivo `.cer`, `.pem` o `.pub`) o una clave SSH, pero sin contraseña. En este caso `sudo` **no** solicitará la contraseña del usuario antes de ejecutar el comando.


## Clave SSH y contraseña o solo contraseña

Inicie sesión en la máquina virtual Linux usando la autenticación de clave SSH o contraseña y, a continuación, ejecute los comandos usando `sudo`, por ejemplo:

	# sudo <command>
	[sudo] password for azureuser:

En este caso, se le solicitará al usuario una contraseña. Después de escribir la contraseña, `sudo` ejecutará el comando con los privilegios `root`.

También puede habilitar sudo sin contraseña, si edita el archivo `/etc/sudoers.d/waagent`, por ejemplo:

	#/etc/sudoers.d/waagent
	azureuser ALL = (ALL) NOPASSWD: ALL

Este cambio permitirá que el usuario "azureuser" use sudo sin contraseña.

## Solo clave SSH

Inicie sesión en la máquina virtual Linux usando la autenticación de clave SSH y, a continuación, ejecute los comandos usando `sudo`, por ejemplo:

	# sudo <command>

En este caso **no** se solicitará la contraseña al usuario. Después de presionar `<enter>`, `sudo` ejecutará el comando con privilegios `root`.

 

<!---HONumber=AcomDC_1223_2015-->