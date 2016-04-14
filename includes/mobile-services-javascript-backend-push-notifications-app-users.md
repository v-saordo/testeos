
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y luego en su servicio móvil.

2. Haz clic en la pestaña **Insertar**, selecciona **Solo usuarios autenticados** para **Permisos**, haz clic en **Guardar** y después haz clic en **Edit Script**.
	
	Esto permite personalizar la función de devolución de llamada de registro de la notificación de inserción. Si usa Git para editar el código de origen, esta misma función de registro se encuentra en `.\service\extensions\push.js`.

3. Reemplaza la función de **registro** existente por el siguiente código y después haz clic en **Save**:

		exports.register = function (registration, registrationContext, done) {   
		    // Get the ID of the logged-in user.
			var userId = registrationContext.user.userId;    
		    
			// Perform a check here for any disallowed tags.
			if (!validateTags(registration))
			{
				// Return a service error when the client tries 
		        // to set a user ID tag, which is not allowed.		
				done("You cannot supply a tag that is a user ID");		
			}
			else{
				// Add a new tag that is the user ID.
				registration.tags.push(userId);
				
				// Complete the callback as normal.
				done();
			}
		};
		
		function validateTags(registration){
		    for(var i = 0; i < registration.tags.length; i++) { 
		        console.log(registration.tags[i]);           
				if (registration.tags[i]
				.search(/facebook:|twitter:|google:|microsoft:/i) !== -1){
					return false;
				}
				return true;
			}
		}

	Esto agrega una etiqueta al registro que es el identificador del usuario que inició sesión. Se validan las etiquetas suministradas para evitar que un usuario se registre con el identificador de otro usuario Cuando se envía una notificación a este usuario, se recibe en este y otros dispositivos registrados que el usuario haya registrado.

4. Haz clic en la flecha atrás, luego haz clic en la pestaña **Datos**, haz clic en **TodoItem**, haz clic en **Script** y selecciona **Insertar**.

<!---HONumber=AcomDC_1203_2015-->