1. En el [Portal de administración de Azure](http://manage.windowsazure.com), haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual que acaba de crear (**testlinuxvm**).

2. En la barra de comandos, haga clic en **Attach** y, a continuación, haga clic en **Attach Empty Disk**.

	Aparece el cuadro de diálogo **Conectar disco vacío**.


3. El **nombre de máquina virtual**, la **ubicación de almacenamiento** y el **nombre de archivo** ya están definidos. Solo tiene que especificar el tamaño que desea utilizar para el disco. Escriba **5** en el campo **Tamaño**.

	![Acoplar disco vacío][Image2]

	**Nota:** todos los discos se crean a partir de un archivo .vhd en el almacenamiento de Azure. Puede proporcionar un nombre para el archivo .vhd que se agregue al almacenamiento, pero Azure genera el nombre del disco automáticamente.

4. Haga clic en la marca de verificación para acoplar el disco de datos a la máquina virtual.

5. Haga clic en el nombre de la máquina virtual para mostrar el panel de modo que pueda comprobar que el disco de datos se ha adjuntado correctamente a la máquina virtual. El disco que conectó aparece en la tabla **Discos**.

	Al conectar un disco de datos, este no estará listo para usar hasta que inicie sesión para completar la configuración.

##Conexión a la máquina virtual con SSH o PuTTY y finalización de la instalación
Inicie sesión en la máquina virtual para completar la configuración del disco de modo que pueda usarlo para almacenar datos.

1. Una vez aprovisionada la máquina virtual, conéctese con SSH o PuTTY e inicie sesión como **newuser** (tal como se describe en los pasos anteriores).	


2. En la ventana SSH o PuTTY, escriba el siguiente comando y, a continuación, la contraseña de la cuenta:

	`$ sudo grep SCSI /var/log/messages`

	Puede buscar el identificador del último disco de datos que se agregó en los mensajes que se muestran (**sdc**, en este ejemplo).

	![GREP][Image4]


3. En la ventana SSH o PuTTY, escriba el siguiente comando para crear una partición en el disco **/dev/sdc**:

	`$ sudo fdisk /dev/sdc`


4. Escriba **n** para crear una nueva partición.

	![FDISK][Image5]


5. Escriba **p** para que la partición sea la partición principal, escriba **1** para que la partición sea la primera partición y, a continuación, escriba Entrar para aceptar el valor predeterminado (1) para el cilindro.

	![FDISK][Image6]


6. Escriba **p** para ver los detalles del disco en el que se va a crear la partición.

	![FDISK][Image7]


7. Escriba **w** para escribir la configuración del disco.

	![FDISK][Image8]


8. Dé formato al nuevo disco con el comando **mkfs**:

	`$ sudo mkfs -t ext4 /dev/sdc1`

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

	Si se crean particiones o unidades de datos adicionales, tendrá que especificarlas también en /etc/fstab por separado.

	Ahora puede probar que el sistema de archivos está correctamente montado con solo desmontar y volver a montar el sistema de archivos, es decir, usando el punto de montaje de ejemplo `/datadrive` que se creó en los pasos anteriores:

		`sudo umount /datadrive`
		`sudo mount /datadrive`

	Si el segundo comando genera un error, compruebe la sintaxis correcta del archivo /etc/fstab.


	>[AZURE.NOTE]Posteriormente, la eliminación de un disco de datos sin editar fstab podría provocar un error en el inicio de la máquina virtual. Si ocurre habitualmente, la mayoría de las distribuciones proporcionan las opciones de fstab `nofail` o `nobootwait` que permitirán que el sistema se inicie, incluso si el disco no está presente. Consulte la documentación de su distribución para obtener más información sobre estos parámetros.


[Image2]: ./media/attach-data-disk-centos-vm-in-portal/AttachDataDiskLinuxVM2.png
[Image4]: ./media/attach-data-disk-centos-vm-in-portal/GrepScsiMessages.png
[Image5]: ./media/attach-data-disk-centos-vm-in-portal/fdisk1.png
[Image6]: ./media/attach-data-disk-centos-vm-in-portal/fdisk2.png
[Image7]: ./media/attach-data-disk-centos-vm-in-portal/fdisk3.png
[Image8]: ./media/attach-data-disk-centos-vm-in-portal/fdisk4.png
[Image9]: ./media/attach-data-disk-centos-vm-in-portal/mkfs.png

<!---HONumber=Oct15_HO3-->