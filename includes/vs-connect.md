
   * Inicie sesión en la cuenta de Azure con sus credenciales.

     Este método es más rápido y sencillo pero, si lo utiliza, no podrá ver la Base de datos SQL de Azure ni los Servicios móviles en la ventana **Server Explorer**.

     En **Server Explorer**, haga clic en el botón **Conectar a Azure**. También puede hacer clic con el botón derecho en el nodo **Azure** y, a continuación, en **Conectar a Azure** en el menú contextual.

   * Instale un certificado de administración que permita el acceso a su cuenta.

     En **Server Explorer**, haga clic con el botón derecho en el nodo **Azure** y, a continuación, haga clic en **Administrar suscripciones** en el menú contextual. En el cuadro de diálogo **Administrar suscripciones de Azure**, haga clic en la pestaña **Certificados** y, a continuación, en **Importar**. Siga las instrucciones para descargar e importar un archivo de suscripción (también conocido como archivo *.publishsettings*) para su cuenta de Azure.

     >[AZURE.NOTE]Descargue el archivo de suscripción en una carpeta ajena a los directorios de código fuente (por ejemplo, en la carpeta Descargas) y elimínelo una vez que finalice la importación. Si un usuario malintencionado obtuviera acceso al archivo de suscripción, podría editar, crear y eliminar servicios de Azure.

	Para obtener más información, consulte [Conexión a Azure desde Visual Studio](http://go.microsoft.com/fwlink/?LinkId=324796).

<!---HONumber=Oct15_HO3-->