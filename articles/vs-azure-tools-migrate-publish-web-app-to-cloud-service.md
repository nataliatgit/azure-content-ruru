<properties
   pageTitle="Инструкции. Миграция и публикация веб-приложения в облачную службу Azure из среды Visual Studio | Microsoft Azure"
   description="Узнайте, как перенести и опубликовать веб-приложение в облачную службу Azure из среды Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="01/30/2016"
   ms.author="tarcher" />

# Инструкции. Миграция и публикация веб-приложения в облачную службу Azure из среды Visual Studio

Чтобы использовать все возможности служб размещения и масштабирования, предлагаемые Azure, необходимо выполнить миграцию и публикацию веб-приложения в облачную службу Azure. Для запуска веб-приложения в Azure вам нужно будет внести в свое приложение минимальные изменения.

>[AZURE.NOTE] Эта статья посвящена развертыванию в облачных службах, а не на веб-сайтах. Сведения о развертывании веб-сайтов см. в статье [Развертывание веб-приложения в службе приложений Azure](/app-service-web/web-sites-deploy.md).

Список поддерживаемых шаблонов для Visual C# и Visual Basic см. в разделе **Поддерживаемые шаблоны проектов** ниже в этой статье.

Для начала необходимо предоставить Azure доступ к веб-приложению из Visual Studio. На приведенном ниже рисунке показаны основные этапы публикации существующего веб-приложения путем добавления проекта Azure, который будет использоваться в развертывании. В ходе этого процесса проект Azure с необходимой веб-ролью добавляется в решение. В зависимости от типа веб-проекта, если пакету служб требуются дополнительные сборки для развертывания, обновляются также свойства проекта для сборок.

![Публикация веб-приложения в Azure Майкрософт](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC748917.png)

>[AZURE.NOTE] Команда **Преобразовать**, **Преобразовать в проект облачной службы Azure** отображается только для веб-проекта в решении. Например, для проекта Silverlight в решении эта команда недоступна. При создании пакета службы или публикации приложения в Azure могут появляться предупреждения и ошибки. Они помогут вам устранить проблемы до развертывания приложения в Azure. Например, может отобразиться предупреждение об отсутствии сборки. Дополнительные сведения об обработке предупреждений в виде ошибок см. в статье [Настройка проекта облачной службы Azure с помощью Visual Studio](vs-azure-tools-configuring-an-azure-project.md). При локальном запуске созданного приложения с помощью эмулятора вычислений или публикации этого приложения в Azure в окне **Список ошибок** может отобразиться следующее сообщение об ошибке: **Указанный путь, или имя файла, или и то и другое слишком длинные**. Причиной этой ошибки является слишком длинное полное имя проекта Azure. Длина имени проекта, включая полный путь, не должна превышать 146 символов. Например, так выглядит полное имя проекта, включая путь к файлу проекта Azure, созданного для приложения Silverlight: `c:\users<user name>\documents\visual studio 2015\Projects\SilverlightApplication4\SilverlightApplication4.Web.Azure.ccproj`. Чтобы сократить полное имя проекта, можно перенести решение в другой каталог с более коротким путем.

Для переноса и публикации веб-приложения в Azure из Visual Studio выполните указанные ниже действия.

## Включение веб-приложения для развертывания в Azure

### Включение веб-приложения для развертывания в Azure

1. Чтобы включить веб-приложение для развертывания в Azure, откройте контекстное меню веб-проекта в решении и выберите пункт «Добавить проект развертывания Azure».

    После этого будут выполнены указанные ниже действия.

    - В решение будет добавлен проект Azure с именем `<name of the web project>.Azure` для приложения.

    - В этот проект Azure будет добавлена веб-роль для веб-проекта.

    - Свойству **Копировать локально** будет присвоено значение true для всех сборок, необходимых для MVC 2, MVC 3, MVC 4 и бизнес-приложений Silverlight. В результате эти сборки будут добавлены в пакет служб, используемый для развертывания.

  >[AZURE.IMPORTANT] Если для веб-приложения требуются другие сборки или файлы, свойства этих файлов необходимо настроить вручную. Инструкции по настройке этих свойств см. в разделе **Включение файлов в пакет службы**, который приводится ниже.

  >[AZURE.NOTE] Если веб-роль для конкретного веб-проекта уже существует в проекте Azure соответствующего решения, команды **Преобразовать**, **Преобразовать в проект облачной службы Azure** в контекстном меню этого веб-проекта отображаться не будут.

  Если веб-приложение содержит несколько веб-проектов и вы хотите создать веб-роли для каждого из них, выполните описанные действия для каждого веб-проекта. В результате для каждой веб-роли будут созданы отдельные проекты Azure, а каждый веб-проект можно будет опубликовать отдельно. Кроме того, в существующий проект Azure в веб-приложении можно вручную добавить другую веб-роль. Для этого откройте контекстное меню папки **Роли** в проекте Azure, последовательно щелкните пункты **Добавить** и **Проект веб-роли в решение**, выберите проект, который нужно добавить в качестве веб-роли, и нажмите кнопку **ОК**.

## Использование базы данных SQL Azure для приложения

Если у вас есть строка подключения к веб-приложению, указывающая на локальную базу данных SQL Server, строку подключения необходимо изменить таким образом, чтобы вместо локальной базы данных использовалась база данных SQL на сервере Azure.

>[AZURE.IMPORTANT] Ваша подписка должна позволять использование базы данных SQL. Если доступ к подписке осуществляется через портал управления Azure, вы можете узнать, какие услуги в нее включены. К выпущенному порталу управления применяются описанные ниже инструкции. Если используется предварительная версия портала управления, перейдите к следующей процедуре.

### Использование экземпляра базы данных SQL в веб-роли для строки подключения

1. Для создания экземпляра базы данных SQL на портале управления Azure выполните действия, описанные в статье [Создание сервера базы данных SQL](http://go.microsoft.com/fwlink/?LinkId=225109).

    >[AZURE.NOTE] При настройке правил брандмауэра для экземпляра базы данных SQL необходимо установить флажок **Разрешить другим службам Azure доступ к этому серверу**.

1. Для создания экземпляра базы данных SQL, который будет использоваться в строке подключения, выполните действия, описанные в следующем статьи [Создание базы данных SQL](http://go.microsoft.com/fwlink/?LinkId=225110).

1. Чтобы скопировать строку подключения ADO.NET для использования в своей строке подключения, на портале управления Azure выполните следующие действия.

  1. Нажмите кнопку **База данных** и откройте узел для подписки, которую вы использовали для создания экземпляра базы данных SQL.

  1. Чтобы отобразить доступные экземпляры баз данных SQL, выберите узел **Базы данных SQL**.

  1. Чтобы отобразить свойства базы данных, выберите базу данных. Откроется представление **Свойства**.

      >[AZURE.NOTE] Если представление **Свойства** не отображается, необходимо открыть его с помощью разделителя.

  1. Чтобы отобразить строки подключения, нажмите кнопку с многоточием рядом с пунктом "Просмотр".

    Откроется диалоговое окно **Строки подключения**.

  1. Чтобы скопировать строку подключения ADO.NET, выделите текст и нажмите сочетание клавиш CTRL+C.

  1. Чтобы закрыть диалоговое окно, нажмите кнопку **Закрыть**.

1. Чтобы заменить строку подключения в файле web.config для использования этого экземпляра базы данных SQL, откройте файл web.config, выделите существующую строку подключения и нажмите сочетание клавиш CTRL+V. Существующая строка подключения будет заменена на строку подключения ADO.NET для экземпляра базы данных SQL.

1. В строку подключения необходимо также добавить параметр `MultipleActiveResultSets=True`. Строка подключения должна иметь следующий формат:

    ```
    connectionString=”Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"
    ```

1. (Необязательно) Изменить строку подключения можно также непосредственно в файле web.config, добавив соответствующий раздел в один из файлов преобразования web.config в зависимости от конфигурации построения, которая использовалась для создания пакета служб. Откройте файл Web.Debug.Config или Web.Release.Config и добавьте в него следующий раздел:

    ```
    XMLCopy<connectionStrings><addname="DefaultConnection"connectionString="Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"xdt:Transform="SetAttributes"xdt:Locator="Match(name)"/></connectionStrings>
    ```

1. Сохраните измененный файл и опубликуйте приложение заново.

### Использование экземпляра базы данных SQL с помощью портала управления Azure

1. На [портале управления Azure](http://go.microsoft.com/fwlink/?LinkID=213885) выберите узел баз данных SQL.

  - Если появится экземпляр базы данных SQL, который вы хотите использовать, выберите и откройте его.

  - Если ни одного экземпляра еще не было создано, перейдите по соответствующей ссылке и создайте экземпляр.

1. После открытия или создания экземпляра базы данных перейдите по ссылке **Строки подключения**.

1. В нижней части странице перейдите по ссылке для настройки параметров брандмауэра и примите параметры по умолчанию или задайте нужные вам значения.

1. Скопируйте строку подключения ADO.NET и вставьте ее в файл web.config вместо старой строки подключения для локальной базы данных, а также добавьте значение `MultipleActiveResultSets=True`.

## Публикация веб-приложения в Azure

### Публикация веб-приложения в Azure

1. Чтобы протестировать приложение в локальной среде разработки с использованием эмулятора вычислений Azure, откройте контекстное меню проекта Azure для веб-роли и выберите команду **Назначить запускаемым проектом**. Затем выберите пункт **Отладка**, **Начать отладку** (клавиша **F5**).

    Появится диалоговое окно **Запустить среду отладки Azure**, а приложение будет запущено в браузере. Точные сведения о запуске веб-приложений разных типов в эмуляторе вычислений см. в таблице, представленной в этом разделе.

1. Для подготовки служб приложения к публикации в Azure требуется учетная запись Майкрософт и подписка на Azure. Чтобы настроить службы, выполните действия, описанные в следующем разделе: [Подготовка к публикации или развертыванию приложения Azure из Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Чтобы опубликовать веб-приложение в Azure, откройте контекстное меню веб-проекта и выберите пункт **Опубликовать в Azure**.

    Появится диалоговое окно **Публикация приложения Azure**, а Visual Studio запустит процесс развертывания. Дополнительные сведения о публикации приложения см. в разделе **Публикация приложения Azure из Visual Studio** статьи [Публикация облачной службы с помощью средств Azure](vs-azure-tools-publishing-a-cloud-service.md).

    >[AZURE.NOTE] Веб-приложение можно также опубликовать из проекта Azure. Для этого откройте контекстное меню проекта Azure и выберите пункт **Опубликовать**.

1. Ход развертывания можно наблюдать в окне **Журнал действий Azure**. Этот лог отображается автоматически при запуске процесса развертывания. Чтобы открыть подробные сведения, разверните соответствующую строку в журнале действий, как показано на следующей иллюстрации:

    ![VST\_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Необязательно) Чтобы отменить процесс развертывания, откройте контекстное меню строки в журнале действий и выберите пункт **Отменить и удалить**. Это позволит остановить процесс развертывания и удалить среду развертывания из Azure.

    >[AZURE.NOTE] Чтобы удалить среду развертывания после того, как она будет развернута, воспользуйтесь порталом управления Azure.

1. (Необязательно) После запуска экземпляров ролей Visual Studio автоматически отображает среду развертывания в узле **Вычисление Azure** в **обозревателе облака** или в **обозревателе серверов**. Здесь можно просматривать состояние отдельных экземпляров ролей.

    На приведенной ниже иллюстрации показаны экземпляры роли в **обозревателе серверов**, находящиеся в состоянии «Инициализация».

    ![VST\_DeployComputeNode](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744134.png)

1. Для доступа к приложению после развертывания выберите стрелку рядом с развертыванием, когда в **журнале действий Azure** появится статус **Завершено**. Это действие позволит отобразить URL-адрес веб-приложения в Azure. Сведения о запуске веб-приложения определенного типа из Azure см. в следующей таблице.

    В приведенной ниже таблице представлены сведения о запуске веб-приложений определенного типа из Azure и локальном запуске или отладке веб-приложения с использованием эмулятора вычислений Azure.

    |Тип веб-приложения|Локальный запуск и отладка с помощью эмулятора вычислений|Выполнение в Azure|
    |---|---|---|
    |Веб-приложение ASP.NET|В строке меню выберите **Отладка**, **Начать отладку** (клавиша **F5**).|Щелкните гиперссылку URL-адреса, отображаемую на вкладке **Развертывание** окна **Журнал действий Azure** для загрузки начальной страницы в браузере.|
    |Веб-приложение ASP.NET MVC 2|В строке меню выберите **Отладка**, **Начать отладку** (клавиша **F5**).|Щелкните гиперссылку URL-адреса, отображаемую на вкладке **Развертывание** окна **Журнал действий Azure** для загрузки начальной страницы в браузере.|
    |Веб-приложение ASP.NET MVC 3|В строке меню выберите **Отладка**, **Начать отладку** (клавиша **F5**).|Щелкните гиперссылку URL-адреса, отображаемую на вкладке **Развертывание** окна **Журнал действий Azure** для загрузки начальной страницы в браузере.|
    |Веб-приложение ASP.NET MVC 4|В строке меню выберите **Отладка**, **Начать отладку** (клавиша **F5**).|Щелкните гиперссылку URL-адреса, отображаемую на вкладке **Развертывание** окна **Журнал действий Azure** для загрузки начальной страницы в браузере.|
    |Пустое веб-приложение ASP.NET|В приложение необходимо добавить ASPX-страницу, которая будет служить начальной страницей веб-проекта. Затем в строке меню выберите **Отладка**, **Начать отладку** (клавиша **F5**).|Если в приложении есть ASPX-страница по умолчанию, щелкните гиперссылку URL-адреса, отображаемую на вкладке **Развертывание** окна **Журнал действий Azure**, а эта страница будет загружена в браузере. Чтобы открыть другую ASPX-страницу, введите URL-адрес в следующем формате: `<url for deployment>/<name of page>.aspx`.|
    |Приложение Silverlight|В строке меню выберите **Отладка**, **Начать отладку** (клавиша **F5**).|Чтобы перейти к заданной странице вашего приложения, введите URL-адрес в следующем формате: `<url for deployment>/<name of page>.aspx`.|
    |Бизнес-приложение Silverlight|В строке меню выберите **Отладка**, **Начать отладку** (клавиша **F5**).|Чтобы перейти к заданной странице вашего приложения, введите URL-адрес в следующем формате: `<url for deployment>/<name of page>.aspx`.|
    |Приложение навигации Silverlight|В строке меню выберите **Отладка**, **Начать отладку** (клавиша **F5**).|Чтобы перейти к заданной странице вашего приложения, введите URL-адрес в следующем формате: `<url for deployment>/<name of page>.aspx`.|
    |Приложение службы WCF|Задайте SVC-файл в качестве начальной страницы для проекта службы WCF. Затем в строке меню выберите **Отладка**, **Начать отладку** (клавиша **F5**).|Чтобы перейти к SVC-файлу вашего приложения, введите URL-адрес в следующем формате: `<url for deployment>/<name of service file>.svc`.|
    |Приложение службы рабочего процесса WCF|Задайте SVC-файл в качестве начальной страницы для проекта службы WCF. Затем в строке меню выберите **Отладка**, **Начать отладку** (клавиша **F5**).|Чтобы перейти к SVC-файлу вашего приложения, введите URL-адрес в следующем формате: `<url for deployment>/<name of service file>.svc`.|
    |Динамические сущности ASP.NET|В строке меню выберите **Отладка**, **Начать отладку** (клавиша **F5**).|Обновите строку подключения (см. следующий раздел). Чтобы перейти к заданной странице вашего приложения, введите URL-адрес в следующем формате: `<url for deployment>/<name of page>.aspx`.|
    |Платформа динамических данных ASP.NET типа Linq to SQL|В строке меню выберите **Отладка**, **Начать отладку** (клавиша **F5**).|Выполните следующую процедуру: использование базы данных SQL Azure для приложения (выше в этой статье). Чтобы перейти к заданной странице вашего приложения, введите URL-адрес в следующем формате: `<url for deployment>/<name of page>.aspx`.|

## Обновление строки подключения для динамических сущностей ASP.NET

### Обновление строки подключения для динамических сущностей ASP.NET

1. Чтобы создать базу данных SQL Azure, которую можно будет использовать в веб-приложении динамических сущностей ASP.NET, выполните процедуру **Использование базы данных SQL Azure для приложения**, описанную выше в этом разделе.

1. Добавьте таблицы и поля, необходимые для этой базы данных, из портала управления Azure.

1. Строка подключения для приложений этого типа имеет в файле web.config следующий формат:

    ```
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=";data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework";"providerName="System.Data.EntityClient"/>
    ```

    Обновите значение *connectionString*, используя строку подключения ADO.NET для базы данных SQL Azure, следующим образом:

    ```
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=";Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework";"providerName="System.Data.EntityClient"/>
    ```

1. Чтобы сохранить файл web.config с внесенными в строку подключения изменениями, последовательно выберите пункты **Файл**, **Сохранить web.config** в строке меню.

## Поддерживаемые шаблоны проектов

Для публикации веб-приложения в Azure приложение должно быть основано на одном из шаблонов проектов для C# или Visual Basic, перечисленных в приведенной ниже таблице.

|Группа шаблонов проекта|Шаблон проекта|
|---|---|
|Веб-приложения|Веб-приложение ASP.NET|
|Веб-приложения|Веб-приложение ASP.NET MVC 2|
|Веб-приложения|Веб-приложение ASP.NET MVC 3|
|Веб-приложения|Веб-приложение ASP.NET MVC 4|
|Веб-приложения|Пустое веб-приложение ASP.NET|
|Веб-приложения|Пустое веб-приложение ASP.NET MVC 2|
|Веб-приложения|Веб-приложение динамических сущностей данных ASP.NET|
|Веб-приложения|Веб-приложение динамических сущностей данных Linq to SQL ASP.NET|
|Silverlight|Приложение Silverlight|
|Silverlight|Бизнес-приложение Silverlight|
|Silverlight|Приложение навигации Silverlight|
|WCF|Приложение службы WCF|
|WCF|Приложение службы рабочего процесса WCF|
|Рабочий процесс|Приложение службы рабочего процесса WCF|

## Дальнейшие действия
Дополнительные сведения о публикации см. в статье [Подготовка к публикации или развертыванию приложения Azure из Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md). Также см. статью [Настройка именованных учетных данных для проверки подлинности](vs-azure-tools-setting-up-named-authentication-credentials.md).

<!----HONumber=AcomDC_0204_2016-->