<properties
	pageTitle="Приступая к работе с мобильными приложениями службы приложений Azure для приложений Xamarin.iOS | Microsoft Azure"
	description="Этот учебник поможет приступить к использованию мобильных приложений в разработке для Xamarin.iOS."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/13/2016"
	ms.author="normesta"/>


#Создание приложения Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Обзор

В этом учебнике рассказывается, как добавить облачную серверную службу в мобильное приложение Xamarin.iOS с помощью серверной части мобильного приложения Azure. Вы создадите новую серверную часть мобильного приложения и простое приложение Xamarin.iOS _Todo list_, в котором в Azure хранятся данные приложения.

Завершение этого учебника является необходимым условием для других учебников по Xamarin.iOS, посвященных использованию функции мобильных приложений в службе приложений Azure.

##Предварительные требования

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных приложений, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) или более поздняя версия. В случае установки Visual Studio Community 2013 устанавливайте [Xamarin] отдельно. При установке Visual Studio 2015 можно установить инструменты Xamarin.

* Компьютер Mac установленным ПО [Xcode] 7.0 или более поздней версии и [Xamarin Studio].

* Если вы планируете выполнить сборку и развертывание приложения на компьютере под управлением Windows с запущенной программой Visual Studio, вам все равно потребуется доступ к компьютеру под управлением Mac, подключенному к узлу сборки Xamarin.iOS. Дополнительные сведения см. в статье [Установка Xamarin.iOS в Windows](http://developer.xamarin.com/guides/ios/getting_started/installation/windows/).

>[AZURE.NOTE] Если вы хотите приступить к работе со службой приложений Azure до регистрации и получения учетной записи Azure, перейдите на страницу [Пробное использование службы приложений](https://tryappservice.azure.com/?appServiceName=mobile). Там вы сможете немедленно создать кратковременное начальное мобильное приложение в службе приложений. Для этого не потребуется ни кредитная карта, ни какие-либо обязательства.

## Создание серверной части мобильного приложения Azure

Чтобы создать серверную часть мобильного приложения, выполните указанные ниже действия.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Настройка серверного проекта

Итак, вы подготовили серверную часть мобильного Azure, которая может использоваться мобильными клиентскими приложениями. Теперь скачайте серверный проект со списком простых задач и опубликуйте его в Azure.

Выполните следующие действия, чтобы настроить серверный проект для использования серверной части .NET или Node.js.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]


## Локальное тестирование серверного проекта (необязательно)

Если выбрана приведенная выше конфигурация серверной части .NET, при необходимости вы можете выполнить локальное тестирование серверной части.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service](../../includes/app-service-mobile-dotnet-backend-test-local-service.md)]



## Скачивание и запуск приложения Xamarin.iOS

1. На компьютере Mac откройте [портал Azure] в окне браузера.

	>[AZURE.NOTE] Проще всего запустить приложение Xamarin.iOS на компьютере Mac. Запустить приложение Xamarin.iOS можно в Visual Studio на компьютере Windows, если хотите, но это немного сложнее, так как необходимо подключиться к компьютеру Mac с доступом к сети. Если вам это интересно, см. статью [Установка Xamarin.iOS в Windows].

2. В колонке параметров мобильного приложения щелкните **Приступая к работе** > **Xamarin.iOS**. На этапе 3 нажмите кнопку **Создать приложение** (если вы еще не сделали этого). Затем нажмите кнопку **Загрузить**.

  	Будет скачан проект, содержащий клиентское приложение, подключенное к вашему мобильному приложению. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

3. Извлеките скачанный проект и откройте его в Xamarin Studio (или Visual Studio).

	![][9]

	![][8]

4. Нажмите клавишу F5, чтобы выполнить сборку проекта и запустить приложение в эмуляторе iPhone.

5. В приложении введите содержательный текст, например _Изучение Xamarin_, и нажмите кнопку **+**.

	![][10]

	Отправится запрос POST к серверной части новой мобильной службы, размещенной в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются серверной частью мобильной службы, а данные отображаются в списке.

>[AZURE.NOTE]Код, который обращается к серверной части вашей мобильной службы для запроса и вставки данных, можно просмотреть в файле C# QSTodoService.cs.

##Дальнейшие действия

* [Добавление проверки подлинности в приложение](app-service-mobile-xamarin-ios-get-started-users.md) <br/>Узнайте, как проверять подлинность пользователей приложения с помощью поставщика удостоверений.

* [Добавление push-уведомлений в приложение](app-service-mobile-xamarin-ios-get-started-push.md) <br/>Узнайте, как отправлять в приложение простейшие push-уведомления.

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[портал Azure]: https://portal.azure.com/


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[Установка Xamarin.iOS в Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/

<!---HONumber=AcomDC_0224_2016-->