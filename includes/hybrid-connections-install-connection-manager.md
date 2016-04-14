
El Administrador de conexiones híbridas permite que su máquina local se conecte a Azure y retransmita el tráfico TCP. Debe instalar el administrador en un equipo local que se pueda conectar a la instancia de SQL Server.

1. La conexión que acaba de crear debería tener un **Estado** **Instalación local incompleta**. Haga clic en esta conexión y haga clic en **Instalación local**.

	![On-Premises Setup](./media/hybrid-connections-install-connection-manager/5-1.png)

2. Haga clic en **Instalar y configurar**.

	De esta forma, se instala una instancia personalizada del Administrador de conexiones, que ya está configurado para funcionar con la conexión híbrida que acaba de crear.

3. Realice el resto de los pasos de configuración del Administrador de conexiones.

	Cuando finalice la instalación, el estado de la conexión híbrida cambia a **1 instancia conectada**. Puede que tenga que actualizar el explorador y esperar unos minutos.

La instalación de la conexión híbrida ha terminado.

<!---HONumber=Oct15_HO3-->