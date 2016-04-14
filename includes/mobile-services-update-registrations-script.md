

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en la pestaña **Datos** y en la tabla **Registros**. 

	![](./media/mobile-services-update-registrations-script/mobile-portal-data-tables-registrations.png)

2. En **Registros**, haga clic en la pestaña **Script** y seleccione **Insertar**.
   
	![](./media/mobile-services-update-registrations-script/mobile-insert-script-registrations.png)

	Se muestra la función que se invoca cuando se produce una inserción en la tabla **Registros**.

3. Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Guardar**:

		function insert(item, user, request) {
			var registrationTable = tables.getTable('Registrations');
			registrationTable
				.where({ handle: item.handle })
				.read({ success: insertChannelIfNotFound });
	        function insertChannelIfNotFound(existingRegistrations) {
        	    if (existingRegistrations.length > 0) {
            	    request.respond(200, existingRegistrations[0]);
        	    } else {
            	    request.execute();
        	    }
    	    }
	    }

   De esta manera, se registra un nuevo script de inserción, que almacena la información de registro en la nueva tabla.

<!---HONumber=AcomDC_1203_2015-->