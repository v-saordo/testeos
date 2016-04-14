1. En el **Explorador de soluciones** de Visual Studio, expanda la carpeta **Controladores** en el proyecto de back-end móvil. Abra **TodoItemController.cs**. Al principio del archivo, agregue las siguientes instrucciones `using`:

        using Microsoft.Azure.Mobile.Server.Notifications;


2. Reemplace el método `PostTodoItem` por el código siguiente:
        
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            HttpConfiguration config = this.Configuration;

            TemplatePushMessage message = new TemplatePushMessage();
            message.Add("message", item.Text);

            try
            {
                var client = new PushClient(config);
                var result = await client.SendAsync(message);

                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                config.Services.GetTraceWriter().Error(ex.Message, null, "Push.SendAsync Error");
            }

            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

<!---HONumber=Oct15_HO3-->