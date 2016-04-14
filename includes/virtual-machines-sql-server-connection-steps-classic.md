### Determinación del nombre DNS de la máquina virtual

Para conectarse al motor de base de datos de SQL Server desde otro equipo, debe conocer el nombre del Sistema de nombres de dominio (DNS) de la máquina virtual. (Este es el nombre que Internet utiliza para identificar la máquina virtual. Puede utilizar la dirección IP, pero esta podría cambiar cuando Azure mueva recursos por redundancia o mantenimiento. El nombre DNS será estable, porque se puede redirigir a una dirección IP nueva).

1. En el Portal de administración de Azure (o desde el paso anterior), seleccione **MÁQUINAS VIRTUALES**. 

2. En la página **INSTANCIAS DE MÁQUINA VIRTUA**L, en la columna **Vista rápida**, encuentre y copie el nombre DNS para la máquina virtual.

	![Nombre DNS](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)
	

### Conexión al motor de base de datos desde otro equipo
 
1. En otro equipo conectado a Internet, abra SQL Server Management Studio.
2. En el cuadro de diálogo **Conectar al servidor ** o **Conectar al motor de base de datos**, en el cuadro **Nombre del servidor**, escriba el nombre DNS de la máquina virtual (determinado en la tarea anterior) y un número de puerto de extremo público con formato *NombreDNS,nombrepuerto*, tal como **tutorialtestVM.cloudapp.net,57500**. Para obtener el número de puerto, inicie sesión en el Portal de administración de Azure y encuentre la máquina virtual. En el panel, haga clic en **EXTREMOS** y use el **PUERTO PÚBLICO** asignado a **MSSQL**. ![Public Port](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. En el cuadro **Autenticación**, seleccione **Autenticación de SQL Server**.
5. En el cuadro **Inicio de sesión**, escriba el nombre de un inicio de sesión que haya creado en una tarea anterior.
6. En el cuadro **Contraseña**, escriba la contraseña del inicio de sesión que creó en una tarea anterior.
7. Haga clic en **Conectar**.

	![Conectar mediante SSMS](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)

<!---HONumber=AcomDC_0107_2016-->