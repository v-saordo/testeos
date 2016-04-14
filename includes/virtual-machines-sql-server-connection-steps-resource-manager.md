### Configuración de una etiqueta DNS para la dirección IP pública

Para conectarse al motor de base de datos de SQL Server desde Internet, primero debe configurar una etiqueta DNS para la dirección IP pública. Tenga en cuenta que este paso no es necesario si planea conectarse únicamente a la instancia de SQL Server dentro de la misma red virtual o solo de forma local.

Para crear una etiqueta DNS, primero seleccione **Máquinas virtuales** en el portal. Seleccione su máquina virtual de SQL Server para que aparezcan sus propiedades.

1. En la hoja de la máquina virtual, seleccione la **Dirección IP pública.**

	![dirección ip pública](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)

2. En las propiedades de la dirección IP pública, expanda **Configuración**.

3. Escriba un nombre para la etiqueta DNS. Este es un registro D que se puede usar para conectarse a la máquina virtual de SQL Server por nombre en lugar de hacerlo directamente por dirección IP.

	![etiqueta dns](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### Conexión al motor de base de datos desde otro equipo
 
1. En otro equipo que esté conectado a Internet, abra SQL Server Management Studio (SSMS).

2. En el cuadro de diálogo **Conectar con el servidor** o **Conectarse al motor de base de datos**, en el cuadro **Nombre del servidor** escriba el nombre DNS completo de la máquina virtual (determinado en la tarea anterior).

3. En el cuadro **Autenticación**, seleccione **Autenticación de SQL Server**.

5. En el cuadro **Inicio de sesión**, escriba un inicio de sesión SQL válido.

6. En el cuadro **Contraseña**, escriba la contraseña del inicio de sesión.

7. Haga clic en **Conectar**.

	![conexión ssms](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)

<!---HONumber=AcomDC_0107_2016-->