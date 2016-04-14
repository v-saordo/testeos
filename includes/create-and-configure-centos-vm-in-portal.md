
**Nota**: en este artículo se crea una máquina virtual que no está conectada a una red virtual. Si desea que su máquina virtual use una red virtual para permitir la conexión a las máquinas virtuales directamente mediante un nombre de host o que configure conexiones entre entornos, utilice el método **Desde galería** y especifique la red virtual cuando cree la máquina virtual. Para obtener más información sobre las redes virtuales, consulte [Información general sobre redes virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

1. Inicie sesión en el Portal de administración de Azure con la cuenta de Azure.
2. En el Portal de administración, en la parte inferior izquierda de la página web, haga clic en **+Nuevo**, en **Máquina virtual** y, a continuación, en **Desde la galería**.

	![Crear una máquina virtual][Image1]

3. Seleccione una imagen de máquina virtual CentOS en **Platform Images** y, a continuación, haga clic en la flecha de avance de la parte inferior derecha de la página.
	
4. En la página **Configuración de la máquina virtual**, proporcione la siguiente información:
	- Proporcione un **Nombre de máquina virtual**, como "testlinuxvm".
	- Especifique un **Nombre de usuario nuevo**, como "newuser", que se agregará al archivo de lista de Sudoers.
	- En el cuadro **Contraseña segura**, escriba una [contraseña segura](http://msdn.microsoft.com/library/ms161962.aspx).
	- En el cuadro **Confirmar contraseña**, vuelva a escribir la contraseña.
	- Seleccione el **Tamaño** adecuado en la lista desplegable.

	Haga clic en la flecha de avance para continuar.
	
5. Proporcione la siguiente información en la página **Modo de máquina virtual**:
	- Seleccione **Máquina Virtual independiente**.
	- En el cuadro **Nombre DNS**, escriba una dirección DNS válida. Por ejemplo, "testlinuxvm".
	- En el cuadro **Cuenta de almacenamiento**, seleccione **Usar una cuenta de almacenamiento generada automáticamente**.
	- En el cuadro **Región/Grupo de afinidad/Red virtual**, seleccione una región donde se hospedará esta imagen virtual.

	Haga clic en la flecha de avance para continuar.

6. En la página **Opciones de la máquina virtual**, seleccione **(ninguno)** en el cuadro **Conjunto de disponibilidad**.

	Haga clic en la marca de verificación para continuar.
	
7. Espere a que Azure prepare la máquina virtual.

##Configuración de extremos
Una vez creada una máquina virtual, configure los extremos para realizar la conexión remota.

1. En el Portal de administración, haga clic en **Máquinas virtuales**, en el nombre de la nueva máquina virtual y, a continuación, en **Extremos**.

2. Haga clic en **Editar extremo** en la parte inferior de la página y edite el extremo SSH de manera que **Puerto público** sea 22.

##Conexión a una máquina virtual
Una vez que se haya realizado el aprovisionamiento en la máquina virtual y los extremos se hayan configurado, puede proceder a la conexión mediante SSH o PuTTY.

###Conexión mediante SSH
Si usa Linux, realice la conexión a la máquina virtual mediante SSH. En el símbolo del sistema, ejecute:

	$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180

Especifique la contraseña del usuario.

###Conexión mediante PuTTY
Si usa un equipo con Windows, conéctese a la máquina virtual mediante PuTTY. PuTTY puede descargarse desde la [página de descarga de PuTTY][PuTTYDownLoad] (en inglés).

1. Descargue y guarde **putty.exe** en un directorio del equipo. Abra un símbolo del sistema, vaya hasta la carpeta correspondiente y ejecute **putty.exe**.

2. Especifique "testlinuxvm.cloudapp.net" en **Nombre de host** y "22" en **Puerto**. ![Pantalla de PuTTY][Image6]

##Actualización de la máquina virtual (opcional)
Una vez que se haya conectado a la máquina virtual, tiene la opción de instalar actualizaciones. Ejecutar:

	$ sudo yum update

Especifique la contraseña de nuevo. Espere a que las actualizaciones se instalen.


[PuTTYDownload]: http://www.puttyssh.org/download.html

[Image1]: ./media/create-and-configure-centos-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-centos-vm-in-portal/putty.png

<!---HONumber=August15_HO6-->