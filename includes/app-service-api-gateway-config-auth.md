4. Vaya a la hoja de puerta de enlace de la aplicación de API.

	![Hacer clic en la puerta de enlace](./media/app-service-api-gateway-config-auth/gateway.png)

7. En la hoja **Puerta de enlace**, haga clic en **Configuración** y, después, en **Identidad**.

	![Hacer clic en Configuración](./media/app-service-api-gateway-config-auth/clicksettingsingateway.png)

	![Hacer clic en Identidad](./media/app-service-api-gateway-config-auth/clickidentity.png)

	Desde la hoja **Identidad** puede navegar a las distintas hojas para configurar la autenticación mediante Azure Active Directory y otros proveedores.

	![Hoja Identidad](./media/app-service-api-gateway-config-auth/identityblade.png)
  
3. Elija el proveedor de identidades que desea utilizar y siga los pasos descritos en el artículo correspondiente para configurar la aplicación de API con ese proveedor. Estos artículos se han escrito para aplicaciones móviles, pero los procedimientos son los mismos para las Aplicaciones de API. Algunos de los procedimientos requieren que se use el [Portal de Azure].

 - [Cuenta Microsoft](../articles/app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md)
 - [Inicio de sesión en Facebook](../articles/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md)
 - [Inicio de sesión en Twitter](../articles/app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md)
 - [Inicio de sesión en Google](../articles/app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md)
 - [Azure Active Directory](../articles/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)

Por ejemplo, las siguientes capturas de pantalla muestran lo que debe ver en las páginas del [Portal de Azure] y en las hojas del [Portal de vista previa de Azure] una vez configurada la autenticación de Azure Active Directory.

En el portal de vista previa de Azure, la hoja **Azure Active Directory** tiene el **Id. de cliente** de la aplicación que ha creado en la pestaña Azure Active Directory del portal de Azure e **Inquilinos permitidos** tiene el inquilino de Azure Active Directory (por ejemplo, "contoso.onmicrosoft.com").

![Hoja Azure Active Directory](./media/app-service-api-gateway-config-auth/tdinaadblade.png)

En el portal de Azure, la pestaña **Configurar** de la aplicación que ha creado en la pestaña **Azure Active Directory** tiene las opciones **URL de inicio de sesión**, **URI de id. de aplicación** y **URL de respuesta** de la hoja **Azure Active Directory** en el Portal de vista previa de Azure.

![](./media/app-service-api-gateway-config-auth/oldportal1.png)

![](./media/app-service-api-gateway-config-auth/oldportal2.png)

![](./media/app-service-api-gateway-config-auth/oldportal3.png)

![](./media/app-service-api-gateway-config-auth/oldportal4.png)

(La URL de respuesta de la imagen muestra la misma dirección URL dos veces, una vez con `http:` y otra con `https:`.)

<!---HONumber=Nov15_HO1-->