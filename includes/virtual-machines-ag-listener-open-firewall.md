En este paso, crearás una regla de firewall para abrir el puerto de sondeo para el extremo de carga equilibrada (59999, como se especificó anteriormente) y otra regla para abrir el puerto de escucha del grupo de disponibilidad. Como creaste el extremo de carga equilibrada en las máquinas virtuales de Azure que contienen réplicas del grupo de disponibilidad, tendrás que abrir el puerto de sondeo y el puerto de escucha en las respectivas máquinas virtuales de Azure.

1. Inicia el **Firewall de Windows con seguridad avanzada** en las máquinas virtuales que alojan réplicas.

1. Haz clic con el botón derecho en **Reglas de entrada** y después en **Nueva regla**.

1. En la página **Tipo de regla**, selecciona **Puerto** y después haz clic en **Siguiente**.

1. En la página **Protocolo y puertos**, selecciona **TCP** y escribe **59999** en la casilla **Puertos locales específicos**. A continuación, haga clic en **Siguiente**.

1. En la página **Acción**, mantén seleccionado **Permitir la conexión** y haz clic en **Siguiente**.

1. En la página **Perfiles**, acepta la configuración predeterminada y haz clic en **Siguiente**.

1. En la página **Nombre**, especifica un nombre de regla, como el **Puerto de sondeo de escucha AlwaysOn** del cuadro de texto **Nombre** y haz clic en **Finalizar**.

1. Repite los pasos anteriores para el puerto de escucha del grupo de disponibilidad (como se especificó anteriormente en el parámetro $EndpointPort del script) y especifica un nombre de regla adecuado, como **Puerto de escucha AlwaysOn**.

<!--------HONumber=Oct15_HO3-->