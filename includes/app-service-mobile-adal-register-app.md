1. Registre el back-end de su aplicación móvil con su inquilino de Azure Active Directory siguiendo el tema [Configuración de aplicaciones móviles con Azure Active Directory].

2. Vaya a **Active Directory** en el [Portal de Azure clásico]

   ![](./media/app-service-mobile-adal-register-app/app-service-navigate-aad.png)

3. Seleccione el directorio y, a continuación, seleccione la pestaña **Aplicaciones** en la parte superior. Haga clic en **AGREGAR** en la parte inferior para crear un nuevo registro de aplicación. 

4. Haga clic en **Agregar una aplicación que mi organización está desarrollando**.

5. En el Asistente para agregar aplicaciones, escriba el **nombre** de la aplicación y haga clic en el tipo **Aplicación de cliente nativo**. A continuación, haga clic para continuar.

6. En el cuadro **URI de redirección**, especifique el extremo /login/done de la puerta de enlace del Servicio de aplicaciones. Este valor debería ser similar a https://contoso.azurewebsites.net/login/done.

7. Una vez que haya agregado la aplicación nativa, haga clic en la pestaña **Configurar**. Copie el **Id. de cliente**. Lo necesitará más adelante.

8. Desplácese hacia abajo por la página hasta la sección **Permisos para otras aplicaciones** y haga clic en **Agregar aplicación**.

9. Busque la aplicación web que registró y haga clic en el icono del signo de suma. A continuación, haga clic en la marca de verificación para cerrar el cuadro de diálogo.

10. En la entrada nueva que acaba de agregar, abra la lista desplegable **Permisos delegados** y seleccione **Acceso (nombreaplic)**. A continuación, haga clic en **Guardar**.

   ![](./media/app-service-mobile-adal-register-app/aad-native-client-add-permissions.png)

La aplicación ya está configurada en AAD para que los usuarios puedan iniciar sesión con el inicio de sesión de AAD.

[Portal de Azure clásico]: https://manage.windowsazure.com/
[Configuración de aplicaciones móviles con Azure Active Directory]: ../articles/app-service-how-to-configure-active-directory-authentication.md

<!---HONumber=AcomDC_1203_2015-->