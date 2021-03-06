<properties 
	pageTitle="Отладка приложения API в службе приложений Azure" 
	description="Узнайте, как отлаживать приложение API, когда оно выполняется в службе приложений Azure, с помощью Visual Studio." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Отладка приложения API в службе приложений Azure

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Обзор

В этом учебнике выполняется отладка кода веб-API ASP.NET, который настроен для запуска в [приложении API](app-service-api-apps-why-best-platform.md) в [службе приложений Azure](../app-service/app-service-value-prop-what-is.md). Будет выполнена отладка кода, который выполняется как локально, так и удаленно в Azure.

Учебник работает с приложением API, которое вы [создавали](app-service-dotnet-create-api-app.md) и [развертывали](app-service-dotnet-deploy-api-app.md) при изучении предыдущих учебников этой серии.

## Удаленная отладка приложения API 

Чтобы включить удаленную отладку, необходимо развернуть отладочную сборку в Azure.

1. В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши проект, который [был развернут в предыдущем учебнике](app-service-dotnet-deploy-api-app.md), и выберите **Опубликовать**.

2. В диалоговом окне **Публикация веб-сайта** откройте вкладку **Параметры** и выберите конфигурацию сборки **Отладка**.

4. Щелкните **Опубликовать**.

	![Опубликовать проект](./media/app-service-api-dotnet-debug/rd-debug-publish.png)

	В окне браузера откроется базовый URL-адрес вашего приложения API.

4. В адресной строке браузера добавьте /swagger в конец URL-адреса и нажмите клавишу ВВОД.

	Для выполнения этого шага необходимо включить пользовательский интерфейс Swagger, как описано в учебнике по [созданию](app-service-dotnet-create-api-app.md) приложений.

	![Пользовательский интерфейс Swagger](./media/app-service-api-dotnet-debug/rd-swagger-ui.png)

5. Вернитесь в Visual Studio и выберите **Вид > Обозреватель серверов**.

6. В **обозревателе сервера** разверните узел **Azure > Служба приложений**.

7. Найдите группу ресурсов, созданную или выбранную при развертывании приложения API.

8. В этой группе ресурсов щелкните правой кнопкой мыши узел вашего приложения API и выберите пункт **Подключить отладчик**.

	![Подключение отладчика](./media/app-service-api-dotnet-debug/rd-attach-debugger.png)

	Удаленный отладчик попытается подключиться. В некоторых случаях может потребоваться повторно щелкнуть пункт **Подключить отладчик**, чтобы установить подключение, поэтому при сбое повторите попытку.

	![Подключение отладчика](./media/app-service-api-dotnet-debug/rd-attaching.png)

9. После установки подключения откройте файл **ContactsController.cs** в проекте приложения API и добавьте точку останова в методе `Get`.

	![Применение точек останова к контроллеру](./media/app-service-api-dotnet-debug/rd-breakpoints.png)

10. Вернитесь в сеанс браузера, щелкните команду **Get** для отображения схемы объекта *Контакт*, а затем нажмите кнопку **Попробовать**. Visual Studio остановит выполнение программы в точке останова, и вы сможете произвести отладку логики вашего контроллера.

	![Попробовать](./media/app-service-api-dotnet-debug/rd-try-it-out.png)

## Локальная отладка приложения API 

Иногда необходимо отладить приложение API локально, например если обращение к серверу Azure замедляет процесс отладки. В данном разделе описывается способ локальной отладки приложения API с помощью пользовательского интерфейса Swagger в качестве тестового клиента.

2. В браузере перейдите на [портал предварительной версии Azure](https://portal.azure.com/). 

3. Нажмите кнопку **Обзор** на боковой панели и выберите **Приложения API**.

	![Просмотр параметров на портале Azure](./media/app-service-api-dotnet-debug/ld-browse.png)

4. В списке приложений API в подписке выберите созданное приложение API.

	![Список приложений API](./media/app-service-api-dotnet-debug/ld-api-app-list.png)

5. В колонке приложения API щелкните **Узел приложения API**.

	![Узел приложения API](./media/app-service-api-dotnet-debug/ld-api-app-blade-api-app-host.png)

6. В колонке узла приложения API щелкните **Все параметры**.

	![«Все параметры» в колонке «Узел приложения API»](./media/app-service-api-dotnet-debug/ld-api-app-host-all-settings.png)

7. В колонке **Параметры** щелкните раздел **Параметры приложения**.

	![Раздел «Параметры приложения» в колонке «Узел приложения API»](./media/app-service-api-dotnet-debug/ld-application-settings.png)

8. В колонке **Параметры веб-приложения** прокрутите вниз до раздела **Параметры приложения**.

	![Раздел «Параметры приложения» в колонке «Узел приложения API» для локальной отладки](./media/app-service-api-dotnet-debug/ld-app-settings-for-local-debugging.png)

1. В Visual Studio откройте файл *web.config* проекта приложения API.

9. В разделе **Параметры приложения** найдите все следующие ключи и значения и добавьте их в раздел **appSettings** файла *web.config*:
	- **EMA\_MicroserviceId;**
	- **EMA\_Secret;**
	- **EMA\_RuntimeUrl.**

	После этого раздел **appSettings** файла *web.config* должен напоминать снимок экрана, показанный ниже.

	![Раздел «Параметры приложения» в колонке «Узел приложения API» для локальной отладки](./media/app-service-api-dotnet-debug/ld-debug-settings.png)

	**Примечание.** В значениях *EMA\_*, добавленных в файл *web.config* в этом разделе, содержится конфиденциальная информация об авторизации. В связи с этим не рекомендуется держать эти значения в общедоступном хранилище источников информации. Дополнительную информацию см. в статье [Рекомендации для развертывания паролей и других конфиденциальных данных в ASP.NET и в службе приложений Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

10. Вставьте точку останова в коде контроллера приложения API в метод `Get`.

11. Нажмите клавишу F5, чтобы запустить сеанс отладки Visual Studio.
 
13.  Если уровень доступа приложения API имеет значение **Общедоступный (анонимно)**, вы можете воспользоваться для целей тестирования страницей пользовательского интерфейса Swagger.

	* Когда браузер загрузит страницу, отобразится сообщение об ошибке HTTP 403. Добавьте */swagger* в конец URL-адреса в адресной строке браузера и нажмите клавишу ВВОД.

	* После загрузки пользовательского интерфейса Swagger щелкните команду **Get** в окне браузера, чтобы отобразить схему для объекта «Контакт», а затем щелкните **Попробовать**.

		Visual Studio остановит выполнение программы в точке останова, и вы сможете произвести отладку логики вашего контроллера.

14.	Если задан уровень доступа приложения API **Общедоступный (с проверкой подлинности)**, необходимо выполнить проверку подлинности и воспользоваться средством браузера, как описано в разделе [Защита приложения API](app-service-api-dotnet-add-authentication.md#use-postman-to-send-a-post-request) для запроса Post.

	* Перейдите по URL-адресу входа в шлюз и введите учетные данные для входа.
	* Получите значение маркера Zumo из файла cookie x-zumo-auth.
	* Добавьте заголовок x-zumo-auth в запрос; его значение должно соответствовать значению файла cookie x-zumo-auth.
	* Отправьте запрос.

	**Примечание.** При локальном запуске Azure не может контролировать доступ к приложению API, ограничивая выполнение его методов только пользователями, прошедшими проверку подлинности. При запуске в Azure весь трафик, предназначенный для приложения API, направляется через шлюз; при этом шлюз не передает запросы, которые не прошли проверку подлинности. Поскольку при локальном запуске перенаправление не выполняется, запросам без проверки подлинности не запрещается получать доступ к приложению API. Смысл описанной процедуры проверки подлинности заключается в том, что в приложении API можно выполнить код, связанный с проверкой подлинности (например, код, который получает сведения о вошедшем в систему пользователе). Дополнительные сведения о том, как шлюз выполняет проверку подлинности для приложений API, см. в статье [Проверка подлинности для приложений API и мобильных приложений](../app-service/app-service-authentication-overview.md#azure-app-service-gateway).

## Дальнейшие действия

В этом учебнике было показано, как выполнять отладку приложений API.

Дополнительные сведения см. в статье [Устранение неполадок веб-приложения в службе приложений Azure с помощью Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md). Поскольку приложения API являются веб-приложениями, включающими дополнительные возможности для размещения веб-служб, вы можете использовать для приложений API те же средства отладки и устранения неполадок, которые используются для веб-приложений.

<!---HONumber=AcomDC_0128_2016-->