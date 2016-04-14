1. En el portal, vaya a **Nuevo** y seleccione **Examinar**. Seleccione **Puertas de enlace de red virtual** de la lista.
2. Haga clic en **Agregar**.
3. Asigne un nombre a la puerta de enlace. Esta acción no es igual a la de asignación de un nombre a una subred de puerta de enlace. Este es el nombre del objeto de puerta de enlace. 
4. En **Red virtual**, seleccione la red virtual que desea conectar a esta puerta de enlace.
5. En la configuración de la red virtual, para el valor **Dirección IP pública**, cree un nombre de la dirección IP pública. Tenga en cuenta que esto no pide una dirección IP. La dirección IP se asignará dinámicamente. En cambio, este es el nombre del objeto de dirección IP a la que se asignará la dirección. 
6. Para **Tipo VPN**, las opciones se basan en directivas y se basan en rutas. Asegúrese de seleccionar el tipo de puerta de enlace de VPN que sea compatible con el escenario de configuración y, si fuera necesario para la configuración, compatible con el dispositivo de puerta de enlace de VPN que va a usar.
7. Para **Grupo de recursos**, elija **Seleccionar existente** y elija el grupo de recursos en el que reside la red virtual, a menos que la configuración requiera una selección diferente.
8. Para **Ubicación**, asegúrese de que muestra la ubicación del grupo de recursos y la red virtual.
9. Haga clic en **Crear**. Verá el icono *Implementación de la puerta de enlace de red virtual* en el panel. La creación de una puerta de enlace tarda algo de tiempo. Hay muchos procesos que se ejecutan en segundo plano. Puede esperar que tarde 15 minutos o más. Es posible que tenga que actualizar la página de portal para ver el estado completado.
10. Una vez creada la puerta de enlace, puede ver la dirección IP que se le ha asignado consultando la red virtual en el portal. La puerta de enlace aparecerá como un dispositivo conectado. Puede ver el nombre y la dirección IP asignada a la puerta de enlace.

<!---HONumber=AcomDC_0114_2016-->