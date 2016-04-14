<properties
   pageTitle="Crear y cargar una imagen de máquina virtual de FreeBSD | Microsoft Azure"
   description="Aprenda a crear y cargar un disco duro virtual (VHD) que contenga el sistema operativo FreeBSD para crear una máquina virtual de Azure."
   services="virtual-machines"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="01/12/2016"
   ms.author="kyliel"/>

# Creación y carga de un VHD de FreeBSD en Azure

En este artículo se muestra cómo puede crear y cargar un disco duro virtual (VHD) que contenga el sistema operativo FreeBSD, de forma que pueda usarlo como su propia imagen para crear una máquina virtual en Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


##Requisitos previos##
En este artículo se supone que tiene los siguientes elementos:

- **Una suscripción de Azure:** si no tiene ninguna, puede crear una cuenta en un par de minutos. Si tiene una suscripción a MSDN, consulte [Beneficio de Azure para los suscriptores de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). De lo contrario, consulte [crear una cuenta de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).  

- **Herramientas de Azure PowerShell**: dispone del módulo Microsoft Azure PowerShell instalado y configurado para usar su suscripción. Para descargar el módulo, consulte [Descargas de Azure](https://azure.microsoft.com/downloads/). Hay disponible un tutorial para instalar y configurar el módulo aquí. Usará el cmdlet de [descargas de Azure](https://azure.microsoft.com/downloads/) para cargar el VHD.

- **Sistema operativo FreeBSD instalado en un archivo .vhd**: ha instalado un sistema operativo FreeBSD compatible en un disco duro virtual. Existen varias herramientas para crear archivos .vhd; por ejemplo, puede utilizar una solución de virtualización como Hyper-V para crear el archivo .vhd e instalar el sistema operativo. Para obtener instrucciones, consulte [Instalación del rol de Hyper-V y configuración de una máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] el reciente formato VHDX no se admite en Azure. Puede convertir el disco al formato VHD mediante el Administrador de Hyper-V o el cmdlet [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx).

Esta tarea incluye los cinco pasos siguientes.

## Paso 1: Preparación de la imagen que se va a cargar ##

Para la instalación de FreeBSD en Hyper-v, hay disponible un tutorial [aquí](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).

Desde la máquina virtual en la que se instaló el sistema operativo FreeBSD, realice los procedimientos siguientes:

1. **Habilitar DHCP**

		# echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
		# service netif restart

2. **Habilitar SSH**

    SSH se activa de forma predeterminada después de la instalación desde el disco. Si no es así, o si utiliza VHD de FreeBSD directamente, escriba:

		# echo 'sshd_enable="YES"' >> /etc/rc.conf
		# ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
		# ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
		# service sshd restart

3. **Configurar la consola de serie**

		# echo 'console="comconsole vidconsole"' >> /boot/loader.conf
		# echo 'comconsole_speed="115200"' >> /boot/loader.conf

4. **Instalar sudo**

    La cuenta raíz está deshabilitada en Azure y, por tanto, deberá usar sudo con un usuario sin privilegios para ejecutar comandos con privilegios elevados.

		# pkg install sudo

5. Requisitos previos del agente de Azure

    5\.1 **Instalar python**

		# pkg install python27
		# ln -s /usr/local/bin/python2.7 /usr/bin/python

    5\.2 **Instalar wget**

		# pkg install wget

6. **Instalar el agente de Azure**

    Siempre puede encontrar la versión más reciente del agente de Azure en [github](https://github.com/Azure/WALinuxAgent/releases). La versión 2.0.10 y las posteriores admiten oficialmente FreeBSD 10 y las versiones posteriores. La última versión del agente de Azure para FreeBSD es 2.0.16.

		# wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.10/waagent --no-check-certificate
		# mv waagent /usr/sbin
		# chmod 755 /usr/sbin/waagent
		# /usr/sbin/waagent -install

    **Importante**: después de la instalación, compruebe que se está ejecutando.

		# service –e | grep waagent
		/etc/rc.d/waagent
		# cat /var/log/waagent.log

    Ahora podría **apagar** la máquina virtual. También podría ejecutar el paso 7 antes de apagarla, pero es opcional.

7. El desaprovisionamiento es opcional. Se trata de una limpieza del sistema y una preparación para el reaprovisionamiento.

    El comando siguiente también elimina la última cuenta de usuario aprovisionada y los datos asociados.

		# waagent –deprovision+user

## Paso 2: Creación de una cuenta de almacenamiento en Azure ##

Necesita una cuenta de almacenamiento de Azure para cargar un archivo .vhd, por lo que se puede usar en Azure para crear una máquina virtual. Puede utilizar el portal clásico de Azure para crear una cuenta de almacenamiento.

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).

2. En la barra de comandos, haga clic en **Nuevo**.

3. Haga clic en **Servicios de datos** > **Almacenamiento** > **Creación rápida**.

	![Crear rápidamente una cuenta de almacenamiento](./media/virtual-machines-freebsd-create-upload-vhd/Storage-quick-create.png)

4. Rellene los campos de la manera siguiente:

	- En **URL**, escriba un nombre de subdominio que vaya a usar en la URL para la cuenta de almacenamiento. Esta entrada puede contener de 3 a 24 letras minúsculas y números. Este nombre se convierte en el nombre del host dentro de la URL que se ha usado para direccionar los recursos Blob, Cola o Tabla de la suscripción.

	- Elija **la ubicación o el grupo de afinidad** para la cuenta de almacenamiento. Un grupo de afinidad le permite colocar sus servicios y almacenamiento en la nube en el mismo centro de datos.

	- Decida si va a utilizar la **replicación geográfica** para la cuenta de almacenamiento. La replicación geográfica está activada de forma predeterminada. Esta opción replica los datos en una ubicación secundaria, sin coste, por lo que su almacenamiento conmuta por error a esa ubicación si se produce un error importante en la ubicación principal. La ubicación secundaria se asigna automáticamente y no se puede cambiar. Si necesita más control sobre la ubicación del almacenamiento en la nube debido a requisitos legales o las directivas de su organización, puede desactivar la replicación geográfica. Sin embargo, tenga en cuenta que si más tarde activa la replicación geográfica, deberá pagar una cuota de transferencia de datos puntual para replicar los datos existentes en la ubicación secundaria. Los servicios de almacenamiento sin replicación geográfica se ofrecen con descuento. Puede encontrar más detalles sobre la replicación geográfica de las cuentas de almacenamiento en: [Creación, administración o eliminación de una cuenta de almacenamiento](../storage-create-storage-account/#replication-options).

	![Escribir los detalles de la cuenta de almacenamiento](./media/virtual-machines-freebsd-create-upload-vhd/Storage-create-account.png)


5. Haga clic en **Crear cuenta de almacenamiento**. La cuenta aparece ahora en **Almacenamiento**.

	![Cuenta de almacenamiento creada correctamente](./media/virtual-machines-freebsd-create-upload-vhd/Storagenewaccount.png)

6. Después cree un contenedor para los VHD que ha cargado. Haga clic en el nombre de la cuenta de almacenamiento y, a continuación, en **Contenedores**.

	![Detalles de la cuenta de almacenamiento](./media/virtual-machines-freebsd-create-upload-vhd/storageaccount_detail.png)

7. Haga clic en **Crear un contenedor**.

	![Detalles de la cuenta de almacenamiento](./media/virtual-machines-freebsd-create-upload-vhd/storageaccount_container.png)

8. Escriba un **Nombre** para el contenedor y seleccione la **Directiva de acceso**.

	![Nombre del contenedor](./media/virtual-machines-freebsd-create-upload-vhd/storageaccount_containervalues.png)

    > [AZURE.NOTE] De manera predeterminada, el contenedor es privado y solo puede acceder a él el propietario de la cuenta. Para permitir el acceso de lectura público a los blobs del contenedor, pero no a las propiedades y metadatos del contenedor, utilice la opción de "Public Blob". Para permitir el acceso de lectura público completo del contenedor y de los blobs, utilice la opción de "Public Container".

## Paso 3: Preparación de la conexión con Microsoft Azure ##

Antes de cargar el archivo .vhd, debe establecer una conexión segura entre el equipo y la suscripción de Azure. Para ello, puede utilizar el método de Microsoft Azure Active Directory o el método del certificado.

###Uso del método de Microsoft Azure AD

1. Abra la consola de PowerShell de Azure.

2. Escriba el siguiente comando: `Add-AzureAccount`

	Este comando abre una ventana de inicio de sesión que le permitirá iniciar sesión con su cuenta profesional o educativa.

	![Ventana de PowerShell](./media/virtual-machines-freebsd-create-upload-vhd/add_azureaccount.png)

3. Azure autentica y guarda las credenciales y, a continuación, cierra la ventana.

###Uso del método del certificado

1. Abra la consola de Azure PowerShell.

2. Escriba: `Get-AzurePublishSettingsFile`.

3. Se abre una ventana del explorador que le solicita que descargue un archivo .publishsettings. Contiene información y un certificado para su suscripción de Microsoft Azure.

	![Página de descarga del explorador](./media/virtual-machines-freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)

3. Guarde el archivo .publishsettings.

4. Escriba: `Import-AzurePublishSettingsFile <PathToFile>`

	Donde `<PathToFile>` es la ruta completa al archivo .publishsettings.

   Para obtener información, consulte [Introducción a los cmdlets de Microsoft Azure](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx).

   Para obtener más información acerca de la instalación y configuración de Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](../install-configure-powershell.md).

## Paso 4: Carga del archivo .vhd ##

Cuando carga el archivo .vhd, puede colocarlo en cualquier parte del almacenamiento de blobs. En los siguientes ejemplos de comandos, **BlobStorageURL** es la URL de la cuenta de almacenamiento que ha creado en el paso 2 y **YourImagesFolder** es el contenedor dentro del almacenamiento de blobs donde desea almacenar las imágenes. **VHDName** es la etiqueta que aparece en el Portal de Azure clásico para identificar el disco duro virtual. **PathToVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd.


1. Desde la ventana de Azure PowerShell que ha usado en el paso anterior, escriba:

		Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## Paso 5: Creación de una máquina virtual con VHD cargado ##
Después de cargar el archivo .vhd, puede agregarlo como una imagen a la lista de imágenes personalizadas asociadas con su suscripción y crear una máquina virtual con esta imagen personalizada.

1. Desde la ventana de Azure PowerShell que ha usado en el paso anterior, escriba:

		Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

    **Importante**: use Linux como tipo de sistema operativo por ahora, ya que la versión actual de Azure PowerShell solo acepta "Linux" o "Windows" como parámetro.

2. Tras completar los pasos anteriores, la nueva imagen aparecerá en la lista cuando elija la pestaña **Imágenes** en el Portal de Azure clásico.

    ![agregar imagen](./media/virtual-machines-freebsd-create-upload-vhd/addfreebsdimage.png)

3. Cree una máquina virtual desde la galería. Esta nueva imagen ahora está disponible en **Mis imágenes**. Seleccione la nueva imagen y siga las indicaciones para configurar un nombre de host, una contraseña o clave SSH, etc.

	![imagen personalizada](./media/virtual-machines-freebsd-create-upload-vhd/createfreebsdimageinazure.png)

4. Cuando haya completado el aprovisionamiento, verá que la máquina virtual de FreeBSD se ejecuta en Azure.

	![imagen de FreeBSD en azure](./media/virtual-machines-freebsd-create-upload-vhd/freebsdimageinazure.png)

<!---HONumber=AcomDC_0128_2016-->