<properties
	pageTitle="Начало работы с мобильными службами Azure для приложений iOS | Серверная часть JavaScript"
	description="Следуйте указаниям этого учебника, чтобы приступить к использованию мобильных служб Azure для разработки приложений iOS."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="02/10/2016"
	ms.author="krisragh"/>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Аналогичные сведения для мобильных приложений см. в статье [Создание приложения iOS в мобильных приложениях Azure](../app-service-mobile/app-service-mobile-android-get-started.md).

В этом учебнике показано, как добавить облачную серверную службу в приложение iOS с помощью мобильных служб Azure.

В этом учебнике вы создадите новую мобильную службу и простое приложение _To do list_, хранящее данные приложения в новой мобильной службе. Мобильная служба, которая будет создана, использует JavaScript для бизнес-логики на стороне сервера. Сведения о создании мобильной службы с серверной бизнес-логикой в .NET см. в разделе [Серверная версия .NET] этой статьи.

> [AZURE.NOTE] Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить [бесплатные мобильные службы, которые можно использовать и после окончания пробного периода](https://azure.microsoft.com/pricing/details/mobile-services/). Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-RU%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20).

## <a name="create-new-service"> </a>Создание мобильной службы

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Создание нового приложения для iOS

Для создания нового приложения, подключенного к мобильной службе, можно использовать раздел быстрого запуска на классическом портале Azure:

1. На [классическом портале Azure] щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

2. На вкладке быстрого запуска в разделе **Выбор платформы** щелкните элемент **iOS**, а затем разверните раздел **Создание нового приложения iOS**. Здесь показаны действия для создания приложения на платформе iOS, подключенного к вашей мобильной службе.

3. Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItem**.

4. В разделе **Загрузка и запуск приложения** щелкните **Загрузить**. При этом загружается проект для примера приложения _To do list_, которое подключается к вашей мобильной службе, а также пакет Mobile Services SDK для iOS. Сохраните сжатый файл проекта на локальный компьютер и запомните путь к нему.

## Запуск нового приложения для iOS

[AZURE.INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]

<ol start="4"> <li><p>На [классическом портале Azure] щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItem**. Это позволяет просматривать данные, добавленные в таблицу приложением.<p></li></ol></p>

## <a name="next-steps"> </a>Дальнейшие действия
Узнайте, как выполнять дополнительные важные задачи в мобильных службах:

* [Приступая к работе с синхронизацией автономных данных] <br/>Узнайте, как использовать синхронизацию автономных данных для повышения скорости реагирования и надежности приложений.

* [Добавление проверки подлинности в приложение] <br/>Узнайте, как проверять подлинность пользователей приложения с помощью поставщика удостоверений.

* [Добавление push-уведомлений в приложение iOS и серверную часть .NET] <br/>Узнайте, как отправлять в приложение базовые push-уведомления.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]


<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Приступая к работе с синхронизацией автономных данных]: mobile-services-ios-get-started-offline-data.md
[Добавление проверки подлинности в приложение]: mobile-services-dotnet-backend-ios-get-started-users.md
[Добавление push-уведомлений в приложение iOS и серверную часть .NET]: mobile-services-dotnet-backend-ios-get-started-push.md


[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[классическом портале Azure]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Серверная версия .NET]: mobile-services-dotnet-backend-ios-get-started.md

<!---HONumber=AcomDC_0309_2016-->