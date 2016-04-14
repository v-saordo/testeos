Siga estos pasos para instalar y ejecutar MongoDB en una máquina virtual con Windows Server.

> [AZURE.IMPORTANT]Las características de seguridad de MongoDB, como la vinculación de direcciones IP y la autenticación, no se encuentran habilitadas de forma predeterminada. Las características de seguridad deben habilitarse antes de implementar MongoDB en un entorno de producción. Consulte [Seguridad y autenticación](http://www.mongodb.org/display/DOCS/Security+and+Authentication) para obtener más información.

1. Después de conectarse a la máquina virtual con Escritorio remoto, abra Internet Explorer en el menú **Inicio** de la máquina virtual.

2. Seleccione el botón **Herramientas** de la esquina superior derecha. En **Opciones de Internet**, seleccione la pestaña **Seguridad**, luego el icono **Sitios de confianza** y, por último, haga clic en el botón **Sitios**. Agregue \__http://*.mongodb.org_ a la lista de sitios de confianza.

3. Vaya a [Descargas - MongoDB][MongoDownloads].

4. Busque la versión estable actual **(Current Stable Release)**, seleccione la versión más reciente de **64 bits** en la columna Windows, descargue el programa de instalación MSI y ejecútelo.

5. MongoDB se instala normalmente en C:\Program Files\MongoDB. Busque variables de entorno en el escritorio y agregue la ruta de acceso de los archivos binarios de MongoDB a la variable PATH. Por ejemplo, podría encontrar los archivos binarios en C:\Program Files\MongoDB\Server\3.0\bin en el equipo.

6. Cree datos de MongoDB y directorios de registro en el disco de datos (unidad **F:**, por ejemplo) que creó en los pasos anteriores. En **Inicio**, seleccione **Símbolo del sistema** para abrir una ventana de símbolo del sistema. Escriba:

		C:\> F:
		F:> mkdir \MongoData
		F:> mkdir \MongoLogs

7. Para ejecutar la base de datos, ejecute:

		F:> C:
		C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

	Todos los mensajes de registro se dirigirán al archivo *F:\MongoLogs\mongolog.log* mientras el servidor mongod.exe se inicia y preasigna los archivos de diario. MongoDB puede tardar unos minutos en preasignar los archivos de diario e iniciar la escucha de las conexiones.

8. Para iniciar el shell administrativo de MongoDB, abra otra ventana de comandos desde **Inicio** y escriba lo siguiente:

		C:\> cd \my_mongo_dir\bin  
		C:\my_mongo_dir\bin> mongo  
		>db  
		test
		> db.foo.insert( { a : 1 } )  
		> db.foo.find()  
		{ _id : ..., a : 1 }  
		> show dbs  
		...  
		> show collections  
		...  
		> help  

	La base de datos se crea mediante la inserción.

9. Como alternativa, puede instalar mongod.exe como servicio:

		C:\mongodb\bin>mongod --logpath F:\MongoLogs\mongolog.log --logappend --dbpath F:\MongoData\ --install

	De esta forma, se crea un servicio llamado MongoDB con la descripción "Mongo DB". La opción **--logpath** debe usarse para especificar un archivo de registro, puesto que el servicio en ejecución no dispondrá de una ventana de comandos para mostrar la salida. La opción **--logappend** especifica que un reinicio del servicio hará que se anexe la salida al archivo de registro existente. La opción **--dbpath** especifica la ubicación del directorio de datos. Para ver más opciones de línea de comandos relacionadas con los servicios, consulte [Opciones de línea de comandos relacionadas con los servicios][MongoWindowsSvcOptions].

	Para iniciar el servicio, ejecute este comando:

		C:\mongodb\bin>net start MongoDB

10. Ahora que MongoDB está instalado y ejecutándose, debe abrir un puerto en el Firewall de Windows para conectarse de forma remota a MongoDB. En el menú **Inicio**, seleccione **Herramientas de administrador** y luego **Firewall de Windows con seguridad avanzada**.

11. En el panel izquierdo, seleccione **Reglas de entrada**. En el panel **Acciones** de la derecha, seleccione **Nueva regla...**.

	![Firewall de Windows][Image1]

	En el **Asistente para nueva regla de entrada**, seleccione **Puerto** y luego haga clic en **Siguiente**.

	![Firewall de Windows][Image2]

	Seleccione **TCP** y luego **Puertos locales específicos**. Especifique el puerto "27017" (el puerto predeterminado en el que escucha MongoDB) y haga clic en **Siguiente**.

	![Firewall de Windows][Image3]

	Seleccione **Permitir la conexión** y haga clic en **Siguiente**.

	![Firewall de Windows][Image4]

	Haga clic en **Siguiente** de nuevo.

	![Firewall de Windows][Image5]

	Especifique un nombre para la regla, como "MongoPort", y haga clic en **Finalizar**.

	![Firewall de Windows][Image6]

12. Si no configuró un extremo para MongoDB cuando creó la máquina virtual, puede hacerlo ahora. Necesita tanto la regla de firewall como el extremo para poder conectarse a MongoDB de manera remota. En el Portal de administración, haga clic en **Máquinas virtuales**, en el nombre de la nueva máquina virtual y luego en **Extremos**.

	![Extremos][Image7]

13. Haga clic en **Agregar** en la parte inferior de la página. Seleccione **Agregar un extremo independiente** y haga clic en **Siguiente**.

	![Extremos][Image8]

14. Agregue un extremo con el nombre "Mongo", el protocolo **TCP** y los puertos **Público** y **Privado** establecidos en "27017". Esto permitirá que se obtenga acceso remoto a MongoDB.

	![Extremos][Image9]

> [AZURE.NOTE]El puerto 27017 es el puerto predeterminado usado por MongoDB. Puede cambiarlo con el subcomando _--port_ al iniciar el servidor mongod.exe. Asegúrese de usar el mismo número de puerto en el firewall y en el extremo de "Mongo" en las instrucciones anteriores.


[MongoDownloads]: http://www.mongodb.org/downloads

[MongoWindowsSvcOptions]: http://www.mongodb.org/display/DOCS/Windows+Service


[Image1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
[Image2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
[Image3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
[Image4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
[Image5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
[Image6]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
[Image7]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
[Image9]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint3.png

<!---HONumber=Oct15_HO3-->