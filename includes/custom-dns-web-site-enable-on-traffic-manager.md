Después de que los registros de su nombre de dominio se han propagado, debería poder utilizar el explorador para comprobar que el nombre de dominio personalizado se puede utilizar para tener acceso a la aplicación web de Servicios de aplicaciones de API.

> [AZURE.NOTE]El CNAME puede tardar un tiempo en propagarse por el sistema DNS. Puede usar un servicio como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para comprobar que el CNAME está disponible.

Si aún no lo ha agregado a la aplicación web como extremo del Administrador de tráfico, debe hacerlo para que funcione la resolución de nombres, debido a que el nombre de dominio personalizado se enruta a Administrador de tráfico. Posteriormente, el Administrador de tráfico enruta a la aplicación web. Use la información de [Adición o eliminación de extremos](../traffic-manager/traffic-manager-endpoints.md) para agregar su aplicación web como un extremo en el perfil del Administrador de tráfico.

> [AZURE.NOTE]Si s aplicación web no aparece en la lista al agregar un extremo, compruebe que está configurada para el modo de plan **estándar** del Servicio de aplicaciones. Si desea trabajar con el Administrador de tráfico, debe utilizar el modo **estándar** para su aplicación web.

1. En el explorador, abra el [Portal de Azure](https://portal.azure.com).

2. En la pestaña **Aplicaciones web**, haga clic en el nombre de la aplicación web, seleccione **Configuración** y luego seleccione **Dominios personalizados y SSL**.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. En la hoja **Dominios personalizados y SSL**, haga clic en **Traer dominios externos**.

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4. Utilice los cuadros de texto **NOMBRES DE DOMINIO** para escribir el nombre de dominio de Administrador de tráfico (contoso.trafficmanager.net) para realizar la asociación con esta aplicación web.

	![](./media/custom-dns-web-site/dncmntask-cname-8.png)

5. Haga clic en **Guardar** para guardar la configuración de nombre de dominio.

	Una vez completada la configuración, el nombre de dominio personalizado aparecerá en la sección **nombres de dominio** de la aplicación web.

En este punto, debería poder escribir el nombre de dominio de Administrador de tráfico (contoso.trafficmanager.net) en el explorador y ver que le lleva sin problemas a la aplicación web.

<!---HONumber=Oct15_HO3-->