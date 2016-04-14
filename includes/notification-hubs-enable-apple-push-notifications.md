

##Generación del archivo de solicitud de firma de certificado

El Servicio de notificaciones push de Apple (APNS) usa certificados para autenticar las notificaciones push. Siga estas instrucciones para crear el certificado de inserción necesario para enviar y recibir notificaciones. Para obtener más información sobre estos conceptos, consulte la documentación oficial del [Servicio de notificaciones push de Apple](http://go.microsoft.com/fwlink/p/?LinkId=272584).

Genere el archivo de solicitud de firma de certificado (CSR) que Apple usa para generar un certificado push firmado.

1. En el Mac, ejecute la herramienta Acceso a llaves. Se puede activar desde la carpeta **Utilidades** o la carpeta **Otros** del panel de inicio.

2. Haga clic en **Keychain Access** (Acceso a llaves), expanda **Certificate Assistant**, (Asistente para certificados) y, a continuación, haga clic en **Request a Certificate from a Certificate Authority...** (Solicitar un certificado de una entidad de certificación...).

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Seleccione una **dirección de correo electrónico de usuario** en User Email Address y un nombre común en **Common Name**, asegúrese de que **Saved to disk** (Guardar en disco) está seleccionado y, a continuación, haga clic en **Continue** (Continuar). Deje el campo **CA Email Address** (Dirección de correo de la entidad de certificación) en blanco, ya que no es obligatorio.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Escriba un nombre para el archivo de solicitud de firma de certificado (CSR) en **Save As** (Guardar como), seleccione la ubicación en **Where** (Dónde) y, a continuación, haga clic en **Save** (Guardar).

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

  	De esta forma, el archivo CSR se guarda en la ubicación seleccionada. La ubicación predeterminada es el escritorio. Recuerde la ubicación seleccionada para este archivo.

A continuación, se registrará la aplicación en Apple, se habilitarán las notificaciones de inserción y se cargará este archivo CSR exportado para crear un certificado de inserción.

##Registro de la aplicación para notificaciones push

Para poder enviar notificaciones push a una aplicación iOS, debe registrar su aplicación con Apple y también registrar las notificaciones push.

1. Si aún no ha registrado su aplicación, diríjase al <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portal de aprovisionamiento de iOS</a> (en inglés) del Centro para desarrolladores de Apple, inicie sesión con su identificador de Apple, haga clic en **Identifiers** (Identificadores) y, a continuación, en **App IDs** (Identificadores de aplicación). Para finalizar, haga clic en el signo **+** para registrar una nueva aplicación.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)


2. Actualice los tres campos siguientes para la nueva aplicación y, a continuación, haga clic en **Continue** (Continuar):

	* **Name** (Nombre): escriba un nombre descriptivo para la aplicación en el campo **Name** (Nombre) de la sección **App ID Description** (Descripción del identificador de la aplicación).
	
	* **Bundle Identifier** (Identificador de paquete): en la sección **Explicit App ID** (Identificador de aplicación explícita), escriba un **identificador de paquete** con el formato `<Organization Identifier>.<Product Name>`, tal como se menciona en la [Guía de distribución de aplicaciones](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). El *identificador de organización* y *nombre del producto* que usa debe coincidir con el identificador de la organización y con el nombre del producto que usará al crear su proyecto XCode. En la captura de pantalla que aparece a continuación, *NotificationHubs* se usa como identificador de una organización y *GetStarted* se usa como el nombre del producto. Asegurarse de que estos valores coincidan con los valores que utilizará en el proyecto XCode le permitirá usar el perfil de publicación correcto con XCode.
	
	* **Push Notifications** (Notificaciones push): Seleccione la opción **Push Notifications** (Notificaciones push) en la sección **App Services** (Servicios de aplicaciones).

	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

   	De esta forma, se genera el identificador de la aplicación y se solicita que confirme la información. Haga clic en **Submit** (Enviar).


    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-confirm-new-appid.png)


   	Una vez que haga clic en **Submit** (Enviar), verá la pantalla **Registration complete** (Registro completado), como se muestra a continuación. Haga clic en **Done** (Listo).


    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


3. En el Centro para desarrolladores, en Id. de aplicación, busque el identificador de la aplicación que acaba de crear y haga clic en su fila.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

   	Al hacer clic en el identificador de la aplicación se mostrarán los detalles de la misma. Haga clic en el botón **Edit** (Editar) en la parte inferior.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

4. Desplácese a la parte inferior de la pantalla y haga clic en el botón **Create Certificate...** (Crear certificado...) en la sección **Development Push SSL Certificate** (Certificado SSL de inserción de desarrollo).

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

   	Se mostrará el asistente "Add iOS Certificate".

    > [AZURE.NOTE]Este tutorial usa un certificado de desarrollo. Se usa el mismo proceso cuando se registra un certificado de producción. Asegúrese de usa el mismo tipo de certificado cuando envíe notificaciones.

5. Haga clic en **Choose File** (Elegir archivo), diríjase a la ubicación en la que guardó el archivo CSR que creó en la primera tarea y, a continuación, haga clic en **Generate** (Generar).

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

6. Una vez que el portal haya creado el certificado, haga clic en el botón **Download** (Descargar) y en **Done** (Listo).

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

   	De esta forma, se descarga el certificado y se guarda en su equipo en la carpeta de descargas.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE]de manera predeterminada, el certificado de desarrollo del archivo descargado se llama **aps\_development.cer**.

7. Haga doble clic en el certificado de inserción **aps\_development.cer** descargado.

   	De esta forma, se instala un nuevo certificado en las llaves, como se muestra a continuación:

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE]El nombre del certificado puede ser diferente, pero tendrá el prefijo de **Apple Development iOS Push Services (Servicios inserción de desarrollo de Apple iOS):**.

8. En Acceso a llaves, haga clic en el nuevo certificado de inserción que creó, en la categoría **Certificados**. Haga clic en **Exportar**, asigne un nombre al archivo, seleccione el formato **.p12** y, luego, haga clic en **Guardar**.

	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

	Anote el nombre de archivo y la ubicación del certificado p12 exportado. Se usará para habilitar la autenticación con APNS.

	>[AZURE.NOTE]Con este tutorial se crea un archivo QuickStart.p12. El nombre del archivo y la ubicación pueden ser diferentes.


##Creación de un perfil de aprovisionamiento para la aplicación

1. Vuelva al <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de aprovisionamiento de iOS</a>, seleccione **Provisioning Profiles** (Perfiles de aprovisionamiento), seleccione **All** (Todo) y, a continuación, haga clic en el botón **+** para crear un nuevo perfil. De esta forma, se iniciará el asistente para **Agregar perfil de aprovisionamiento de iOS**.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Seleccione **Desarrollo de aplicaciones de iOS** en **Desarrollo** como tipo de perfil de aprovisionamiento y haga clic en **Continuar**.


3. A continuación, seleccione el identificador de aplicación que acaba de crear desde la lista desplegable **Identificador de aplicación** y, a continuación, haga clic en **Continuar**

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. En la pantalla **Seleccionar certificados**, seleccione el certificado de desarrollo usual que se usó para firma de código y, a continuación, haga clic en **Continuar**. Este no es el certificado de inserción que acaba de crear.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. A continuación, seleccione en **Devices** los dispositivos que usará para la prueba y haga clic en **Continue** (Continuar).

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. Para terminar, seleccione un nombre para el perfil en **Profile Name** (Nombre de perfil) y haga clic en **Generate** (Generar).

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)


7. Cuando se crea el nuevo perfil de aprovisionamiento, haga clic aquí para descargarlo e instalarlo en el equipo de desarrollo de Xcode. A continuación, haga clic en **Hecho**.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

<!---HONumber=AcomDC_1203_2015-->