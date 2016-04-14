1.	Inicie sesión en el [Portal de Microsoft Azure](https://portal.azure.com/) en línea.
2.	En la barra de salto, haga clic en **Nuevo**, en **Datos y almacenamiento** y luego en **Azure DocumentDB**.

	![Captura de pantalla del Portal de Azure de creación de bases de datos en la que está resaltado el botón Nuevo, Datos y almacenamiento en la hoja Crear y Azure DocumentDB en la hoja Datos y almacenamiento](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. En la hoja **Nueva cuenta de DocumentDB**, especifique la configuración que desee para la cuenta de DocumentDB.

	![!Screen shot of the New DocumentDB (Preview) blade3](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)


	- En el cuadro **Id.**, escriba un nombre para identificar la cuenta de DocumentDB. Cuando se valida el **identificador**, aparece una marca de verificación verde en el cuadro **Id.** El valor de **Id.** se convierte en el nombre de host dentro del URI. El **identificador** puede contener solo minúsculas, números y el carácter "-"; debe tener entre 3 y 50 caracteres. Tenga en cuenta que *documents.azure.com* se anexa al nombre de extremo que elija; su resultado se convertirá en el extremo de la cuenta de DocumentDB.

	- En **Suscripción**, seleccione la suscripción a Azure que quiere usar para la cuenta de DocumentDB. Si la cuenta tiene una sola suscripción, dicha cuenta se selecciona de manera predeterminada.

	- En **Grupo de recursos**, seleccione o cree un grupo de recursos para la cuenta de DocumentDB. De forma predeterminada, se elegirá un grupo de recursos existente en la suscripción de Azure. Sin embargo, puede elegir crear un nuevo grupo de recursos al que desea agregar su cuenta de DocumentDB. Para más información, consulte [Uso del Portal de Azure para administrar los recursos de Azure](resource-group-portal.md).

	- Use **Ubicación** para especificar la ubicación geográfica en la que se hospedará la cuenta de DocumentDB.

4.	Una vez que las nuevas opciones de cuenta de DocumentDB estén configuradas, haga clic en **Crear**. La cuenta de DocumentDB puede tardar unos minutos en crearse. Para comprobar el estado, puede supervisar el progreso en el Panel de inicio.  
	![Captura de pantalla del icono de creación en el Panel de inicio - creador de bases de datos en línea](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-3.png)

	O bien, puede supervisar su progreso en el centro de notificaciones.

	![Creación rápida de bases de datos - Captura de pantalla del Centro de notificaciones, donde se indica que se está creando la cuenta de DocumentDB](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-4.png)

	![Captura de pantalla del Centro de notificaciones, que indica que se creó correctamente la cuenta de DocumentDB y se implementó en un grupo de recursos - Notificación del creador de bases de datos en línea](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-5.png)

5.	Después de crear la cuenta de DocumentDB, ya se puede usar con la configuración predeterminada en el portal en línea. Tenga en cuenta que la coherencia predeterminada de la cuenta de DocumentDB está establecida en **Sesión**. Para ajustar la configuración predeterminada, haga clic en el icono de **configuración** en la barra de comandos de la parte superior, luego haga clic en la entrada **Coherencia predeterminada** en **Característica** en la hoja **Todas las configuraciones**.

    ![Captura de pantalla de la hoja Grupo de recursos - comenzar el desarrollo de aplicaciones](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-6.png)

    ![Captura de pantalla de la hoja Nivel de coherencia: coherencia de la sesión](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-7.png)

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]: ../articles/documentdb/documentdb-manage.md

<!---HONumber=AcomDC_0302_2016-->