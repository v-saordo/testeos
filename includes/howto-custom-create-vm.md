#Creación de una máquina virtual personalizada

Una máquina virtual *personalizada* se refiere a una máquina virtual creada con el método **Desde la galería** porque le permite configurar más opciones que el método **Creación rápida**. Entre estas opciones se incluyen:

- Numerosas opciones para la imagen utilizada para crear una máquina virtual (VM)
- Conexión de la VM a una red virtual
- Adición de la VM a un servicio en la nube existente
- Adición de la VM a un conjunto de disponibilidad

> [AZURE.IMPORTANT] Si desea que la máquina virtual use una red virtual, con el fin de poder conectarse a ella directamente mediante un nombre de host o configurar conexiones entre locales, asegúrese de que especifica la red virtual al crear la máquina virtual. Puede configurarse una máquina virtual para que se una a una red virtual solo cuando se cree la máquina virtual. Para obtener más información acerca de redes virtuales, consulte [Información general sobre redes virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

1. Inicie sesión en el [Portal de Azure](http://manage.windowsazure.com).

2. En la barra de comandos, haga clic en **Nuevo**.

3. Haga clic en **Proceso**, en **Máquina virtual** y, a continuación, haga clic en **Desde la galería**.

4. Elija la imagen que desee usar y luego haga clic en la flecha para continuar.

5. Si hay varias versiones disponibles de la imagen, en **Fecha de lanzamiento de la versión**, seleccione la versión que desea usar.

6. En **Nombre de máquina virtual**, escriba el nombre que desea usar para la máquina virtual.

7. Utilice **Nivel** y **Tamaño** para seleccionar el tamaño adecuado de máquina virtual. El tamaño que seleccione afectará a la configuración máxima de la máquina virtual y también a su precio. Para obtener los detalles de configuración, consulte [Tamaños de máquinas virtuales y servicios en la nube de Azure](http://go.microsoft.com/fwlink/p/?LinkID=389844).

8. En **Nuevo nombre de usuario**, escriba un nombre para la cuenta administrativa que desea usar para administrar el servidor.

9. En **Nueva contraseña**, escriba una contraseña segura para la cuenta administrativa. En **Confirmar contraseña**, vuelva a escribir la contraseña.

10. Haga clic en la flecha para continuar.

11. En **Servicio en la nube**, realice los siguientes pasos:

	- Si es la primera o única máquina virtual en el servicio en la nube, seleccione **Crear un nuevo servicio en la nube**. A continuación, en **Nombre DNS de servicio en la nube**, escriba un nombre que use entre 3 y 24 letras minúsculas y números. Este nombre se convierte en parte del URI que se usa para ponerse en contacto con la máquina virtual mediante el servicio en la nube.
	- Si la máquina virtual se agrega a un servicio en la nube, selecciónelo en la lista.

	> [AZURE.NOTE] para obtener más información sobre la colocación de máquinas virtuales en el mismo servicio en la nube, consulte [Conexión de máquinas virtuales en un Servicio en la nube](https://azure.microsoft.com/manage/windows/how-to-guides/connect-to-a-cloud-service/).

12. En **Región/grupo de afinidad/red virtual**, seleccione la región, el grupo de afinidad o la red virtual que desea usar con la máquina virtual. Para obtener más información sobre los grupos de afinidad, consulte [Acerca de los grupos de afinidad para la red virtual](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

13. En **Cuenta de almacenamiento**, seleccione una cuenta de almacenamiento existente para el archivo VHD o use una cuenta de almacenamiento generada automáticamente. Solo se crea una cuenta de almacenamiento por región de manera automática. Todas las demás máquinas virtuales que crea con esta configuración se ubican en esta cuenta de almacenamiento. Tiene un límite de 20 cuentas de almacenamiento.

14. Si desea que la máquina virtual pertenezca a un conjunto de disponibilidad, en **Conjunto de disponibilidad**, seleccione **Crear conjunto de disponibilidad** o agréguela a un conjunto de disponibilidad existente.

	**Nota**: las máquinas virtuales de un conjunto de disponibilidad se implementan en distintos dominios de error. La colocación de varias máquinas virtuales en un conjunto de disponibilidad ayuda a garantizar que la aplicación esté disponible durante los errores de red, los errores de hardware de disco local y cualquier tiempo de inactividad planificado.

15.  En **Extremos**, revise los nuevos extremos que se crearán para permitir las conexiones con la máquina virtual, como el cliente Shell seguro (SSH) o Escritorio remoto. Puede también agregar extremos ahora, o crearlos más tarde. Para obtener instrucciones sobre la creación de extremos más adelante, consulte [Configuración de extremos en una máquina virtual](../articles/virtual-machines/virtual-machines-set-up-endpoints.md).

16.  En **Agente de máquina virtual**, decida si va a instalar el Agente de VM. Este agente proporciona el entorno para que pueda instalar las extensiones que pueden ayudarlo a interactuar con la máquina virtual. Para conocer los detalles, consulte [Administrar extensiones](http://go.microsoft.com/FWLink/p/?LinkID=390493).

17. Haga clic en la flecha para crear la máquina virtual.

	![Creación correcta de la máquina virtual personalizada](./media/howto-custom-create-vm/VMSuccessWindows.png)

##Pasos siguientes##
Una vez creada una máquina virtual, esta arrancará automáticamente. Cuando el portal se muestre el estado como en ejecución, podrá iniciar sesión en la máquina virtual. Si desea instrucciones, consulte uno de los artículos siguientes:

- [Inicio de sesión en una máquina virtual con Linux](../articles/virtual-machines/virtual-machines-linux-how-to-log-on.md)
- [Inicio de sesión en una máquina virtual con Windows Server](../articles/virtual-machines/virtual-machines-log-on-windows-server.md)

<!---HONumber=AcomDC_0128_2016-->