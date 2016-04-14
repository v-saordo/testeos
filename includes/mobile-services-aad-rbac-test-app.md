
Las instrucciones y capturas de pantalla siguientes se aplican a las pruebas de un cliente de la Tienda Windows, pero puede probar esto en cualquiera de las otras plataformas compatibles con Servicios móviles de Azure.

1. En Visual Studio, ejecute la aplicación de cliente para tratar de autenticar con la cuenta de usuario que creamos con el nombre de David. 

    ![](./media/mobile-services-aad-rbac-test-app/dave-login.png)

2. David no pertenece al grupo de ventas. Por tanto, la comprobación de acceso basada en rol denegará el acceso a las operaciones de tabla. Cierre la aplicación de cliente.

    ![](./media/mobile-services-aad-rbac-test-app/unauthorized.png)

3. En Visual Studio, ejecute de nuevo la aplicación cliente. Esta vez autenticaremos con la cuenta de usuario que creamos con el nombre de Roberto.

    ![](./media/mobile-services-aad-rbac-test-app/bob-login.png)

4. Roberto sí pertenece al grupo de ventas. Por tanto, la comprobación de acceso basada en rol permitirá el acceso a las operaciones de tabla.

    ![](./media/mobile-services-aad-rbac-test-app/success.png)

<!---HONumber=Oct15_HO3-->