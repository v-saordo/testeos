Siga estos pasos para crear una puerta de enlace de red local:

1. Utilice **Examinar** y filtre para encontrar la hoja **Puertas de enlace de red local** y, luego, haga clic en **Agregar**.
2. En la hoja **Crear puertas de enlace de red local**, asígnele un **nombre** al objeto de puerta de enlace de red local. En este ejemplo, le asignaremos el nombre *GW1LocalNet* a la puerta de enlace de red local.
3. Configure una **dirección IP** para la puerta de enlace. Esta es la dirección IP del dispositivo VPN externo al que desea conectarse. No puede encontrarse detrás de NAT y debe estar al alcance de Azure. Esta es la dirección IP del dispositivo al que se conectará la puerta de enlace de Azure.
4. El **espacio de direcciones** se refiere a los intervalos de direcciones de la red local. Puede agregar varios intervalos de espacios de direcciones. Los intervalos que ingrese aquí no se pueden superponer a ninguno de los intervalos de espacios de direcciones que utiliza para las redes virtuales que se comunicarán a través de la puerta de enlace. Deberá coordinar con la configuración local, además de los espacios de direcciones de la red virtual de Azure. 
5. En **Suscripción**, compruebe que se muestra la suscripción correcta.
6. En **Grupo de recursos**, seleccione el grupo de recursos que desea utilizar. Puede crear un grupo de recursos nuevo o seleccionar uno ya creado. Para crear un grupo de recursos, escriba el nombre en el cuadro. Si desea seleccionar un grupo de recursos ya creado, haga clic en **Grupo de recursos** para abrir la hoja **Grupo de recursos** y, luego, seleccione el grupo de recursos que desea utilizar.
7. En **Ubicación**, compruebe que la ubicación es la misma que la puerta de enlace de red virtual con que la asociará.
8. Deje seleccionada la opción "Anclar al panel" si desea acceder fácilmente a esta puerta de enlace de red local desde el panel.
9. Haga clic en **Crear** para crear la puerta de enlace de red local. En el panel, verá "Implementación de la puerta de enlace de red local". Este proceso de creación no debería demorar mucho.

<!---HONumber=AcomDC_0107_2016-->