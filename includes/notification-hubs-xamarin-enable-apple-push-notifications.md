

Para registrar la aplicación para notificaciones push mediante el Servicio de notificaciones push de Apple (APNS), debe crear un nuevo certificado de inserción, el identificador de la aplicación y el perfil de aprovisionamiento para el proyecto en el portal para desarrolladores de Apple. El identificador de la aplicación contendrá los valores de configuración que permiten a la aplicación enviar y recibir notificaciones push. Estas opciones incluirán el certificado de notificaciones push necesario para autenticar con el Servicio de notificaciones push de Apple (APN) al enviar y recibir notificaciones push. Para obtener más información sobre estos conceptos, consulte la documentación oficial del [Servicio de notificaciones push de Apple](http://go.microsoft.com/fwlink/p/?LinkId=272584).


####Generación de un archivo de solicitud de firma de certificado para el certificado de inserción

Estos pasos le guiarán por la creación de la solicitud de firma de certificado. Se usará para generar un certificado de inserción para usarse con APNS.

1. En el Mac, ejecute la herramienta Acceso a llaves. Se puede activar desde la carpeta **Utilidades** o la carpeta **Otros** del panel de inicio.

2. Haga clic en **Keychain Access** (Acceso a llaves), expanda **Certificate Assistant**, (Asistente para certificados) y, a continuación, haga clic en **Request a Certificate from a Certificate Authority...** (Solicitar un certificado de una entidad de certificación...).

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Seleccione una **dirección de correo electrónico de usuario** en User Email Address y un nombre común en **Common Name**, asegúrese de que **Saved to disk** (Guardar en disco) está seleccionado y, a continuación, haga clic en **Continue** (Continuar). Deje el campo **CA Email Address** (Dirección de correo de la entidad de certificación) en blanco, ya que no es obligatorio.

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Escriba un nombre para el archivo de solicitud de firma de certificado (CSR) en **Save As** (Guardar como), seleccione la ubicación en **Where** (Dónde) y, a continuación, haga clic en **Save** (Guardar).

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

  	De esta forma, el archivo CSR se guarda en la ubicación seleccionada. La ubicación predeterminada es el escritorio. Recuerde la ubicación seleccionada para este archivo.


####Registro de la aplicación para notificaciones push

Cree un nuevo identificador de aplicación explícita para la aplicación con Apple y se configúrelo también para notificaciones push.

1. Navegue al [portal de aprovisionamiento de iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) (en inglés) del Centro para desarrolladores de Apple, inicie sesión con su identificador de Apple, haga clic en **Identifiers** (Identificadores) y, a continuación, en **App IDs** (Identificadores de aplicación). Para finalizar, haga clic en el signo **+** para registrar una nueva aplicación.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Actualice los tres campos siguientes para la nueva aplicación y, a continuación, haga clic en **Continue** (Continuar):

	* **Name** (Nombre): escriba un nombre descriptivo para la aplicación en el campo **Name** (Nombre) de la sección **App ID Description** (Descripción del identificador de la aplicación).
	
	* **Bundle Identifier** (Identificador de paquete): en la sección **Explicit App ID** (Identificador de aplicación explícita), escriba un **identificador de paquete** con el formato `<Organization Identifier>.<Product Name>`, tal como se menciona en la [Guía de distribución de aplicaciones](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Debe coincidir con lo que también se usa en el proyecto XCode o Xamarin para la aplicación.
	 
	* **Push Notifications** (Notificaciones push): seleccione la opción **Push Notifications** (Notificaciones push) en la sección **App Services** (Servicios de aplicaciones).

	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

3.	En la pantalla Confirm your App ID (Confirmar identificador de aplicación), revise la configuración y una vez que la haya comprobado, haga clic en **Submit** (Enviar).

4. 	Una vez que haya enviado la nueva identificación de aplicación, verá la pantalla **Registration complete** (Registro completado). Haga clic en **Done** (Listo).

5. En el Centro para desarrolladores, en Id. de aplicación, busque el identificador de la aplicación que acaba de crear y haga clic en su fila. Al hacer clic en la fila del identificador de la aplicación se mostrarán los detalles de la misma. Haga clic en el botón **Edit** (Editar) en la parte inferior.

6. Desplácese a la parte inferior de la pantalla y haga clic en el botón **Create Certificate...** (Crear certificado...) en la sección **Development Push SSL Certificate** (Certificado SSL de inserción de desarrollo).

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

   	Se mostrará el asistente "Add iOS Certificate" (Añadir certificado iOS).

    > [AZURE.NOTE]Este tutorial usa un certificado de desarrollo. Se usa el mismo proceso cuando se registra un certificado de producción. Asegúrese de usa el mismo tipo de certificado cuando envíe notificaciones.

7. Haga clic en **Choose File** (Elegir archivo), vaya a la ubicación donde guardó el CSR para el certificado de inserción. A continuación, haga clic en **Generate** (Generar).

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

8. Una vez que el portal haya creado el certificado, haga clic en el botón **Download** (Descargar).

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

   	De esta forma, se descarga el certificado de firma y se guarda en su equipo en la carpeta de descargas.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE]de manera predeterminada, el certificado de desarrollo del archivo descargado se llama **aps\_development.cer**.

9. Haga doble clic en el certificado de inserción **aps\_development.cer** descargado. De esta forma, se instala un nuevo certificado en las llaves, como se muestra a continuación:

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE]El nombre del certificado puede ser diferente, pero tendrá el prefijo de **Apple Development iOS Push Services (Servicios inserción de desarrollo de Apple iOS):**.

10. En Acceso a llaves, haga clic con el botón derecho en el nuevo certificado de inserción que acaba de crear, en la categoría **Certificados**. Haga clic en **Exportar**, asigne un nombre al archivo, seleccione el formato **.p12** y, luego, haga clic en **Guardar**.

	Recuerde el nombre de archivo y la ubicación del certificado de inserción p12 exportado. Se usará para habilitar la autenticación con APNS mediante su carga en el Portal de Azure clásico.



####Creación de un perfil de aprovisionamiento para la aplicación

1. Vuelva al <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de aprovisionamiento de iOS</a>, seleccione **Provisioning Profiles** (Perfiles de aprovisionamiento), seleccione **All** (Todo) y, a continuación, haga clic en el botón **+** para crear un nuevo perfil. De esta forma, se iniciará el asistente para **Agregar perfil de aprovisionamiento de iOS**.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Seleccione **Desarrollo de aplicaciones de iOS** en **Desarrollo** como tipo de perfil de aprovisionamiento y haga clic en **Continuar**.


3. A continuación, seleccione el identificador de aplicación que acaba de crear desde la lista desplegable **Identificador de aplicación** y, a continuación, haga clic en **Continuar**

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. En la pantalla **Seleccionar certificados**, seleccione el certificado de desarrollo que se usó para firma de código y, a continuación, haga clic en **Continuar**. Este no es el certificado de firma ni de inserción que acaba de crear.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. A continuación, seleccione en **Devices** los dispositivos que usará para la prueba y haga clic en **Continue** (Continuar).

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. Para terminar, seleccione un nombre para el perfil en **Nombre de perfil** y haga clic en **Generar**.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

<!---HONumber=AcomDC_1210_2015-->