
Este tutorial se basa en la [aplicación GetStartedWithMobileServices](http://go.microsoft.com/fwlink/p/?LinkID=510826), que es un proyecto de aplicación universal de Windows en Visual Studio 2013. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de Servicios móviles, con la excepción de que los elementos agregados se almacenan localmente en la memoria.

1. Descargue la versión JavaScript de la aplicación de ejemplo GetStartedWithMobileServices desde el [sitio de ejemplos de código para desarrolladores]. 

3. En Visual Studio 2013, abra el proyecto descargado, expanda la carpeta js en el Explorador de soluciones y examine el archivo default.js.

   	Los objetos **TodoItem** agregados se almacenan en un `WinJS.Binding.List` en memoria.

4. Presione la tecla **F5** para recopilar el proyecto e iniciar la aplicación.

5. En la aplicación, escriba algo de texto en **Insertar TodoItem** y, a continuación, haga clic en **Guardar**.

   	![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup.png)

   	Se muestra el texto guardado.

6. Haga clic con el botón secundario en el proyecto de Windows Phone 8.1, haga clic en **Establecer como proyecto de inicio** y presione **F5** para iniciar la aplicación de la Tienda de Windows Phone.

	![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup-wp8.png)

7. Repita los pasos 3 y 4 para comprobar que la muestra se comporta del mismo modo.

<!---HONumber=Oct15_HO3-->