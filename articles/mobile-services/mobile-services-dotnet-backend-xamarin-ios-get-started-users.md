<properties
	pageTitle="Приступая к работе с проверкой подлинности в мобильных службах для приложений Xamarin для iOS | Microsoft Azure"
	description="Узнайте, как использовать мобильные службы для аутентификации пользователей приложения Xamarin для iOS с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="lindydonna"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/25/2015" 
	ms.author="donnam"/>

# Добавление проверки подлинности к приложению мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

>[AZURE.NOTE]Этот раздел посвящен мобильным службам Azure. Мы рекомендуем использовать мобильные приложения службы приложений Azure для всех новых развертываний мобильной серверной части. Дополнительные сведения см. в [аналогичном учебнике в документации по мобильным приложениям](../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md).

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и настройка мобильных служб]
2. [Предоставление разрешений на таблицу только пользователям, прошедшим проверку подлинности]
3. [Добавление проверки подлинности в приложение]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами].

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;&nbsp;6. В Visual Studio или Xamarin Studio запустите проект клиента на устройстве или симуляторе. Убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный).

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица *TodoItem* теперь требует выполнения такой проверки.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

В этом разделе предстоит изменить приложение для отображения экрана входа до отображения данных. При запуске приложение не подключится к вашей мобильной службе и не отобразит никаких данных. После первого обновления пользователем появится экран входа; список задач появится после успешного входа.

1. В проекте клиента откройте файл **QSTodoService.cs** и добавьте следующие объявления в QSTodoService:

		// Mobile Service logged in user
		private MobileServiceUser user;
		public MobileServiceUser User { get { return user; } }

2. Добавьте новый метод **Authenticate** в **QSTodoService** со следующим определением:

        private async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

	> [AZURE.NOTE] Если у вас поставщик удостоверений, отличный от Facebook, замените значение, передаваемое в метод **LoginAsync** выше, одним из следующих: _MicrosoftAccount_, _Twitter_, _Google_ или _WindowsAzureActiveDirectory_.

3. Откройте **QSTodoListViewController.cs** и измените определение метода **ViewDidLoad**, удалив или закомментировав вызов метода **RefreshAsync()** в конце.

4. Добавьте следующий код в верхнюю часть определения метода **RefreshAsync**:

		// Add at the start of the RefreshAsync method.
		if (todoService.User == null) {
			await QSTodoService.DefaultService.Authenticate (this);
			if (todoService.User == null) {
				Console.WriteLine ("You must sign in.");
				return;
			}
		}
		
	В результате при нулевом значении свойства **User** будет отображаться экран входа в систему для проверки подлинности. После успешного входа в систему значение свойства **User** будет установлено.

5. Нажмите кнопку **Запустить**, чтобы создать проект и запустить приложение в симуляторе iPhone. Убедитесь, что в приложении не отображаются данные. Метод **RefreshAsync()** еще не вызывался.

6. Сделайте жест обновления, потянув список элементов вниз — это вызовет метод **RefreshAsync()**. Будет вызван метод **Authenticate()** для выполнения проверки подлинности, и откроется экран входа в систему. После успешного прохождения проверки подлинности в приложении отобразится список элементов задач и появится возможность изменить данные.

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, который называется [Авторизация пользователей мобильных служб на стороне службы][Authorize users with scripts], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами.


<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и настройка мобильных служб]: #register
[Предоставление разрешений на таблицу только пользователям, прошедшим проверку подлинности]: #permissions
[Добавление проверки подлинности в приложение]: #add-authentication
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: mobile-services-dotnet-backend-xamarin-ios-get-started.md
[Get started with authentication]: mobile-services-dotnet-backend-xamarin-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-xamarin-ios-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

<!---HONumber=AcomDC_0128_2016-->