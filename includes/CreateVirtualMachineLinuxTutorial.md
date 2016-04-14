<!---
Don't use this file. It's deprecated and will be removed. Instead use, virtual-machines-linux-tutorial-log-on-attach-disk.md
-->

## <a id="logon"> </a>Inicio de sesión en una máquina virtual después de su creación ##

Para administrar la configuración de la máquina virtual y las aplicaciones que se ejecutan en la máquina, puede usar un cliente de SSH. Para ello, debe instalar un cliente de SSH en el equipo que desea usar para tener acceso a la máquina virtual. Existen muchos programas cliente de SSH donde elegir. Las siguientes son algunas posibles opciones:

- Si va a usar un equipo que está ejecutando un sistema operativo Windows, es posible que desee usar un cliente de SSH, como por ejemplo PuTTY. Para obtener más información, consulte [Descarga de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
- Si va a usar un equipo que está ejecutando un sistema operativo Linux, es posible que desee usar un cliente de SSH, como por ejemplo OpenSSH. Para obtener más información, consulte [OpenSSH](http://www.openssh.org/).

Este tutorial le muestra cómo usar el programa PuTTY para tener acceso a la máquina virtual.

1. Busque el **nombre de host** y la **información de puerto** en el Portal de administración. Puede encontrar la información que necesita en el panel de la máquina virtual. Haga clic en el nombre de la máquina virtual y busque **Detalles de SSH** en la sección **Vista rápida** del panel.

	![Buscar detalles de SSH](./media/CreateVirtualMachineLinuxTutorial/SSHdetails.png)

2. Abra el programa PuTTY.

3. Escriba el **nombre de host** y la **información de puerto** que recopiló del panel y, a continuación, haga clic en **Abrir**.

	![Escribir el nombre de host y la información de puerto](./media/CreateVirtualMachineLinuxTutorial/putty.png)

4. Inicie sesión en la máquina virtual usando la cuenta NewUser1 que se agregó cuando creó la máquina virtual.

	![Iniciar sesión en la nueva máquina virtual](./media/CreateVirtualMachineLinuxTutorial/sshlogin.png)

	Ahora puede trabajar con la máquina virtual igual que hace con cualquier otro servidor.


## <a id="attachdisk"> </a>Acoplamiento de un disco de datos a la nueva máquina virtual ##

Es posible que su aplicación necesite almacenar datos. Para hacerlo posible, adjunta un disco de datos a la máquina virtual que creó anteriormente. La manera más fácil de hacerlo es adjuntar un disco de datos vacío a la máquina.

**Nota: disco de datos frente a disco de recursos** Los discos de datos residen en el Almacenamiento de Azure y se pueden usar para un almacenamiento continuo de archivos y datos de aplicaciones.

Cada máquina virtual que se crea tiene también adjunto un *disco de recursos* local temporal. Debido a que los datos de un disco de recursos podrían no resistir los diversos reinicios, muchas veces los usan aplicaciones y procesos que se ejecutan en la máquina virtual para un almacenamiento de datos transitorio ytemporal. También se usan para almacenar archivos de intercambio y de paginación para el sistema operativo.

En Linux, el disco de recursos se administra generalmente mediante el agente de Linux de Azure y se monta automáticamente en **/mnt/resource** (o **/mnt** en las imágenes de Ubuntu). Tenga en cuenta que el disco de recursos es un disco *temporal* que debe vaciarse cuando la máquina virtual se desaprovisiona. Por otro lado, en Linux el kernel podría denominar al disco de datos como `/dev/sdc`, y los usuarios necesitarán crear particiones, dar formato y montar ese recurso. Consulte la [guía de usuario del Agente de Linux de Azure](http://www.windowsazure.com/manage/linux/how-to-guides/linux-agent-guide/) para obtener más información.



1. Si no lo ha hecho todavía, inicie sesión en el Portal de administración de Azure.

2. Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual **MyTestVM1** que creó anteriormente.

3. En la barra de comandos, haga clic en **Conectar** y luego en **Conectar disco vacío**.
	
	Aparece el cuadro de diálogo **Conectar disco vacío**.

	![Definir los detalles del disco](./media/CreateVirtualMachineLinuxTutorial/attachnewdisklinux.png)

4. El **nombre de máquina virtual**, la **ubicación de almacenamiento** y el **nombre de archivo** ya están definidos. Solo tiene que especificar el tamaño que desea utilizar para el disco. Escriba **5** en el campo **Tamaño**.

	**Nota:** todos los discos se crean a partir de un archivo .vhd en el almacenamiento de Azure. Puede proporcionar un nombre para el archivo VHD que se agregue al almacenamiento, pero el nombre del disco se general automáticamente.

5. Haga clic en la marca de verificación para acoplar el disco de datos a la máquina virtual.

6. Puede verificar que el disco de datos se haya adjuntado correctamente a la máquina virtual si observa el panel. Haga clic en el nombre de la máquina virtual para ver el panel.

	La cantidad de discos ahora es 2 para la máquina virtual, y el disco que adjuntó se enumera en la tabla **Discos**.

	![Disco adjuntado correctamente](./media/CreateVirtualMachineLinuxTutorial/attachemptysuccess.png)


El disco de datos que acaba de adjuntar a la máquina virtual permanecerá desconectado y no inicializado después de haberlo agregado. Debe iniciar sesión en la máquina virtual e inicializar el disco para usarlo para almacenar datos.

1. Conéctese a la máquina virtual siguiendo los pasos que se mencionaron anteriormente en **Inicio de sesión en una máquina virtual después de su creación**.


2. En la ventana SSH, escriba el siguiente comando y, a continuación, la contraseña de la cuenta:

	`sudo grep SCSI /var/log/messages`

	Puede buscar el identificador del último disco de datos conectado en los mensajes que se muestran.

	![Identificar disco](./media/CreateVirtualMachineLinuxTutorial/diskmessages.png)


3. En la ventana SSH, escriba el siguiente comando para crear un nuevo dispositivo y, a continuación, especifique la contraseña de la cuenta:

	`sudo fdisk /dev/sdc`

	>[AZURE.NOTE]En este ejemplo, es posible que tenga que utilizar `sudo -i` en algunas distribuciones si /sbin o /usr/sbin no se encuentran en su `$PATH`.


4. Escriba **n** para crear una nueva partición.

	![Crear un dispositivo nuevo](./media/CreateVirtualMachineLinuxTutorial/diskpartition.png)


5. Escriba **p** para que la partición sea la partición principal, escriba **1** para que sea la primera partición y, a continuación, escriba "enter" para aceptar el valor predeterminado para el cilindro.

	![Crear partición](./media/CreateVirtualMachineLinuxTutorial/diskcylinder.png)


6. Escriba **p** para ver los detalles del disco en el que se va a crear la partición.

	![Enumerar la información del disco](./media/CreateVirtualMachineLinuxTutorial/diskinfo.png)


7. Escriba **w** para escribir la configuración del disco.

	![Escribir los cambios del disco](./media/CreateVirtualMachineLinuxTutorial/diskwrite.png)


8. Debe crear el sistema de archivos en la partición nueva. Como ejemplo, escriba el siguiente comando para crear el sistema de archivos y, a continuación, especifique la contraseña de la cuenta:

	`sudo mkfs -t ext4 /dev/sdc1`

	![Crear sistema de archivos](./media/CreateVirtualMachineLinuxTutorial/diskfilesystem.png)

	>[AZURE.NOTE]Tenga en cuenta que los sistemas SUSE Linux Enterprise 11 ofrecen únicamente acceso de solo lectura a sistemas de archivos ext4. Para estos sistemas, es recomendable dar formato al nuevo sistema de archivos como ext3 en vez de ext4.


9. A continuación, debe tener un directorio disponible para montar el nuevo sistema de archivos. Como ejemplo, escriba el siguiente comando para crear un nuevo directorio para el montaje de la unidad y, a continuación, especifique la contraseña de la cuenta:

	`sudo mkdir /datadrive`


10. Escriba el siguiente comando para montar la unidad:

	`sudo mount /dev/sdc1 /datadrive`

	El disco de datos está ahora listo para usarse como **/datadrive**.


11. Agregue la nueva unidad a /etc/fstab:

	Para asegurarse de que la unidad se vuelve a montar automáticamente después de reiniciar, debe agregarse al archivo /etc/fstab. Además, se recomienda encarecidamente que se use el UUID (identificador único global) en /etc/fstab para hacer referencia a la unidad en lugar de solo el nombre del dispositivo (es decir, /dev/sdc1). Para buscar el UUID de la unidad nueva, puede usar la utilidad **blkid**:
	
		`sudo -i blkid`

	El resultado será similar al siguiente:

		`/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
		`/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
		`/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`

	>[AZURE.NOTE]Es posible que blkid no requiera acceso sudo en todos los casos. Sin embargo, puede que sea más sencillo ejecutar con `sudo -i` en algunas distribuciones si /sbin o /usr/sbin no se encuentran en su `$PATH`.

	**Precaución:** la edición incorrecta del archivo /etc/fstab puede tener como resultado un sistema que no se pueda arrancar. Si no está seguro, consulte la documentación de distribución para obtener información sobre cómo editar correctamente ese archivo. También se recomienda realizar una copia de seguridad del archivo /etc/fstab antes de editarlo.

	Use un editor de texto para especificar la información sobre el nuevo sistema de archivos al final del archivo /etc/fstab. En este ejemplo usaremos el valor de UUID para el nuevo dispositivo **/dev/sdc1** que se creó en los pasos anteriores y el punto de montaje **/datadrive**:

		`UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`

	O bien, en sistemas basados en SUSE Linux, es posible que tenga que utilizar un formato diferente:

		`/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`

	Si se crean particiones o unidades de datos adicionales, tendrá que especificarlas también en /etc/fstab por separado.

	Ahora puede probar que el sistema de archivos está correctamente montado con solo desmontar y volver a montar el sistema de archivos, es decir, usando el punto de montaje de ejemplo `/datadrive` que se creó en los pasos anteriores:

		`sudo umount /datadrive`
		`sudo mount /datadrive`

	Si el segundo comando genera un error, compruebe la sintaxis correcta del archivo /etc/fstab.


	>[AZURE.NOTE]Posteriormente, la eliminación de un disco de datos sin editar fstab podría provocar un error en el inicio de la máquina virtual. Si ocurre habitualmente, la mayoría de las distribuciones proporcionan las opciones de fstab `nofail` o `nobootwait` que permitirán que el sistema se inicie, incluso si el disco no está presente. Consulte la documentación de su distribución para obtener más información sobre estos parámetros.

<!---HONumber=August15_HO6-->