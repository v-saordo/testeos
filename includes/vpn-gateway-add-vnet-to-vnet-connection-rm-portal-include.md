Cuando agregue una conexión de red virtual a red virtual, compruebe que ambas redes virtuales tienen una puerta de enlace de red virtual y que no tienen ningún intervalo de direcciones superpuesto.

1. En la hoja **Redes virtuales**, encuentre su red virtual y haga clic para abrir la hoja. En la hoja, la puerta de enlace aparecerá como un *Dispositivo conectado*. También puede configurar los ajustes directamente desde la puerta de enlace de red virtual sin que sea necesario expandir la red virtual.
2. En la configuración de la puerta de enlace de red virtual, haga clic en **Conexiones** y, luego, **Agregar**.
3. Asígnele un **nombre** a la conexión. 
4. En **Tipo de conexión**, seleccione **Red virtual a red virtual**.
5. En **Puerta de enlace de red virtual**, el valor es fijo porque se conecta desde esta puerta de enlace.
6. En **Segunda puerta de enlace de red virtual**, seleccione la puerta de enlace a la que desea crear una conexión desde esta puerta de enlace.
8. Los valores restantes para **Suscripción**, **Grupo de recursos** y **Ubicación** son fijos.
9. Haga clic en **Aceptar** para crear la conexión. El mensaje *Creando la conexión* aparecerá de forma intermitente en la pantalla.
10. Una vez que se complete la conexión, aparecerá en la hoja **Conexiones** de su puerta de enlace.

<!---HONumber=AcomDC_0107_2016-->