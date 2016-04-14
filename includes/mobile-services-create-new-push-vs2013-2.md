1. En el archivo insert.js de la tabla **channels**, busque las siguientes líneas de código, coméntelas o elimínelas del archivo y, a continuación, guarde los cambios.

		sendNotifications(item.channelUri);

		function sendNotifications(uri) {
		    console.log("Uri: ", uri);
		    push.wns.sendToastText01(uri, {
		        text1: "Sample toast from sample insert"
		    }, {
		        success: function (pushResponse) {
		            console.log("Sent push:", pushResponse);
		        }
		    });
		}
		
	Cuando guarde los cambios en el archivo insert.js, se cargará una nueva versión del script en el servicio móvil.

2. En el Explorador de servidores, expanda la tabla TodoItem, abra el archivo insert.js y reemplace la función de inserción actual por el siguiente código y, a continuación, guarde los cambios:

		function insert(item, user, request) {
			request.execute({
				success: function() {
					request.respond();
					sendNotifications();
				}
			});
		
			function sendNotifications() {
				var channelsTable = tables.getTable('channels');
				channelsTable.read({
					success: function(devices) {
						devices.forEach(function(device) {
							push.wns.sendToastText04(device.channelUri, {
								text1: item.text
							}, {
								success: function(pushResponse) {
									console.log("Sent push:", pushResponse);
								}
							});
						});
					}
				});
			}
		}
		
	Ahora, cuando inserte un TodoItem nuevo, se enviará una notificación push a todos los dispositivos registrados.

<!---HONumber=Oct15_HO3-->