1. В **обозревателе решений** Visual Studio разверните папку **Контроллеры** в проекте мобильного внутреннего сервера. Откройте файл **TodoItemController.cs**. Добавьте следующие операторы `using` в начало файла:

        using Microsoft.Azure.Mobile.Server.Notifications;


2. Замените метод `PostTodoItem` следующим кодом:
        
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