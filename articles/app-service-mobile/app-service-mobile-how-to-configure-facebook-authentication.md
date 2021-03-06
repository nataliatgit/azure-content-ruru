<properties
	pageTitle="Настройка проверки подлинности Facebook для приложения служб приложений"
	description="Настройка проверки подлинности Facebook для приложения служб приложений."
	services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/28/2016"
	ms.author="mahender"/>

# Как настроить приложение службы приложений для использования имени для входа Facebook

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

В этом разделе показано, как настроить службу приложений Azure для использования Facebook в качестве поставщика проверки подлинности.

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Facebook с проверенным электронным адресом и номером мобильного телефона. Чтобы создать новую учетную запись Facebook, перейдите по ссылке [facebook.com].

## <a name="register"> </a>Регистрация приложения с помощью Facebook

1. Войдите на [портал Azure] и перейдите к своему приложению. Скопируйте свой **URL-адрес**. Он понадобится для настройки приложения Facebook.

2. В другом окне браузера перейдите на веб-сайт [разработчиков для Facebook] и войдите, используя свои данные учетной записи Facebook.

3. (Необязательно). Если вы еще не зарегистрированы, щелкните **Apps** (Приложения) > **Register as a Developer** (Зарегистрироваться в качестве разработчика), примите условия политики и выполните действия для регистрации.

4. Щелкните **My Apps** (Мои приложения) > **Add a New App** (Добавить новое приложение) > **Website** (Веб-сайт) > **Skip and Create App ID** (Пропустить и создать идентификатор приложения).

5. В поле **Display Name** (Отображаемое имя) введите уникальное имя своего приложения, выберите категорию в разделе **Категория**, после чего нажмите кнопку **Создайте идентификатор приложения** и завершите процедуру безопасности. Вы перейдете на панель мониторинга разработчика для нового приложения Facebook.

6. В поле **Секрет приложения** щелкните **Показать**, при запросе укажите пароль, а затем запишите значения полей **Идентификатор приложения** и **Секрет приложения**. Вы используете их позже, чтобы настроить свое приложение в Azure.

	> [AZURE.IMPORTANT] Секрет приложения — это важные учетные данные безопасности. Не сообщайте этот секрет никому и не раскрывайте его в клиентском приложении.

7. В области навигации слева щелкните **Settings** (Параметры), введите **URL-адрес** мобильного приложения в поле **App Domains** (Домены приложения), также введите **контактный электронный адрес**.

    ![][0]

8. Если вы не видите раздел веб-сайта, щелкните **+Добавить платформу** > **Website** (Веб-сайт), введите **URL-адрес** мобильного приложения в поле **Site URL** (URL-адрес сайта), а затем щелкните **Save Changes** (Сохранить изменения).

9. Перейдите на вкладку **Advanced** (Дополнительно) и добавьте **универсальный код ресурса (URI) перенаправления** приложения в поле **Valid OAuth redirect URIs** (Допустимые универсальные коды ресурса (URI) перенаправления OAuth). Нажмите кнопку **Save Changes** (Сохранить изменения).

	> [AZURE.NOTE] Ваш универсальный код ресурса (URI) перенаправления — это URL-адрес приложения, дополненный путем _/.auth/login/facebook/callback_. Например, `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Убедитесь, что используете схему HTTPS.

10. Учетная запись Facebook, которая использовалась для регистрации приложения, является администратором приложения. На этом этапе только администраторы могут входить в приложение. Чтобы аутентифицировать другие учетные записи Facebook, щелкните **App Review** (Обзор приложения) и включите параметр **Make todolist-complete-nodejs public** (Сделать общедоступным todolist-complete-nodejs), чтобы включить общий доступ с использованием аутентификации Facebook.


## <a name="secrets"> </a>Добавление данных Facebook в приложение

1. Снова вернитесь на [портал Azure] и перейдите к своему приложению. Щелкните **Параметры** > **Аутентификация или авторизация** и убедитесь, что для параметра **Аутентификация службы приложений** задано значение **Вкл.**.

2. Щелкните **Facebook**, вставьте значения идентификатора и секрета приложения, полученные ранее, и, при необходимости, включите любые области, которые требуются приложению. Нажмите кнопку **ОК**.

    ![][1]

	По умолчанию служба приложений обеспечивает проверку подлинности, но не ограничивает авторизованный доступ к содержимому сайта и API. Авторизация пользователей должна быть включена в код приложения.

3. (Необязательно) Чтобы предоставить доступ к сайту только для пользователей, прошедших проверку подлинности в Facebook, установите для параметра **Действие, выполняемое, если запрос не прошел проверку подлинности** значение **Facebook**. В этом случае все запросы, не прошедшие проверку подлинности, направляются для проверки подлинности с помощью Facebook.

4. Завершив настройку аутентификации, нажмите кнопку **Сохранить**.

Теперь вы можете использовать Facebook для проверки подлинности в приложении.

## <a name="related-content"> </a>Связанная информация

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[разработчиков для Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /ru-RU/develop/mobile/tutorials/get-started-with-users-dotnet/
[портал Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0302_2016-->
