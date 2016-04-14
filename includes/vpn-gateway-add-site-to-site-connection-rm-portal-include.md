Cuando agregue una conexión de sitio a sitio a la puerta de enlace de red virtual, primero deberá crear una puerta de enlace de red local para hacer referencia a ella desde su configuración. Compruebe que tiene configurada una puerta de enlace de red local. Para buscar las puertas de enlace de red local, utilice **Examinar** y filtre por **Puertas de enlace de red local**.

1. En la hoja **Redes virtuales**, encuentre su red virtual y haga clic para abrir la hoja. En la hoja, la puerta de enlace aparecerá como un *Dispositivo conectado*.
2. Haga clic en el ***nombre de la puerta de enlace de red virtual*** -> **Puerta de enlace de red virtual** -> **Configuración** -> **Conexiones** y, luego, haga clic en **Agregar**.
3. Asígnele un **nombre** a la conexión. En este ejemplo, utilizaremos *GW1S2S*.
4. En **Tipo de conexión**, seleccione **Sitio a sitio (IPsec)**.
5. En **Puerta de enlace de red virtual**, el valor es fijo porque se conecta desde esta puerta de enlace.
6. En **Puerta de enlace de red local**, haga clic en **Elegir una puerta de enlace de red local** y seleccione la puerta de enlace de red local que desea utilizar. En este ejemplo, utilizaremos *GW1LocalNet*.
7. En **Clave compartida**, los valores que aparecen deben coincidir con los que tiene para el dispositivo VPN local. Si el dispositivo VPN de la red local no proporciona una clave compartida, puede inventar una e ingresarla aquí y en el dispositivo local. Lo importante es que ambas coincidan.
8. Los valores restantes para **Suscripción**, **Grupo de recursos** y **Ubicación** son fijos.
9. Haga clic en **Aceptar** para crear la conexión. El mensaje *Creando la conexión* aparecerá de forma intermitente en la pantalla.
10. Una vez que se complete la conexión, aparecerá en la hoja **Conexiones** de su puerta de enlace.

<!---HONumber=AcomDC_0107_2016-->