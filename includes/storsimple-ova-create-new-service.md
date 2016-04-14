#### Para crear un nuevo servicio

1.  Con sus credenciales de la cuenta Microsoft, inicie sesión en el Portal de Azure clásico en esta dirección URL: [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

2.  En el portal, haga clic en **Nuevo > Servicios de datos > StorSimple Manager > Creación rápida**.

3.  En el formulario que aparece, haga lo siguiente:

	1.  Proporcione un **Nombre** único para el servicio. Se trata de un nombre descriptivo que sirve para identificar el servicio. El nombre puede tener entre 2 y 50 caracteres que pueden ser letras, números y guiones. El nombre debe empezar y terminar con una letra o un número.

	2.  Para que un servicio administre un dispositivo virtual de StorSimple, en la lista desplegable de **Tipo de dispositivos administrados**, elija **Serie de dispositivos virtuales**.

	3.  Proporcione una **Ubicación** para el servicio. La ubicación hace referencia a la región geográfica donde desea implementar el dispositivo.

	 -   Si tiene otras cargas de trabajo en Azure que se van a implementar con el dispositivo StorSimple, se recomienda usar ese centro de datos.

   	 -   El StorSimple Manager y el almacenamiento de Azure pueden estar en dos ubicaciones independientes. En este caso, se le pedirá que cree la cuenta del Administrador de StorSimple y la del Almacenamiento de Azure por separado. Para crear una cuenta de almacenamiento de Azure, vaya al servicio Almacenamiento de Azure en el portal y siga los pasos indicados en la sección [Crear una cuenta de Almacenamiento de Azure](storage-create-storage-account.md#create-a-storage-account). Después de crear esta cuenta, agréguela al servicio de StorSimple Manager siguiendo los pasos indicados en la sección [Configurar una nueva cuenta de almacenamiento para el servicio](#optional-step-configure-a-new-storage-account-for-the-service).
   	 
   	 	
	1.  Elija una **Suscripción** en la lista desplegable. La suscripción está vinculada a la cuenta de facturación. Este campo no está presente cuando tiene una sola suscripción.

	1.  Seleccione **Crear una nueva cuenta de Almacenamiento de Azure** para crear de manera automática una cuenta de almacenamiento con el servicio. Esta cuenta de almacenamiento tendrá un nombre especial, como "storsimplebwv8c6dcnf." Si necesita tener sus datos en una ubicación diferente, desactive esta casilla.

	1.  Haga clic en **Crear Administrador de StorSimple** para crear el servicio.

		![](./media/storsimple-ova-create-new-service/image1-include.png)

	Se le dirigirá a la página de aterrizaje del **Servicio**. La creación del servicio tardará unos minutos. Recibirá una notificación apropiada cuando el servicio se cree correctamente.

	![](./media/storsimple-ova-create-new-service/image2-include.png)

	El estado del servicio cambiará a **Activo**.

<!---HONumber=AcomDC_0302_2016-->