
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Servicios móviles**, haga clic en el servicio móvil y haga clic en la pestaña **Panel**.

2. (Opcional) Si ya ha establecido las credenciales de control de código fuente de Sitios web o Servicios móviles para la suscripción a Azure, puede pasar directamente al paso 4. De lo contrario, haga clic en **Configurar control de código fuente** en **Vista rápida** y haga clic en **Sí** para confirmar.

	![Configurar el control de código fuente](./media/mobile-services-enable-source-control/mobile-setup-source-control.png)

3. Proporcione un valor para **Nombre de usuario**, **Contraseña nueva**, confirme la contraseña y haga clic en el botón de comprobación.

	Se crea el repositorio Git en el servicio móvil. Tome nota de las credenciales que acaba de proporcionar, ya que se usarán para obtener acceso a este y a otros repositorios de Servicios móviles en su suscripción.

4. Haga clic en la pestaña **Configurar** y observe los nuevos campos que aparecen bajo **Control de código fuente**.

	![Configurar el control de código fuente](./media/mobile-services-enable-source-control/mobile-source-control-configure.png)

	Se muestra la dirección URL del repositorio Git, que se usará para clonar dicho repositorio en el equipo local.

Con el control de código fuente habilitado en el servicio móvil, puede usar Git para clonar el repositorio en el equipo local.
 

<!---HONumber=AcomDC_1203_2015-->