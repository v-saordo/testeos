
La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1. Vaya a la ubicación donde guardó los archivos comprimidos del proyecto, expándalos en su equipo y abra el archivo de solución en Visual Studio.

2. Presione la tecla **F5** para recopilar el proyecto e iniciar la aplicación.

3. En la aplicación, escriba texto significativo, como *Realice el tutorial*, en **Insertar TodoItem** y, a continuación, haga clic en **Guardar**.

   	Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y se muestran los datos en la segunda columna de la aplicación.

4. (Opcional) En una solución Windows universal, cambie el proyecto de inicio predeterminado a la otra aplicación y vuelva a ejecutar la aplicación.

	Tenga en cuenta que los datos guardados en el paso anterior se cargan del servicio móvil después de que se inicie la aplicación.
 
4. De nuevo en el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en la pestaña **Datos** y luego en la tabla **TodoItems**.

   	Esto le permite examinar los datos que la aplicación inserta en la tabla.

   	![](./media/mobile-services-javascript-backend-run-app/mobile-data-browse.png)

<!----HONumber=AcomDC_1203_2015-->