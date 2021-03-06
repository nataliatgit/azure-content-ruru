<properties
	pageTitle="Настройка проверки подлинности учетной записи Майкрософт для приложения служб приложений"
	description="Настройка проверки подлинности учетной записи Майкрософт для приложения служб приложений."
	authors="mattchenderson"
	services="app-service"
	documentationCenter=""
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/24/2016"
	ms.author="mahender"/>

# Настройка приложения службы приложений для использования входа по учетной записи Майкрософт

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

В этом разделе показано, как настроить службу приложений Azure для использования учетной записи Майкрософт в качестве поставщика проверки подлинности.

## <a name="register-windows-dev-center"> </a>Регистрация приложения для Windows в Центре разработки для Windows

Универсальные приложения для Windows и приложения Магазина Windows должны быть зарегистрированы в Центре разработки для Windows. Это упрощает настройку push-уведомлений для приложения в будущем.

>[AZURE.NOTE]Пропустите этот раздел, если вас интересуют приложения Windows Phone 8, Windows Phone 8.1 Silverlight, а также приложения не на базе Windows. Если вы уже настроили push-уведомления для своего приложения для Windows, вы также можете пропустить этот раздел.

1. Перейдите на [портал Azure] и перейдите к своему приложению. Скопируйте свой **URL-адрес**. Он понадобится для настройки учетной записи Майкрософт в приложении.

2. Если вы еще не зарегистрировали свое приложение, перейдите в [Центр разработки для Windows](https://dev.windows.com/dashboard/Application/New), войдите с использованием своей учетной записи Майкрософт, введите имя своего приложения, проверьте, доступно ли оно, а затем щелкните **Зарезервировать имя приложения**.

3. Откройте проект приложения для Windows в Visual Studio и в обозревателе решений щелкните правой кнопкой мыши проект приложения Магазина Windows. Выберите **Магазин**, а затем — **Связать приложение с Магазином…**.

  	![](./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-windows-store-association.png)

4. В окне мастера щелкните **Войти** и войдите в систему с помощью учетной записи Майкрософт, выберите зарезервированное имя приложения, затем щелкните **Далее** > **Связать**. Чтобы зарегистрировать универсальное приложение для Windows 8.1, необходимо повторить шаги 4 и 5 для проекта Магазина Windows Phone.

6. На странице создания приложения в центре разработки для Windows щелкните **Службы** > **Push-уведомления**.

7. На странице **Push-уведомления** щелкните **Сайт служб Live** в разделе **Служба push-уведомлений Windows (WNS) и мобильные службы Microsoft Azure**.

Далее вы настроите аутентификацию учетных записей Майкрософт для своего приложения для Windows.


## <a name="register-microsoft-account"> </a>Регистрация приложения с использованием учетной записи Майкрософт

Если вы уже зарегистрировали приложение для Windows в предыдущем разделе, можно перейти к шагу 4.

1. Перейдите на [портал Azure] и перейдите к своему приложению. Скопируйте свой **URL-адрес**. Он понадобится для настройки учетной записи Майкрософт в приложении.

2. Перейдите на страницу [Мои приложения] в центре разработки для учетной записи Майкрософт и войдите по учетной записи Майкрософт, если это требуется.

3. Щелкните элемент **Создать приложение**, введите **Имя приложения** и нажмите кнопку **Я принимаю**.

4. Щелкните **Параметры API**, выберите значение **Да** для параметра **Мобильное или классическое клиентское приложение**, введите **URL-адрес перенаправления** для приложения, затем нажмите кнопку **Сохранить**.
 
	![][0]

	>[AZURE.NOTE]Ваш универсальный код ресурса (URI) перенаправления — это URL-адрес приложения, дополненный путем _/.auth/login/microsoftaccount/callback_. Например, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Убедитесь, что используете схему HTTPS.

6. Щелкните **Параметры приложений** и запишите значения **Идентификатор клиента** и **Секрет клиента**.

    > [AZURE.IMPORTANT] Секрет клиента — это важные учетные данные безопасности. Не сообщайте никому этот секрет клиента и не распространяйте его вместе со своим приложением.

## <a name="secrets"> </a>Добавление данных учетной записи Майкрософт в приложение службы приложений

1. На [портале Azure] перейдите к своему приложению, затем щелкните **Параметры** > **Аутентификация и авторизация**.

2. Если функция аутентификации или авторизации не включена, установите переключатель в положение **Вкл.**.

3. Щелкните **учетную запись Майкрософт**. Вставьте значения идентификатора и секретного кода приложения, полученные ранее, и, при необходимости, включите любые области, которые требуются приложению. Нажмите кнопку **ОК**.

    ![][1]

	По умолчанию служба приложений обеспечивает проверку подлинности, но не ограничивает авторизованный доступ к содержимому сайта и API. Авторизация пользователей должна быть включена в код приложения.

4. (Необязательно) Чтобы предоставить доступ к узлу только для пользователей, прошедших проверку подлинности по учетной записи Майкрософт, установите для параметра **Действие, выполняемое, если запрос не прошел проверку подлинности** значение **Учетная запись Майкрософт**. В этом случае все запросы, не прошедшие проверку подлинности, направляются для проверки подлинности по учетной записи Майкрософт.

5. Щелкните **Сохранить**.

Теперь вы можете использовать учетную запись Майкрософт для проверки подлинности в приложении.

## <a name="related-content"> </a>Связанная информация

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[портал Azure]: https://portal.azure.com/
[портале Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0302_2016-->