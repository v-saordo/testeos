<properties 
	pageTitle="Uso de SSH en Windows para conectarse a máquinas virtuales de Linux | Microsoft Azure" 
description="Obtenga información acerca de cómo generar y utilizar claves SSH en un equipo de Windows para conectarse a una máquina virtual con Linux en Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/04/2016" 
	ms.author="rasquill"/>

#Uso de SSH con Windows en Azure

> [AZURE.SELECTOR]
- [Windows](../articles/virtual-machines/virtual-machines-windows-use-ssh-key.md)
- [Linux/Mac](../articles/virtual-machines/virtual-machines-linux-use-ssh-key.md)

En este tema se describe cómo crear y usar archivos de clave privada y pública de formato **ssh-rsa** y **.pem** en Windows que puede usar para conectarse a las máquinas virtuales de Linux en Azure con el comando **ssh**. Si ya tiene archivos **.pem** creados, puede utilizarlos para crear máquinas virtuales de Linux a las que puede conectarse con **ssh**. Hay otros comandos que usan el protocolo **SSH** y archivos clave para realizar el trabajo de forma segura, en particular **scp** o [Secure Copy](https://en.wikipedia.org/wiki/Secure_copy), que pueden copiar archivos de manera segura a y desde equipos que admiten conexiones **SSH**.


## ¿Qué programas SSH y de creación de claves necesita?

**SSH** &#8212; o [secure shell](https://en.wikipedia.org/wiki/Secure_Shell) &#8212; es un protocolo de conexión cifrada que permite inicios de sesión seguros sobre conexiones no seguras. Se trata del protocolo de conexión predeterminado para las máquinas virtuales de Linux hospedadas en Azure, a menos que configure las máquinas virtuales de Linux para habilitar algún otro mecanismo de conexión. Los usuarios de Windows también pueden conectarse a máquinas virtuales Linux en Azure y administrarlas mediante una implementación de cliente **ssh**, pero los equipos Windows normalmente no incorporan un cliente **ssh**, por lo que tendrá que elegir uno.

Entre los clientes comunes que puede instalar se incluyen los siguientes:

- [puTTY y puTTYgen]((http://www.chiark.greenend.org.uk/~sgtatham/putty/)
- [MobaXterm](http://mobaxterm.mobatek.net/)
- [Cygwin](https://cygwin.com/)
- [Git para Windows](https://git-for-windows.github.io/), que viene con el entorno y las herramientas

Si se siente especialmente experto, también puede probar el [nuevo puerto del conjunto de herramientas de **OpenSSH** para Windows](http://blogs.msdn.com/b/powershell/archive/2015/10/19/openssh-for-windows-update.aspx). Sin embargo, tenga en cuenta que se trata de código que está actualmente en desarrollo, y debe revisar el código base antes de usarlo para sistemas de producción.

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## ¿Qué archivos claves necesita crear?

Una configuración básica SSH para Azure incluye un par de claves pública y privada **ssh-rsa** de 2048 bits (de forma predeterminada, **ssh keygen** almacena estos archivos como **~/.ssh/id\_rsa** y **~/.ssh/id-rsa.pub** a menos que cambie los valores predeterminados), así como un archivo `.pem` generado a partir del archivo de clave privada **id\_rsa** para su uso con el modelo de implementación clásica del portal clásico.

Estos son los escenarios de implementación y los tipos de archivos que se usan en cada uno:

1. Las claves **ssh rsa** son necesarias para cualquier implementación mediante el [Portal de Azure](https://portal.azure.com), independientemente del modelo de implementación.
2. Los archivos .pem son necesarios para crear máquinas virtuales mediante el [portal clásico](https://manage.windowsazure.com). Los archivos .pem también se admiten en las implementaciones clásicas que usan la [CLI de Azure](../xplat-cli-install.md).

> [AZURE.NOTE] Si planea administrar el servicio implementado con el modelo de implementación clásica, también puede crear un archivo de formato **.cer** para su carga en el portal, aunque esto no implica **ssh** o conectarse a máquinas virtuales de Linux, que es el tema de este artículo. Para crear esos archivos en Linux o Mac, escriba

## Obtención de ssh-keygen y openssl en Windows ##

[Esta sección](#What-SSH-and-key-creation-programs-do-you-need) anterior enumera algunas utilidades que incluyen `ssh-keygen` y `openssl` para Windows. A continuación, se enumeran algunos ejemplos:

### Uso de msysgit ###

1.	Descargue e instale msysgit desde la siguiente ubicación: [http://msysgit.github.com/](http://msysgit.github.com/)
2.	Ejecute `msys` desde el directorio instalado (ejemplo: c:\\msysgit\\msys.exe)
3.	Cambie al directorio `bin`. Para ello escriba `cd bin`


### Uso de GitHub para Windows ###

1.	Descargue e instale GitHub para Windows desde la siguiente ubicación: [http://windows.github.com/](http://windows.github.com/)
2.	Ejecute Git Shell desde la ruta: menú Inicio > Todos los programas > GitHub, Inc.

> [AZURE.NOTE] Puede encontrar el siguiente error al ejecutar los comandos `openssl` anteriores:

        Unable to load config info from /usr/local/ssl/openssl.cnf

La manera más fácil de resolver este problema consiste en establecer la variable de entorno `OPENSSL_CONF`. El proceso para establecer esta variable variará según el shell que se ha configurado en Github:

**Powershell:**

        $Env:OPENSSL_CONF="$Env:GITHUB_GIT\ssl\openssl.cnf"

**CMD:**

        set OPENSSL_CONF=%GITHUB_GIT%\ssl\openssl.cnf

**GIT Bash:**

        export OPENSSL_CONF=$GITHUB_GIT/ssl/openssl.cnf
	

###Uso de cygwin###

1.	Descargue e instale cygwin desde la siguiente ubicación: [http://cygwin.com/](http://cygwin.com/)
2.	Asegúrese de que el paquete OpenSSL y todas sus dependencias estén instalados.
3.	Ejecute `cygwin`

## Creación de una clave privada##

1.	Siga uno de los conjuntos de instrucciones anteriores para poder ejecutar `openssl.exe`.
2.	Escriba el siguiente comando:

		# openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	La pantalla debería mostrar lo siguiente:

	![linuxwelcomegit](./media/virtual-machines-linux-use-ssh-key/linuxwelcomegit.png)

4.	Responda las preguntas.
5.	Se habrán creado dos archivos: `myPrivateKey.key` y `myCert.pem`.
6.	Si va a utilizar la API directamente y no utiliza el Portal de administración, convierta `myCert.pem` en `myCert.cer` (certificado X509 con codificación DER) mediante el siguiente comando:

		# openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Creación de un PPK para Putty ##

1. Descargue e instale Puttygen desde la siguiente ubicación: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Es posible que Puttygen no pueda leer la clave privada que se creó anteriormente (`myPrivateKey.key`). Ejecute el comando siguiente para convertirla en una clave privada RSA que Puttygen pueda entender:

		# openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
		# chmod 600 ./myPrivateKey_rsa

	El comando anterior debería producir una nueva clave privada denominada myPrivateKey\_rsa.

3. Ejecute `puttygen.exe`

4. Haga clic en el menú: File > Load a Private Key (Archivo > Cargar clave privada).

5. Busque su clave privada, a la que hemos denominado anteriormente `myPrivateKey_rsa`. Deberá cambiar el filtro de archivos para mostrar **Todos los archivos (*.*)**

6. Haga clic en **Abrir**. Recibirá una secuencia parecida a la siguiente:

	![linuxgoodforeignkey](./media/virtual-machines-linux-use-ssh-key/linuxgoodforeignkey.png)

7. Haga clic en **Aceptar**.

8. Haga clic en **Guardar clave privada**, que aparece resaltado en la siguiente captura de pantalla:

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtygenprivatekey.png)

9. Guarde el archivo como PPK.


## Uso de Putty para conectarse a una máquina virtual con Linux ##

1.	Descargue e instale Putty desde la siguiente ubicación: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	Ejecute putty.exe.
3.	Rellene el nombre del host con la IP desde el Portal de administración:

	![linuxputtyconfig](./media/virtual-machines-linux-use-ssh-key/linuxputtyconfig.png)

4.	Antes de seleccionar **Abrir**, haga clic en la pestaña Connection > SSH > Auth (Conexión > SSH > Autenticación) para elegir la clave. Consulte la siguiente captura de pantalla con el campo que se va a rellenar:

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtyprivatekey.png)

5.	Haga clic en **Abrir** para conectarse a su máquina virtual.
 

<!---HONumber=AcomDC_0204_2016-->