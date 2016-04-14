<properties 
	pageTitle="Selección de nombres de usuario para Linux | Microsoft Azure" 
	description="Aprenda a seleccionar nombres de usuario para una máquina virtual de Linux en Azure." 
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



#Selección de nombres de usuario para Linux en Azure#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Cuando aprovisiona una máquina virtual con Linux en Azure, debe especificar el nombre de un usuario no raíz que más adelante pueda usar para iniciar sesión en la máquina virtual. Puede elegir el nombre del nuevo usuario o, si el aprovisionamiento es a través del Portal de Azure clásico, puede aceptar el nombre predeterminado "azureuser".

En la mayoría de los casos, este usuario no existirá en la imagen base y se crea durante el proceso de aprovisionamiento. Si el usuario ya existe en la imagen de la máquina virtual base, el agente Azure Linux sencillamente configura la contraseña (o la tecla SSH) para ese usuario de acuerdo con la información especificada al crear la máquina virtual.

**Sin embargo**, Linux define un conjunto de nombres de usuario que no se deben usar al crear usuarios nuevos. El proceso de aprovisionamiento generará un **error** si trata de aprovisionar una máquina virtual Linux con un usuario del sistema existente, que se define como un usuario con UID 0-99. Un ejemplo típico es el usuario `root`, que tiene el UID de 0.

 - Consulte también: [Base estándar de Linux - Intervalos de identificadores de usuarios](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

A continuación, se indican los nombres de usuario que debería evitar al realizar el aprovisionamiento de una máquina virtual de Linux. Le recomendamos que **no use estos nombres de usuario** porque el proceso de aprovisionamiento de máquinas virtuales podría generar un error.


## openSUSE
- abrt
- adm
- audio
- bin
- bin
- cdrom
- cgred
- daemon
- dbus
- dialout
- dip
- disk
- floppy
- ftp
- games
- gopher
- haldaemon
- halt
- kmem
- lock
- lp
- mail
- man
- mem
- nfsnobody
- nobody
- ntp
- operator
- oprofile
- postdrop
- postfix
- qpidd
- root
- rpc
- rpcuser
- saslauth
- shutdown
- slocate
- sshd
- stapdev
- stapusr
- sync
- sys
- tape
- test
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel


## SLES
- audio
- bin
- cdrom
- console
- daemon
- dialout
- disk
- floppy
- ftp
- ftp
- games
- haldaemon
- kmem
- lp
- lp
- mail
- maildrop
- man
- messagebus
- modem
- news
- news
- nobody
- nogroup
- polkituser
- postfix
- public
- root
- shadow
- sshd
- sys
- test
- trusted
- tty
- users
- utmp
- uucp
- uuidd
- video
- wheel
- www
- wwwrun
- xok

 
## CentOS
- abrt
- adm
- audio
- bin
- cdrom
- cgred
- daemon
- dbus
- dialout
- dip
- disk
- floppy
- ftp
- ftp
- games
- gopher
- haldaemon
- halt
- kmem
- lock
- lp
- mail
- man
- mem
- nfsnobody
- nobody
- ntp
- operator
- oprofile
- postdrop
- postfix
- qpidd
- root
- rpc
- rpcuser
- saslauth
- shutdown
- slocate
- sshd
- stapdev
- stapusr
- sync
- sys
- tape
- test
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel


## Ubuntu
- adm
- admin
- audio
- backup
- bin
- cdrom
- crontab
- daemon
- dialout
- dip
- disk
- fax
- floppy
- fuse
- games
- gnats
- irc
- kmem
- landscape
- libuuid
- list
- lp
- mail
- man
- messagebus
- mlocate
- netdev
- news
- nobody
- nogroup
- operator
- plugdev
- proxy
- root
- sasl
- shadow
- src
- ssh
- sshd
- staff
- sudo
- sync
- sys
- syslog
- tape
- tty
- users
- utmp
- uucp
- video
- voice
- whoopsie
- www-data

 

<!---HONumber=AcomDC_1223_2015-->