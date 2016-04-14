1. Desplázate atrás al Administrador de clústeres de conmutación por error. Amplia las **Funciones** y después resalta tu grupo de disponibilidad. En la pestaña de **Recursos**, haz clic con el botón derecho en el nombre del agente de escucha y haz clic en Propiedades.

1. Haz clic en la pestaña **Dependencias**. Si aparecen varios recursos, comprueba que las direcciones IP tienen dependencias OR, y no AND. Haga clic en **Aceptar**.

1. Haz clic en el nombre del agente de escucha y luego haz clic en **Poner en línea**.

1. Una vez que el agente de escucha está en línea, en la pestaña de **Recursos**, haz clic con el botón derecho en el grupo de disponibilidad y haz clic en **Propiedades**.

	![Configurar el recurso de grupo de disponibilidad](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

1. Crea una dependencia en el recurso del nombre del agente de escucha (no en el nombre de los recursos de dirección IP). Haga clic en **Aceptar**.

	![Agregar dependencias en el nombre del agente de escucha](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

1. Abra **SQL Server Management Studio** y conéctese a la réplica principal.

1. Vaya a **Alta disponibilidad AlwaysOn** | **Grupos de disponibilidad** | **<AvailabilityGroupName>** | **Agentes de escucha del grupo de disponibilidad**.

3. Ahora tienes que ver el nombre del agente de escucha que creaste en el Administrador de clústeres de conmutación por error. Haga clic con el botón derecho en el nombre del agente de escucha y luego en **Propiedades**.

1. En la casilla **Puerto**, especifique el número del puerto del agente de escucha del grupo de disponibilidad mediante el $EndpointPort usado anteriormente (en este tutorial, 1433 era el valor predeterminado) y después haga clic en **Aceptar**.

<!---HONumber=Oct15_HO3-->