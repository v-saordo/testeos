1. Inicie sesión en el [Portal clásico](http://manage.windowsazure.com). Consulte la oferta [gratuita](https://azure.microsoft.com/pricing/free-trial/) si no tiene todavía una suscripción.

2. En la barra de comandos que se encuentra en la parte inferior de la ventana, haga clic en **Nuevo**.

3. En **Proceso**, haga clic en **Máquina virtual**, y, a continuación, en **Desde la galería**.

	![Navigate to From Gallery in the Command Bar](./media/virtual-machines-create-WindowsVM/fromgallery.png)

4. La primera pantalla después de esta acción le permite **Elegir una imagen** para su máquina virtual de la lista de imágenes disponibles. Puede elegir una imagen de la galería o seleccionar de imágenes y discos que ha cargado. Las imágenes disponibles pueden ser diferentes según la suscripción que esté usando.

5. La segunda pantalla permite seleccionar el nombre del equipo y el tamaño, así como el nombre de usuario y la contraseña del usuario administrativo. Use el nivel y el tamaño necesarios para ejecutar la aplicación o carga de trabajo. A continuación se incluyen algunas sugerencias:

	- El **nombre de máquina virtual** solo puede contener letras, números y guiones. También debe empezar por una letra y terminar por una letra o un número.
	- **Nuevo nombre de usuario** se refiere a la cuenta administrativa que se usa para administrar el servidor. La contraseña debe tener entre 8 y 123 caracteres y al menos tres de los siguientes valores: una minúscula, una mayúscula, un número y un carácter especial. **Necesitará el nombre de usuario y la contraseña para iniciar sesión en la máquina virtual**.
	- El tamaño de la máquina virtual afecta al coste de usarla, así como a algunas opciones de configuración como el número de discos de datos que se pueden acoplar. Para más información, consulta el tema [Tamaños de máquinas virtuales](virtual-machines-size-specs.md).

6. La tercera pantalla permite configurar los recursos relacionados con las redes, el almacenamiento y la disponibilidad. A continuación se incluyen algunas sugerencias:

	- El **Nombre DNS de servicio en la nube** es el nombre DNS global que forma parte del URI que se usa para establecer contacto con la máquina virtual. Tendrá que proponer su propio nombre de servicio en la nube porque debe ser único en Azure. Los servicios en la nube tienen importancia en los escenarios que usan [varias máquinas virtuales](cloud-services-connect-virtual-machine.md).

	- En** Región/Grupo de afinidad/Red virtual**, use una región que sea adecuada para su ubicación. También puede optar por especificar una red virtual en su lugar.

	>[AZURE.NOTE] Si desea que una máquina virtual use una red virtual, **debe** especificar la red virtual cuando cree la máquina virtual. No puede incorporar una máquina virtual a una red virtual después de crear la VM. Para obtener más información, consulte [Información general sobre redes virtuales de Azure](virtual-networks-overview.md).
	>
	> Para obtener información detallada sobre la configuración de extremos, consulte [Configuración de extremos en una máquina virtual](virtual-machines-set-up-endpoints.md).

7. la cuarta pantalla de configuración le permite instalar el agente de máquina virtual y configurar algunas de las extensiones disponibles.

	>[AZURE.NOTE] El agente de máquina virtual proporciona el entorno para que pueda instalar las extensiones que pueden ayudarle a interactuar con la máquina virtual o a administrarla. Para saber más detalles, consulta el tema [Acerca del agente de máquinas virtuales y extensiones](virtual-machines-extensions-agent-about.md).

8. Una vez creada la máquina virtual, en el portal clásico se muestra la nueva máquina virtual en **Máquinas virtuales**. También se crean el servicio en la nube y la cuenta de almacenamiento correspondientes y se muestran en dichas secciones. La máquina virtual y el servicio en la nube se inician automáticamente, y su estado aparece como **En ejecución**.

	![Configure el agente de máquina virtual y los puntos de conexión de la máquina virtual](./media/virtual-machines-create-WindowsVM/vmcreated.png)

<!---HONumber=AcomDC_0204_2016-->