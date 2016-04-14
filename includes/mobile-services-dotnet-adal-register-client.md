## <a name="register-app-aad"></a>Registro de la aplicación cliente con Azure Active Directory

1. Vaya a **Active Directory** en el [Portal de Azure clásico](https://manage.windowsazure.com/) y haga clic en el directorio.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-select-aad.png)

2. Haga clic en la pestaña **Aplicaciones** que aparece en la parte superior y, a continuación, haga clic en **AGREGAR** para agregar una aplicación. 

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-aad-applications-tab.png)

3. Haga clic en **Agregar una aplicación que mi organización está desarrollando**.

4. En el Asistente para agregar aplicaciones, escriba el **nombre** de la aplicación y haga clic en el tipo **Aplicación de cliente nativo**. A continuación, haga clic para continuar.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-selection.png)

5. En el cuadro **URI de redirección**, escriba el extremo /login/done de su servicio móvil. Este valor debería ser similar a https://todolist.azure-mobile.net/login/done.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-redirect-uri.png)

6. Haga clic en la pestaña **Configurar** de la aplicación nativa y copie el **Id. de cliente**. Lo necesitará más adelante.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-client-id.png)

7. Desplace hacia abajo la página hasta la sección **Permisos para otras aplicaciones** y haga clic en el botón **Agregar aplicación**. Elija **Otra** en el menú Mostrar y busque todo. Haga clic en **TodoList** para agregar el servicio móvil que ha registrado anteriormente y haga clic en la marca de verificación para finalizar. Conceda acceso a la aplicación de servicio móvil. A continuación, haga clic en **Guardar**.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-add-permissions.png)

El servicio móvil está ahora configurado en AAD para recibir inicios de sesión únicos desde su aplicación.

<!---HONumber=AcomDC_1203_2015-->