
# Creación de una máquina virtual que ejecuta Windows Server #

Este tutorial muestra lo fácil que es crear una máquina virtual de Azure que ejecuta Windows Server, con la Galería de imágenes en el Portal de administración de Microsoft Azure. La Galería de imágenes ofrece una variedad de imágenes, incluidas imágenes de aplicaciones, sistemas operativos basados en Linux y sistemas operativos Windows.

> [AZURE.NOTE]No se necesita experiencia previa con máquinas virtuales de Azure para finalizar este tutorial. Sin embargo, necesitará una cuenta de Azure. Puede crear una cuenta de evaluación gratuita en un par de minutos. Para obtener más información, consulte [Crear una cuenta de Azure](http://www.windowsazure.com/develop/php/tutorials/create-a-windows-azure-account/).

Este tutorial muestra:

- [Creación de la máquina virtual](#createvirtualmachine)
- [Inicio de sesión en la máquina virtual después de crearla](#logon)
- [Acoplamiento de un disco de datos a la nueva máquina virtual](#attachdisk)

Si desea saber más, consulte [Máquinas virtuales](http://go.microsoft.com/fwlink/p/?LinkID=271224).


##<a id="createvirtualmachine"> </a>Creación de la máquina virtual##

En esta sección se muestra cómo utilizar la opción **De la galería** en el Portal de administración para crear la máquina virtual. Esta opción proporciona más opciones de configuración que la opción **Creación rápida**. Por ejemplo, si desea conectar una máquina virtual a una red virtual, necesitará usar la opción **De la galería**.

[AZURE.INCLUDE [virtual-machines-create-windowsvm](../includes/virtual-machines-create-windowsvm.md)]

## <a id="logon"> </a>Inicio de sesión en la máquina virtual después de crearla ##

En esta sección se muestra cómo iniciar sesión en una máquina virtual para poder administrar su configuración y las aplicaciones que va a ejecutar en ella.

1. Inicie sesión en el [Portal de administración](http://manage.windowsazure.com).

2. Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual **MyTestVM**.

	![Seleccione MyTestVM](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)

3. En la barra de comandos, haga clic en **Conectar**.

	![Conecte a MyTestVM](./media/CreateVirtualMachineWindowsTutorial/commandbarconnect.png)
	
4. Haga clic en **Abrir** para usar el archivo de protocolo de escritorio remoto que se creó automáticamente para la máquina virtual.

	![Abra el archivo rdp](./media/CreateVirtualMachineWindowsTutorial/openrdp.png)
	
5. Haga clic en **Conectar**.

	![Continuar con la conexión](./media/CreateVirtualMachineWindowsTutorial/connectrdc.png)

6. En el cuadro de contraseña, escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual y, a continuación, haga clic en**Aceptar**.

7. Haga clic en **Sí** para comprobar la identidad de la máquina virtual.

	![Verificar la identidad de la máquina](./media/CreateVirtualMachineWindowsTutorial/certificate.png)

	Ahora puede trabajar con la máquina virtual al igual como lo hace con un servidor en su oficina.

## <a id="attachdisk"> </a>Acoplamiento de un disco de datos a la nueva máquina virtual ##

En esta sección se muestra cómo asociar un disco de datos vacío a una máquina virtual. Consulte el tutorial sobre cómo [acoplar un disco de datos](../articles/virtual-machines/storage-windows-attach-disk.md) para obtener más información sobre la asociación de discos vacíos y discos existentes.

1. Inicie sesión en el [Portal de administración](http://manage.windowsazure.com) de Azure.

2. Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual **MyTestVM**.

	![Seleccione MyTestVM](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)
	
3. Quizá el sistema le ha llevado primero a la página Inicio rápido. Si es así, seleccione **Panel** en la parte superior.

	![Seleccione Panel](./media/CreateVirtualMachineWindowsTutorial/dashboard.png)

4. En la barra de comandos, haga clic en **Conectar** y, a continuación, en **Conectar disco vacío** cuando aparezca esta opción.

	![Seleccione Adjuntar en la barra de comandos](./media/CreateVirtualMachineWindowsTutorial/commandbarattach.png)

5. Las opciones **Nombre de la máquina virtual**, ** Ubicación de almacenamiento**, ** Nombre de archivo** y** Preferencia de caché de host** ya están predefinidas. Solo tiene que especificar el tamaño que desea utilizar para el disco. Escriba **5** en el campo **Tamaño**. Haga clic en la marca de verificación para asociar el disco vacío a la máquina virtual.

	![Especifique el tamaño del disco vacío](./media/CreateVirtualMachineWindowsTutorial/emptydisksize.png)
	
	>[AZURE.NOTE]Todos los discos se crean a partir de un archivo VHD en el almacenamiento de Microsoft Azure. En **Nombre de archivo**, puede proporcionar un nombre para el archivo VHD que se agrega al almacenamiento, pero Azure genera el nombre del disco automáticamente.

6. Vuelva al panel para comprobar que el disco de datos vacío se ha asociado correctamente a la máquina virtual. Debe aparecer como segundo disco en la lista **Discos** junto con Disco del SO.

	![Acoplar disco vacío](./media/CreateVirtualMachineWindowsTutorial/disklistwithdatadisk.png)

	Después de acoplar el disco de datos a la máquina virtual, el disco permanecerá desconectado y no inicializado. Antes de que pueda usarlo para almacenar datos, tendrá que iniciar sesión en la máquina virtual e inicializarlo.

7. Conéctese a la máquina virtual usando los pasos que se mencionaron en la sección anterior, [Inicio de sesión en una máquina virtual después de su creación](#logon).

8. Después de iniciar sesión en la máquina virtual, abra el **Administrador del servidor**. En el panel izquierdo, seleccione **Servicios de archivos y almacenamiento**.

	![Expanda Servicios de archivos y almacenamiento en el Administrador del servidor](./media/CreateVirtualMachineWindowsTutorial/fileandstorageservices.png)

9. Seleccione **Discos** en el menú expandido.

	![Expanda Servicios de archivos y almacenamiento en el Administrador del servidor](./media/CreateVirtualMachineWindowsTutorial/selectdisks.png)
	
10. En la sección **Discos**, hay tres discos en la lista: disco 0, disco 1 y disco 2. El disco 0 es el disco del sistema operativo, el disco 1 es un disco de recursos temporales (que no debe usarse para almacenamiento de datos) y el disco 2 es el disco de datos que ha conectado a la máquina virtual. Tenga en cuenta que el disco de datos tiene una capacidad de 5 GB como se especificó anteriormente. Haga clic con el botón secundario en el disco 2 y seleccione **Inicializar**.

	![Comenzar la inicialización](./media/CreateVirtualMachineWindowsTutorial/initializedisk.png)

11. Haga clic en **Sí** para comenzar el proceso de inicialización.

	![Continúe la inicialización](./media/CreateVirtualMachineWindowsTutorial/yesinitialize.png)

12. Haga clic con el botón secundario de nuevo en el disco 2 y seleccione **Nuevo volumen **.

	![Crear el volumen](./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png)

13. Complete el asistente usando los valores predeterminados que se proporcionan. Una vez finalizado el asistente, aparece un volumen nuevo en la sección **Volúmenes**.

	![Crear el volumen](./media/CreateVirtualMachineWindowsTutorial/newvolumecreated.png)

	El disco está ahora en línea y listo para usarse con una nueva letra de unidad.
	
##Pasos siguientes 

Para obtener más información sobre la configuración de máquinas virtuales de Windows en Azure, consulte los siguientes artículos:

[Conexión de máquinas virtuales en un Servicio en la nube](../articles/virtual-machines/cloud-services-connect-virtual-machine.md)

[Creación y carga de su propio disco duro virtual con el sistema operativo Windows Server](../articles/virtual-machines/virtual-machines-create-upload-vhd-windows-server.md)

[Acoplamiento de discos de datos a una máquina virtual](../articles/virtual-machines/storage-windows-attach-disk.md)

[Administración de la disponibilidad de las máquinas virtuales](../articles/manage-availability-virtual-machines.md)

[About virtual machines in Azure]: #virtualmachine
[How to create the virtual machine]: #custommachine
[How to log on to the virtual machine after you create it]: #logon
[How to attach a data disk to the new virtual machine]: #attachdisk
[How to set up communication with the virtual machine]: #endpoints

<!---HONumber=August15_HO6-->