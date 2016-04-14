#### Para crear un dispositivo virtual

1.  En el Portal de Azure, vaya al servicio **StorSimple Manager**.

2. Vaya a la página **Dispositivos**. Haga clic en **Crear un dispositivo virtual** en la parte inferior de la página **Dispositivos**.

3. En el **cuadro de diálogo Crear un dispositivo virtual**, especifique los detalles siguientes.

     ![Crear dispositivo virtual de StorSimple](./media/storsimple-create-virtual-device-u2/CreatePremiumsva1.png)

	1. **Nombre**: un nombre único para el dispositivo virtual.


	2. **Modelo**: elija el modelo del dispositivo virtual. Este campo se muestra solo si se ejecuta Update 2 o posterior. Un modelo de dispositivo 8010 ofrece 30 TB de almacenamiento estándar, mientras que 8020 tiene 64 TB de almacenamiento premium. Especifique 8010
	3.  para implementar escenarios de recuperación de nivel de elemento a partir de copias de seguridad. Seleccione 8020 para implementar cargas de trabajo de baja latencia de alto rendimiento o de uso como un dispositivo secundario para recuperación ante desastres.
	 
	4. **Versión**: elija la versión del dispositivo virtual. Si se selecciona un modelo de dispositivo 8020, el campo de versión no se presentará al usuario. Esta opción está ausente si todos los dispositivos físicos registrados con este servicio ejecutan Update 1 (o posterior). Este campo se muestra solo si tiene una combinación de dispositivos físicos Update 1 y anteriores registrados con el mismo servicio. Dado que la versión del dispositivo virtual determinará desde qué dispositivo físico puede efectuar la conmutación por error o la clonación, es importante que cree una versión adecuada del dispositivo virtual. Seleccione:

	   - Versión Update 0.3 si conmuta por error o efectúa una recuperación ante desastres a partir de un dispositivo físico que ejecuta Update 0.3 o una versión anterior. 
	   - Versión Update 1 si conmuta por error o efectúa una clonación a partir de un dispositivo físico que ejecuta Update 1 (o una versión posterior). 
	   
	
	5. **Red virtual**: especifique una red virtual que desea usar con este dispositivo virtual. Si utiliza almacenamiento premium (Update 2 o posterior), debe seleccionar una red virtual que sea compatible con la cuenta de almacenamiento premium. Las redes virtuales no compatibles se atenuarán en la lista desplegable. Se le avisará si selecciona una red virtual no compatible. 

	5. **Cuenta de almacenamiento para la creación de dispositivos virtuales**: seleccione una cuenta de almacenamiento para almacenar la imagen del dispositivo virtual durante el aprovisionamiento. Esta cuenta de almacenamiento debe estar en la misma región que el dispositivo virtual y la red virtual. No debe utilizarse para el almacenamiento de datos por el dispositivo físico o el dispositivo virtual. De forma predeterminada, para ello se creará una nueva cuenta de almacenamiento. Sin embargo, si sabe que ya tiene una cuenta de almacenamiento que es adecuada para este uso, puede seleccionarla en la lista. Si crea un dispositivo virtual premium, la lista desplegable sólo mostrará las cuentas de almacenamiento premium.

    	>[AZURE.NOTE]El dispositivo virtual solo puede funcionar con las cuentas de almacenamiento de Azure. Otros proveedores de servicios en la nube como Amazon, HP y OpenStack (que son compatibles con el dispositivo físico) no se admiten para el dispositivo virtual StorSimple.
	
	1. Haga clic en la marca de verificación para indicar que sabe que los datos almacenados en el dispositivo virtual estarán hospedados en un centro de datos de Microsoft. Cuando se utiliza solo un dispositivo físico, la clave de cifrado se mantiene con el dispositivo; por lo tanto, Microsoft no podrá descifrarla.
	 
		Cuando se utiliza un dispositivo virtual, la clave de cifrado y la clave de descifrado se almacenan en Microsoft Azure. Para más información, consulte [Consideraciones de seguridad para utilizar un dispositivo virtual](storsimple-security/#storsimple-virtual-device-security).
	2. Haga clic en el icono de verificación para crear el dispositivo virtual. El dispositivo puede tardar unos 30 minutos en aprovisionar.

	![Fase de creación de dispositivo virtual de StorSimple](./media/storsimple-create-virtual-device-u2/StorSimple_VirtualDeviceCreating1M.png)

    

<!---HONumber=AcomDC_1217_2015-->