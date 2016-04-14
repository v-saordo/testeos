1. Haga clic en la pestaña **Aplicaciones** en la página del directorio del [Portal de Azure clásico](https://manage.windowsazure.com/).
  
2. Haga clic en el registro de la aplicación integrado.

3. Haga clic en **Configurar** en la página de la aplicación y desplácese hacia la sección **claves** de la página.
4. Haga clic en la duración **1 año** para una nueva clave. A continuación, haga clic en **Guardar** y el portal mostrará el nuevo valor de la clave.
5. Copie los valores de **Id. de cliente** y **Clave** mostrados después de guardar. Tenga en cuenta que el valor de la clave solo se mostrará una única vez después de que lo haya guardado. 

    ![](./media/mobile-services-generate-aad-app-registration-access-key/client-id-and-key.png)

6. Desplácese hacia la parte inferior de la página de configuración de la aplicación integrada y habilite el permiso **Leer datos de directorio** para la aplicación y haga clic en **Guardar**.

    ![](./media/mobile-services-generate-aad-app-registration-access-key/app-perms.png)


7. En el [Portal de Azure clásico](https://manage.windowsazure.com/), vuelva a su servicio móvil y haga clic en la pestaña **Configurar**. Desplácese a la sección de **configuración de la aplicación** y agregue las siguientes configuraciones y haga clic en **Guardar**.

    <table border="1"> <tr> <th>Nombre de la configuración de la aplicación</th><th>Descripción</th> </tr> <tr> <td>AAD\_CLIENT\_ID</td><td>El identificador del cliente que ha copiado de la aplicación integrada en los pasos anteriores.</td> </tr> <tr> <td>AAD\_CLIENT\_KEY</td><td>La clave de la aplicación generada en la aplicación integrada AAD en los pasos anteriores.</td> </tr> <tr> <td>AAD\_TENANT\_DOMAIN</td><td>Nombre de su dominio de AAD. Debe ser similar a "midominio.onmicrosoft.com"</td> </tr> </table><br/>

 
    ![](./media/mobile-services-generate-aad-app-registration-access-key/aad-app-settings.png)
  

<!---HONumber=AcomDC_1203_2015-->