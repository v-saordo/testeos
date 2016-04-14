<properties
   pageTitle="Implementar una matriz virtual de StorSimple: Aprovisionamiento en VMware"
   description="En este segundo tutorial de la serie de implementación de matrices virtuales de StorSimple, se trata el aprovisionamiento de un dispositivo virtual en VMware."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/01/2016"
   ms.author="alkohli"/>


# Implementar una matriz virtual de StorSimple: Aprovisionar una matriz virtual en VMware

![](./media/storsimple-ova-deploy2-provision-vmware/vmware4.png)

## Información general 
Este tutorial de aprovisionamiento se aplica a las matrices virtuales de StorSimple (también conocidas como dispositivos virtuales locales de StorSimple o dispositivos virtuales de StorSimple) que se ejecutan en la versión de disponibilidad general de marzo de 2016. En este tutorial se describe cómo aprovisionar y conectarse una matriz virtual de StorSimple en un sistema host que ejecuta VMware ESXi 5.5 y versiones posteriores.

Se necesitan privilegios de administrador para aprovisionar y conectarse a un dispositivo virtual. El aprovisionamiento y la instalación inicial pueden tardar unos 10 minutos en completarse.


## Requisitos previos de aprovisionamiento

Aquí encontrará los requisitos previos para aprovisionar un dispositivo virtual en un sistema host que ejecuta VMware ESXi 5.5 y versiones posteriores.

### Para el servicio de Administrador de StorSimple

Antes de comenzar, asegúrese de que:

-   Ha completado todos los pasos de [Preparar el portal para la matriz virtual de StorSimple](storsimple-ova-deploy1-portal-prep.md).

-   Ha descargado la imagen del dispositivo virtual para VMware desde el Portal de Azure. Para más información, consulte [Paso 3: Descargar la imagen del dispositivo virtual](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

### Para el dispositivo virtual StorSimple 

Antes de implementar un dispositivo virtual, asegúrese de que:

-   Tiene acceso a un sistema host que ejecuta Hyper-V (2008 R2 o superior) que pueda utilizarse para aprovisionar un dispositivo.

-   El sistema host es capaz de utilizar los recursos siguientes para aprovisionar el dispositivo virtual:

	-   Un mínimo de 4 núcleos.

	-   Al menos 8 GB de RAM.

	-   Una interfaz de red.

	-   Un disco virtual de 500 GB para datos del sistema.

### Para la red en el centro de datos 

Antes de comenzar, asegúrese de que:

-   Ha revisado los requisitos de red para implementar un dispositivo virtual StorSimple y ha configurado la red del centro de datos según los requisitos. Para obtener más información, consulte la Guía de requisitos del sistema de matrices virtuales de Microsoft Azure StorSimple.

## Aprovisionamiento paso a paso 

Para aprovisionar y conectarse a un dispositivo virtual, debe realizar los pasos siguientes:

1.  Asegúrese de que el sistema host tiene recursos suficientes para cumplir los requisitos mínimos del dispositivo virtual.

2.  Aprovisione un dispositivo virtual en el hipervisor.

3.  Inicie el dispositivo virtual y obtenga la dirección IP.

## Paso 1: Asegurarse de que el sistema host cumple los requisitos mínimos del dispositivo virtual

Para crear un dispositivo virtual, necesitará:

-   Acceso a un sistema host que ejecute VMware ESXi Server 5.5 y versiones posteriores.

-   Cliente VMware vSphere en el sistema para administrar el host ESXi.

	-   Un mínimo de 4 núcleos.

	-   Al menos 8 GB de RAM.

	-   Una interfaz de red conectada a la red capaz de enrutar el tráfico a Internet. El ancho de banda mínimo de Internet debe ser de 5 Mbps para permitir el funcionamiento óptimo del dispositivo.

	-   Un disco virtual de 500 GB para datos.

## Paso 2: Aprovisionar un dispositivo virtual en el hipervisor

Realice los pasos siguientes para aprovisionar un dispositivo virtual en el hipervisor.

1.  Copie la imagen del dispositivo virtual en el sistema. Esta es la imagen que ha descargado mediante el Portal de Azure clásico. Anote la ubicación donde haya copiado la imagen, ya que la va a utilizar más adelante en el procedimiento.

2.  Inicie sesión en el servidor de ESXi mediante el cliente de vSphere. Debe tener privilegios de administrador para crear una máquina virtual.

	![](./media/storsimple-ova-deploy2-provision-vmware/image1.png)

1.  En el cliente de vSphere, en la sección de inventario en el panel izquierdo, seleccione el servidor ESXi.

	![](./media/storsimple-ova-deploy2-provision-vmware/image2.png)

1.  En primer lugar, cargue el VMDK al servidor ESXi. Desplácese hasta la pestaña **Configuration** (Configuración) en el panel derecho. En **Hardware**, seleccione **Storage** (Almacenamiento).

	![](./media/storsimple-ova-deploy2-provision-vmware/image3.png)

1.  En el panel derecho, bajo **Datastores** (Almacenes de datos), seleccione el almacén de datos donde quiere cargar el VMDK. El almacén de datos debe tener espacio suficiente para los discos de datos y el sistema operativo.

	![](./media/storsimple-ova-deploy2-provision-vmware/image4.png)

1.  Haga clic con el botón derecho y seleccione **Browse Datastore** (Examinar almacén de datos).

	![](./media/storsimple-ova-deploy2-provision-vmware/image5.png)

1.  Aparecerá una ventana **Datastore Browser** (Explorador del almacén de datos).

	![](./media/storsimple-ova-deploy2-provision-vmware/image6.png)

1.  En la barra de herramientas, haga clic en el icono ![](./media/storsimple-ova-deploy2-provision-vmware/image7.png) para crear una nueva carpeta. Especifique el nombre de la carpeta y tome nota. Utilizará este nombre de carpeta más adelante al crear una máquina virtual (práctica recomendada). Haga clic en **Aceptar**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image8.png)

1.  La nueva carpeta aparecerá en el panel izquierdo del **Datastore Browser** (Explorador del almacén de datos).

	![](./media/storsimple-ova-deploy2-provision-vmware/image9.png)

1.  Haga clic en el icono de carga ![](./media/storsimple-ova-deploy2-provision-vmware/image10.png) y seleccione **Upload file** (Cargar archivo).

	![](./media/storsimple-ova-deploy2-provision-vmware/image11.png)

1.  Ahora debe examinar y seleccionar el VMDK que descargó.

	![](./media/storsimple-ova-deploy2-provision-vmware/image12.png)

1.  Haga clic en **Abrir**. Ahora se iniciará la carga del archivo VMDK en el almacén de datos especificado.

	![](./media/storsimple-ova-deploy2-provision-vmware/image13.png)

1.  El archivo puede tardar varios minutos en cargarse. Una vez finalizada la carga, verá el archivo en el almacén de datos en la carpeta que creó.

	![](./media/storsimple-ova-deploy2-provision-vmware/image14.png)

1.  Vuelva a la ventana de cliente de vSphere. Con el servidor ESXi seleccionado, haga clic con el botón derecho y seleccione **New Virtual Machine** (Nueva máquina virtual).

	![](./media/storsimple-ova-deploy2-provision-vmware/image15.png)

1.  Se muestra una ventana **Create New Virtual Machine** (Crear nueva máquina virtual). En la página **Configuration** (Configuración), seleccione la opción **Custom** (Personalizado). Haga clic en **Next** (Siguiente). ![](./media/storsimple-ova-deploy2-provision-vmware/image16.png)

2.  En la página **Name and Location** (Nombre y ubicación), especifique el nombre de la máquina virtual. Este nombre debe coincidir con el nombre de carpeta (práctica recomendada) que especificó anteriormente en el paso 8.

	![](./media/storsimple-ova-deploy2-provision-vmware/image17.png)

1.  En la página **Storage** (Almacenamiento), seleccione un almacén de datos que desee utilizar para aprovisionar la máquina virtual.

	![](./media/storsimple-ova-deploy2-provision-vmware/image18.png)

1.  En la página **Virtual Machine Version** (Versión de la máquina virtual), seleccione **Virtual Machine Version: 8** (Versión de la máquina virtual: 8). Tenga en cuenta que esta es la única opción admitida para esta versión.

	![](./media/storsimple-ova-deploy2-provision-vmware/image19.png)

1.  En la página **Guest Operating System** (Sistema operativo invitado), seleccione **Guest Operating System** (Sistema operativo invitado) como **Windows**. Para **Version** (Versión), en la lista desplegable, seleccione **Microsoft Windows Server 2012 (64 bits)**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image20.png)

1.  En la página **CPU**, ajuste el **número de sockets virtuales** y el **número de núcleos por socket virtual** para que el **número total de núcleos** sea de 4 (o más). Haga clic en **Siguiente**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image21.png)

1.  En la página **Memory** (Memoria), especifique 8 GB (o más) de RAM. Haga clic en **Siguiente**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image22.png)

1.  En la página **Network** (Red), especifique el número de interfaces de red. El requisito mínimo es una interfaz de red.

	![](./media/storsimple-ova-deploy2-provision-vmware/image23.png)

1.  En la página **SCSI Controller** (Controlador SCSI), acepte el valor predeterminado **LSI Logic SAS controller** (Controlador LSI Logic SAS).

	![](./media/storsimple-ova-deploy2-provision-vmware/image24.png)

1.  En la página **Select a Disk** (Seleccionar un disco), elija **Use an existing virtual disk** (Utilizar un disco virtual existente). Haga clic en **Siguiente**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image25.png)

1.  En la página **Select Existing Disk** (Seleccionar disco existente), en **Disk File Path** (Ruta de acceso de archivo de disco), haga clic en **Browse** (Examinar). Se abrirá un cuadro de diálogo **Browse Datastores** (Examinar almacenes de datos). Desplácese hasta la ubicación en la que cargó el VMDK. Seleccione el archivo y haga clic en **OK** (Aceptar). Haga clic en **Siguiente**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image26.png)

1.  En la página **Advanced Options** (Opciones avanzadas), acepte el valor predeterminado y haga clic en **Next** (Siguiente).

	![](./media/storsimple-ova-deploy2-provision-vmware/image27.png)

1.  En la página **Ready to Complete** (Listo para completarse), revise toda la configuración asociada a la nueva máquina virtual. Active **Edit the virtual machine settings before completion** (Editar la configuración de la máquina virtual antes de completarse). Haga clic en **Continue**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image28.png)

1.  En la página **Virtual Machines Properties** (Propiedades de las máquinas virtuales), en la pestaña **Hardware**, busque el hardware del dispositivo. Seleccione **New Hard Disk** (Nuevo disco duro). Haga clic en **Agregar**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image29.png)

1.  Se abrirá la ventana **Add Hardware** (Agregar hardware). En la página **Device Type** (Tipo de dispositivo), en **Choose the type of device you wish to add** (Elegir el tipo de dispositivo que desea agregar), seleccione **Hard Disk** (Unidad de disco duro) y haga clic en **Next** (Siguiente).

	![](./media/storsimple-ova-deploy2-provision-vmware/image30.png)

1.  En la página **Select a Disk** (Seleccionar un disco), elija **Create a new virtual disk** (Crear un nuevo disco virtual). Haga clic en **Siguiente**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image31.png)

1.  En la página **Create a Disk** (Crear un disco), cambie el valor de **Disk Size** (Tamaño de disco) a 500 GB (o más). En **Disk Provisioning** (Aprovisionamiento de disco), seleccione **Thin Provision** (Aprovisionamiento fino). Haga clic en **Siguiente**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image32.png)

1.  En la página **Advanced Options** (Opciones avanzadas), acepte el valor predeterminado.

	![](./media/storsimple-ova-deploy2-provision-vmware/image33.png)

1.  En la página **Ready to Complete** (Listo para completarse), revise las opciones del disco. Haga clic en **Finalizar**

	![](./media/storsimple-ova-deploy2-provision-vmware/image34.png)

1.  Ahora volverá a la página Propiedades de máquina virtual. Se agregó un nuevo disco duro a la máquina virtual. Haga clic en **Finalizar**
  
	![](./media/storsimple-ova-deploy2-provision-vmware/image35.png)

2.  Con la máquina virtual seleccionada en el panel derecho, desplácese hasta la pestaña **Summary** (Resumen). Revise la configuración de la máquina virtual.

	![](./media/storsimple-ova-deploy2-provision-vmware/image36.png)

Ahora la máquina virtual está aprovisionada. El paso siguiente es encender este equipo y obtener la dirección IP.

## Paso 3: Iniciar el dispositivo virtual y obtener la dirección IP

Realice los pasos siguientes para iniciar el dispositivo virtual y conectarse a él.

#### Para iniciar el dispositivo virtual

1.  Inicie el dispositivo virtual. En el panel izquierdo del Administrador de configuración de vSphere, seleccione el dispositivo y haga clic con el botón secundario para mostrar el menú contextual. Seleccione **Power** (Encendido) y, a continuación, seleccione **Power on** (Encender). Esto debe encender la máquina virtual. Puede ver el estado en el panel inferior **Recent Tasks** (Tareas recientes) del cliente vSphere.

	![](./media/storsimple-ova-deploy2-provision-vmware/image37.png)

1.  Las tareas de instalación pueden tardar unos minutos en completarse. Una vez que el dispositivo se esté ejecutando, diríjase a la pestaña **Console** (Consola). Presione Ctrl+Alt+Supr para iniciar sesión en el dispositivo. Como alternativa, puede colocar el cursor en la ventana de la consola y presionar Ctrl+Alt+Insert. El usuario predeterminado es *StorSimpleAdmin* y la contraseña predeterminada es *Password1*.

	![](./media/storsimple-ova-deploy2-provision-vmware/image38.png)

1.  Por motivos de seguridad, la contraseña del administrador de dispositivos expirará en el primer inicio de sesión. Se le pedirá que cambie la contraseña.

	![](./media/storsimple-ova-deploy2-provision-vmware/image39.png)

1.  Escriba una contraseña que contenga al menos 8 caracteres. La contraseña debe contener 3 de 4 de estos requisitos: caracteres en mayúsculas, minúsculas, números y caracteres especiales. Vuelva a escribir la contraseña para confirmarla. Se le notificará que la contraseña ha cambiado.

	![](./media/storsimple-ova-deploy2-provision-vmware/image40.png)

1.  Tras cambiar la contraseña correctamente, puede que se reinicie el dispositivo virtual. Espere a que se complete el reinicio. La consola de Windows PowerShell del dispositivo puede mostrarse junto a una barra de progreso.

	![](./media/storsimple-ova-deploy2-provision-vmware/image41.png)

1.  Los pasos 6 a 8 solo se aplican durante el arranque en un entorno sin DHCP. Si se encuentra en un entorno de DHCP, omita estos pasos y vaya al paso 9. Si arranca el dispositivo en un entorno sin DHCP, verá la siguiente pantalla.

	![](./media/storsimple-ova-deploy2-provision-vmware/image42.png)

	Ahora debe configurar la red.

1.  Utilice el comando `Get-HcsIpAddress` para enumerar las interfaces de red habilitadas en el dispositivo virtual. Si el dispositivo tiene una única interfaz de red habilitada, el nombre predeterminado asignado a esta interfaz es `Ethernet`.

	![](./media/storsimple-ova-deploy2-provision-vmware/image43.png)

1.  Utilice el cmdlet Set-HcsIpAddress para configurar la red. A continuación se muestra un ejemplo:


    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

	![](./media/storsimple-ova-deploy2-provision-vmware/image44.png)

1.  Una vez que haya finalizado la instalación inicial y el dispositivo haya arrancado, verá el texto de titular del dispositivo. Anote la dirección IP y la dirección URL que se muestran en el texto del titular para administrar el dispositivo. Utilizará esta dirección IP para conectarse a la interfaz de usuario web del dispositivo virtual y completar la instalación local y el registro.

	![](./media/storsimple-ova-deploy2-provision-vmware/image45.png)

Si el dispositivo no cumple los requisitos mínimos de configuración, verá un error en el texto del titular (se muestra a continuación). Debe modificar la configuración del dispositivo para que tenga los recursos adecuados para cumplir los requisitos mínimos. A continuación, puede reiniciar y conectarse al dispositivo. Consulte los requisitos mínimos de configuración en [Paso 1: Asegurarse de que el sistema host cumple los requisitos mínimos del dispositivo virtual](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-vmware/image46.png)

Si encuentra cualquier otro error durante la configuración inicial mediante la interfaz de usuario web local, consulte los siguientes flujos de trabajo en [Usar la interfaz de usuario web para administrar la matriz virtual de StorSimple (vista previa)](storsimple-ova-web-ui-admin.md).

-   Ejecute pruebas de diagnóstico para [solucionar problemas de configuración de la interfaz de usuario web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Genere el paquete de registro y vea los archivos del registro](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## Pasos siguientes

-   [Configurar la matriz virtual de StorSimple como servidor de archivos](storsimple-ova-deploy3-fs-setup.md)

-   [Configurar la matriz virtual de StorSimple como servidor iSCSI](storsimple-ova-deploy3-iscsi-setup.md)

<!---HONumber=AcomDC_0302_2016-->