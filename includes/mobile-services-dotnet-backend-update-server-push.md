
1. En el Explorador de soluciones de Visual Studio, muestre la carpeta **Controladores** en el proyecto de servicio móvil. Abra TodoItemController.cs y actualice la definición del método `PostTodoItem` con el siguiente código  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            // Create a WNS native toast.
            WindowsPushMessage message = new WindowsPushMessage();

            // Define the XML paylod for a WNS native toast notification 
			// that contains the text of the inserted item.
            message.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                                 @"<toast><visual><binding template=""ToastText01"">" +
                                 @"<text id=""1"">" + item.Text + @"</text>" +
                                 @"</binding></visual></toast>";
            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Este código envía una notificación de inserción (con el texto del elemento insertado) tras insertar un elemento todo. En caso de error, el código agregará una entrada al registro de errores que aparecerá en la pestaña **Registros** del servicio móvil en el [Portal de Azure clásico](https://manage.windowsazure.com/).

	>[AZURE.NOTE]Puede usar las notificaciones de plantilla para enviar una sola notificación de inserción a los clientes en varias plataformas. Para obtener más información, consulte [Compatibilidad de plataformas de varios dispositivos desde un único servicio móvil](../articles/mobile-services-how-to-use-multiple-clients-single-service.md#push).

2. Vuelva a publicar el proyecto de servicio móvil en Azure.

<!---HONumber=AcomDC_1203_2015-->