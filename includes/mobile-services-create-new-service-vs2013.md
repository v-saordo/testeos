

Los siguientes pasos permiten crear un servicio móvil en Azure y agregar al proyecto un código que conecte su aplicación con este nuevo servicio. Visual Studio 2013 se conecta a Azure en su nombre para crear el nuevo servicio móvil usando las credenciales que usted facilita. Cuando se crea un servicio móvil, es necesario especificar una Base de datos SQL de Azure. Esta base de datos la utilizará el servicio móvil para almacenar los datos de aplicaciones.


1. En Visual Studio 2013, abra el Explorador de soluciones, haga clic con el botón secundario en el proyecto de aplicación de la Tienda Windows, haga clic en **Agregar** y, a continuación, haga clic en **Servicio conectado...**.  

2. En el cuadro de diálogo Administrador de servicios, haga clic en **Crear servicio...** y seleccione **&lt;Administrar...&gt;** en **Suscripción**, en el cuadro de diálogo Crear servicio móvil.

	![crear servicio para administrar suscripciones](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3. En Administrar las suscripciones de Microsoft Azure, haga clic en **Iniciar sesión...** para iniciar sesión en su cuenta de Azure (si es necesario), seleccione una suscripción disponible y haga clic en **Cerrar**.

	Cuando su suscripción ya tiene uno o varios servicios móviles existentes, se muestran los nombres de los servicios.

5. En el cuadro de diálogo anterior, **Crear servicio móvil**, seleccione su **Suscripción**, el back-end de **JavaScript** en **Tiempo de ejecución** y una **Región** para su servicio móvil; después, escriba un **Nombre** para el servicio móvil.

	>[AZURE.NOTE]Los nombres de los servicios móviles deben ser únicos. Cuando el nombre suministrado no se encuentra disponible, una X roja se muestra junto a **Name**.

6. En **Base de datos**, seleccione **&lt;Crear una base de datos SQL gratuita&gt;**, proporcione el **Nombre de usuario del servidor**, la **Contraseña del servidor** y la **Confirmación de la contraseña del servidor** y, a continuación, haga clic en **Crear**.

  	![crear nuevo servicio móvil en VS 2013](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013-2.png)


	> [AZURE.NOTE]Como parte de este tutorial, va a crear una nueva instancia y un nuevo servidor de Base de datos SQL libre. Puede reutilizar esta nueva base de datos y administrarla como lo haría con cualquier otra instancia de Base de datos SQL. Solo puede tener una instancia de base de datos libre. Si ya tiene una base de datos en la misma región que el nuevo servicio móvil, puede elegir en su lugar la base de datos existente. Cuando elija una base de datos existente, asegúrese de suministrar las credenciales de inicio de sesión correctas. En caso contrario, el servicio móvil se creará con un estado incorrecto.

7. Después de crear el servicio móvil, selecciónelo en la lista del Administrador de servicios y haga clic en **Aceptar**.

	Cuando el asistente finaliza, significa que ya están instalados los paquetes de NuGet necesarios, que se ha agregado al proyecto una referencia a la biblioteca cliente de Servicios móviles y que se ha actualizado el código fuente del proyecto.

<!---HONumber=Oct15_HO3-->