### Configuración de una regla de entrada de grupo de seguridad de red para la máquina virtual

Si desea conectarse a SQL Server a través de Internet, debe configurar una regla de entrada en el grupo de seguridad de red del puerto al que escucha su instancia de SQL Server. De manera predeterminada, se trata del puerto TCP 1433.

1. En el portal, seleccione **Máquinas virtuales** y, luego, seleccione su máquina virtual de SQL Server.

2. Haga clic en el vínculo **Toda la configuración**.

3. Expanda **Interfaces de red**.

	![interfaz de red](./media/virtual-machines-sql-server-connection-steps/rm-network-interface.png)

4. Luego, seleccione la interfaz de red para la máquina virtual.

4. Haga clic en el vínculo **Grupo de seguridad de red**.

	![interfaz de red](./media/virtual-machines-sql-server-connection-steps/rm-network-security-group.png)

6. En las propiedades del grupo de seguridad de red, expanda **Reglas de seguridad de entrada**.

5. Haga clic en el botón **Add** (Agregar).

6. Proporcione el **nombre** de "SQLServerPublicTraffic".

7. Cambie el **Protocolo** a **TCP**.

8. Especifique un **Intervalo de puertos de destino** de 1433 (o el puerto en el que escucha su instancia de SQL Server).

9. Compruebe que la opción **Acción** está definida en **Permitir**. El cuadro de diálogo de la regla de seguridad debe verse como la siguiente captura de pantalla.

	![regla de seguridad de red](./media/virtual-machines-sql-server-connection-steps/rm-network-security-rule.png)

9. Haga clic en **Aceptar** para guardar la regla correspondiente a su máquina virtual.

<!---HONumber=AcomDC_0107_2016-->