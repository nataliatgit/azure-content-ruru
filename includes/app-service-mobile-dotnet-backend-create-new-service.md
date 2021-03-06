1. Войдите на [портал Azure].

2. В верхней левой части окна нажмите кнопку **+Создать** > **Интернет + мобильные устройства** > **Мобильное приложение**, а затем введите имя серверной части своего мобильного приложения.

3. В поле **Группа ресурсов** выберите существующую группу ресурсов. Если у вас нет группы ресурсов, введите имя своего приложения.
 
	На этом этапе будет выбран план службы приложений по умолчанию — [ценовая категория "Стандартный"](https://azure.microsoft.com/pricing/details/app-service/). Параметры плана службы приложений определяют расположение, функции, стоимость и вычислительные ресурсы, связанные с вашим приложением. Выберите другой план службы приложений или создайте новый. Дополнительные сведения о планах службы приложений и создании нового плана другой ценовой категории и в другом расположении приведены в статье [Подробный обзор планов службы приложений Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

4. Вы можете использовать план службы приложений по умолчанию, выбрать другой план или [создать новый план](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Затем нажмите кнопку **Создать**.
	
	Будет создана серверная часть мобильного приложения, для которой позднее будет развернут серверный проект. Подготовка серверной части мобильного приложения может занять несколько минут, после чего отобразится колонка **Параметры**. Перед использованием серверной части мобильного приложения необходимо определить подключение к хранилищу данных.

    > [AZURE.NOTE] В рамках данного учебника вы создадите новый экземпляр базы данных SQL и сервер. Эту новую базу данных можно потом использовать и администрировать ее, как и любой другой экземпляр базы данных SQL. Если у вас уже есть база данных в одном расположении с новой серверной частью мобильного приложения, вы можете выбрать эту базу данных, щелкнув **Использовать существующую базу данных**. Из-за затрат на увеличение пропускной способности и более длительных задержек не рекомендуется использовать базу данных в другом расположении. Доступны и другие параметры хранения данных.

6. В колонке **Параметры** серверной части нового мобильного приложения последовательно щелкните **Быстрый запуск** > клиентская платформа приложения > **Подключить базу данных**.

	![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. В колонке **Добавление подключения к данным** щелкните **База данных SQL** > **Создать базу данных**, а затем введите **имя** базы данных, выберите ценовую категорию и щелкните **Сервер**.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. В колонке **Новый сервер** в поле **Имя сервера** введите уникальное имя сервера, укажите надежные **имя администратора сервера** и **пароль**, а затем проверьте, установлен ли флажок **Разрешить службам Azure доступ к серверу**, и дважды щелкните **ОК**. Будут созданы новая база данных и сервер.

10. Вернитесь в колонку **Добавление подключения к данным**, щелкните **Строка подключения**, введите имя для входа и пароль для базы данных, а затем дважды щелкните **ОК**.

	Создание базы данных может занять несколько минут. Используйте область **Уведомления**, чтобы отслеживать ход выполнения развертывания. Не выполняйте никаких действий, пока база данных не будет успешно развернута.


<!-- URLs. -->
[портал Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0224_2016-->