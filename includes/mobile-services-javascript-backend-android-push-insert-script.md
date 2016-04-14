
1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en la pestaña **Datos** y luego en la tabla **TodoItem**. 
 
2. En **todoitem**, haga clic en la pestaña **Script** y seleccione **Insertar**.
   
   	Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

3. Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Guardar**:

		function insert(item, user, request) {
		// Define a simple payload for a GCM notification.
	    var payload = {
	        "data": {
	            "message": item.text
	        }
	    };		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		        push.gcm.send(null, payload, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse, payload);
		                request.respond();
		                },              
		            error: function (pushResponse) {
		                console.log("Error Sending push:", pushResponse);
		                request.respond(500, { error: pushResponse });
		                }
		            });
		        },
		    error: function(err) {
		        console.log("request.execute error", err)
		        request.respond();
		    }
		  });
		}

   	Esto registra un nuevo script de inserción, que usa el [objeto gcm](http://go.microsoft.com/fwlink/p/?LinkId=282645) para enviar una notificación push a todos los dispositivos registrados después de que se realiza la inserción.

<!---HONumber=AcomDC_1203_2015-->