<properties 
	pageTitle="Проверка подлинности в приложениях API и мобильных приложениях в службе приложений Azure" 
	description="Узнайте, как настроить и использовать проверку подлинности для приложений API и мобильных приложений в службе приложений Azure." 
	services="app-service" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Проверка подлинности для приложений API и мобильных приложений в службе приложений Azure

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Обзор

В этой статье рассматриваются функции встроенной проверки подлинности для [приложений API](../app-service-api/app-service-api-apps-why-best-platform.md) и [мобильных приложений](../app-service-mobile/app-service-mobile-value-prop-preview.md).

В разделе [Дальнейшие действия](#next-steps) в конце статьи есть ссылки на связанные разделы с инструкциями.

## Шлюз службы приложений Azure

В службе приложений Azure имеются встроенные службы проверки подлинности, которые реализуют протоколы [OAuth 2.0](#oauth) и [OpenID Connect](#oauth) и взаимодействуют с разными *поставщиками удостоверений*. Поставщик удостоверений — это внешняя служба, являющаяся доверенной для службы приложений Azure, которая выполняет проверку подлинности пользователей вашего приложения. Служба приложений поддерживает наиболее популярных поставщиков удостоверений:

* Azure Active Directory
* Учетная запись Майкрософт
* Google
* Twitter
* Facebook

### Архитектура шлюза

В любой группе ресурсов Azure, которая содержит приложения API или мобильные приложения, есть *шлюз*. Шлюз представляет собой ресурс Azure, который выполняется в веб-приложении и реализует административные задачи, включая проверку подлинности для приложений API и мобильных приложений в группе ресурсов.

Шлюз управляет процессом входа в систему, маркерами и предотвращает неавторизованные вызовы, которые пытаются подключиться к приложениям API, [настроенным на авторизованный доступ](../app-service-api/app-service-api-dotnet-add-authentication.md#protect-the-api-app). Шлюз может управлять доступом к приложениям API, поскольку все входящие HTTP-запросы для приложений API в группе ресурсов проходят через шлюз.

На следующей схеме показана работа функций шлюза.

![](./media/app-service-authentication-overview/gateway.png)

### Функции шлюза

Службы проверки подлинности шлюза имеют ряд преимуществ по сравнению с реализацией собственного протокола OAuth 2.0.

* Корпорация Майкрософт предоставляет пакеты SDK, которые помогают реализовать проверку подлинности и задачи авторизации с помощью упрощенного синтаксиса.

* Поскольку служба приложений обрабатывает большое количество задач проверки подлинности, время, которое вы тратите на разработку и тестирование, сводится к минимуму, а вы сможете избежать практически всех последствий изменений поставщика реализации OAuth.

* При наличии нескольких приложений, которые необходимо защитить и внести в одну группу ресурсов, требуется один идентификатор и секрет клиента для каждого поставщика проверки подлинности, поскольку используется только один URL-адрес перенаправления для шлюза.

* Вести мониторинг и устранять неполадки проще, поскольку появляется возможность отслеживать трафик, связанный с проверкой подлинности, для целой группы ресурсов путем отслеживания шлюза.

* Отладка становится проще, поскольку можно настроить программу на использование шлюза во время работы в режиме отладки локально, при этом не придется изменять URL-адреса перенаправления в учетной записи поставщика удостоверения.

## Сравнение клиентского потока и серверного потока

Шлюз службы приложений обеспечивает два способа проверки подлинности клиентов: *клиентский поток* и *серверный поток*. В обоих потоках клиентское приложение отправляет пользовательские учетные данные (обычно это имя пользователя и пароль) напрямую поставщику удостоверений. В обоих потоках ни шлюз, ни приложение не получают пользовательские учетные данные.

### Клиентский поток

Клиентский поток означает, что клиентское приложение взаимодействует напрямую с поставщиком удостоверений для получения маркера доступа поставщика. Клиентское приложение отправляет маркер доступа поставщика в шлюз. Шлюз создает и отправляет клиенту маркер контекста пользователя. Этот маркер контекста пользователя также называется маркером Zumo — по первоначальному имени кода для [мобильных служб Azure](/documentation/services/mobile-services/). (Службы проверки подлинности для приложений API и мобильных приложений построены на той же архитектуре, что и мобильные службы.)

Поток показан на схеме ниже.

![](./media/app-service-authentication-overview/clientflow.png)

Затем клиент предоставляет маркер Zumo в HTTP-запросах, когда обращается к защищенным приложениям API или мобильным приложениям. Шлюз хранит маркеры поставщика и отслеживает, какие из них связаны с теми или иными маркерами Zumo.

### Серверный поток

Серверный поток означает, что клиентское приложение предоставляет шлюзу взаимодействовать с поставщиком удостоверений, когда необходимо выполнить вход. Клиентский браузер переходит по URL-адресу шлюза, и шлюз перенаправляет запрос на страницу входа поставщика удостоверений. После входа пользователя шлюз получает маркер поставщика удостоверений, создает маркер Zumo и отправляет его клиенту.

![](./media/app-service-authentication-overview/serverflow.png)

Последующее взаимодействие между клиентским приложением и защищенными приложениями API или мобильными приложениями обрабатывается так же, как клиентский поток: клиент предоставляет маркер Zumo в HTTP-запросах.

### Выбор между клиентским потоком и серверным потоком

Клиентский поток обычно является предпочтительным вариантом, если поставщик удостоверений предоставляет пакет SDK для клиентской платформы, которую необходимо поддерживать. Клиентский поток обеспечивает удобную работу пользователей, поскольку уменьшает количество раз, когда пользователь должен вводить учетные данные. Например, если пользователь имеет устройство на базе Android, у него, вероятно, есть учетная запись Google, связанная с устройством. Если пользователь может использовать ее и не вводить повторно имя пользователя и пароль, это упрощает работу. То же самое касается учетной записи Facebook на устройствах на базе Android, iOS или Windows Phone либо учетной записи Майкрософт на компьютере или телефоне на базе Windows.

В других ситуациях серверный поток может быть более предпочтительным.

- При его использовании отсутствует пакет Native Client SDK для поставщика удостоверений и клиентская платформа, которую необходимо поддерживать.

- Нужно быстро запустить в производство какое-либо приложение и улучшать работу по мере возможности в будущем. Серверный поток позволяет службе приложений Azure выполнить большую часть работы по проверке подлинности и сводит к минимуму объем разработки и тестирования, которые нужно выполнить.

## Именные исходящие вызовы для платформ SaaS

Чтобы делать исходящие вызовы для платформ SaaS от имени вошедшего в систему пользователя, можно написать код либо использовать [соединитель приложения API](../app-service-mobile/app-service-logic-what-are-biztalk-api-apps.md). Например, чтобы отправить твит из учетной записи пользователя Twitter, вы можете использовать [пакет SDK Twitter](https://dev.twitter.com/overview/api/twitter-libraries) или подготовить [соединитель Twitter](../app-service-mobile/app-service-logic-connector-twitter.md) в подписке Azure и использовать его. В этом разделе рассматривается доступ к платформе SaaS из кода, выполняемого в приложении API или мобильном приложении.

### <a id="obotoidprovider"></a>Использование маркера поставщика удостоверений 

Шлюз имеет *хранилище маркеров*, в котором он связывает маркер Zumo с одним или несколькими маркерами доступа поставщиков удостоверений и обновляет маркеры. При получении HTTP-запроса с допустимым маркером Zumo шлюз знает, какие маркеры поставщика удостоверений принадлежат данному пользователю.
  
Когда код, работающий в приложении API или мобильном приложении, вызывает защищенный ресурс от имени авторизованного пользователя, код может получить и использовать маркер поставщика удостоверений из хранилища маркеров шлюза, как показано на следующей схеме. Схема предполагает, что клиент уже прошел проверку подлинности, выполняемую шлюзом, и получил маркер Zumo.

![](./media/app-service-authentication-overview/idprovidertoken.png)

Предположим, что поставщиком удостоверений является Azure Active Directory (AAD) и приложению API нужно использовать его маркер доступа для вызова API Graph или нужно запросить доступ к сайту SharePoint, для которого у пользователя есть разрешение. Вы можете отправить запрос шлюзу на получение маркера AAD, затем использовать этот маркер для вызова API Graph или получения маркера доступа для сайта SharePoint.

### <a id="obotosaas"></a>Получение согласия пользователя на доступ к другим ресурсам

Шлюз также имеет встроенные функции для получения согласия пользователя, когда необходимо получить доступ к ресурсам, защищенным поставщиком, не являющимся исходным поставщиком удостоверений. Например, для пользователя, который выполняет вход с помощью Azure Active Directory, может потребоваться доступ к файлам в учетной записи Dropbox.

Шлюз службы приложений имеет встроенную поддержку для получения согласия пользователя на такой доступ с помощью следующих поставщиков:

* Box
* DropBox
* Facebook
* Google
* Office365
* OneDrive
* QuickBooks
* Salesforce
* SharePointOnline
* Twitter
* Yammer
* Azure Active Directory
* Учетная запись Майкрософт

Для этих поставщиков шлюз хранит маркеры доступа и связывает их с маркером Zumo так же, как и в случае с маркером доступа поставщика удостоверений. Процесс получения согласия пользователя и вызов платформы SaaS показан на следующей схеме. Схема предполагает, что клиент уже прошел проверку подлинности, выполняемую шлюзом, и получил маркер Zumo.

![](./media/app-service-authentication-overview/saastoken.png)

Поддержка среды выполнения службы приложений и пакеты SDK позволяют относительно легко написать код, который обращается к защищенным ресурсам с помощью одного из этих поставщиков. Есть один предварительный этап, который не показан на схеме: нужно настроить учетную запись у поставщика и указать идентификатор и секрет клиента в службе приложений Azure.

При использовании этих и многих других поставщиков также можно получать доступ к защищенным ресурсам с помощью встроенного [соединителя приложения API](../app-service-mobile/app-service-logic-what-are-biztalk-api-apps.md).

## Доступность пакета SDK

Для приложений API пакет SDK для .NET обеспечивает возможности проверки подлинности.

* [Microsoft.Azure.AppService](http://www.nuget.org/packages/Microsoft.Azure.AppService) — для использования в клиенте приложения API.  
* [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/) — для использования в проекте веб-API, который выполняется в приложении API.
 
Кроме того, Visual Studio может автоматически создавать код, который работает с пакетом SDK для .NET и упрощает код, который нужно написать для вызова приложения API. Дополнительные сведения см. в разделе [Использование приложения API в службе приложений Azure из клиента .NET](../app-service-api/app-service-api-dotnet-consume.md).

Для мобильных приложений пакеты SDK доступны для следующих платформ:

- [.NET Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) 
- [iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-preview.md)
- [Xamarin iOS](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md)
- [Xamarin Android](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md)
- [Windows](../app-service-mobile/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md)
- JavaScript (учебник по разработке)

## Другие методы проверки подлинности

Если службы проверки подлинности шлюза не отвечают требованиям вашего приложения, можно самостоятельно реализовать проверку подлинности или использовать службу управления API Azure.

### <a id="doityourself"></a>Самостоятельная проверка подлинности

В Azure можно использовать методы проверки подлинности, такие как [ASP.NET Identity](http://www.asp.net/identity) или [Thinktecture](http://www.thinktecture.com/identityAndAccessControl). У вас есть возможность контролировать, что и как работает, однако придется потратить время на разработку и тестирование функций проверки подлинности. Кроме того, при наличии нескольких приложений, которые нужно защитить с помощью нескольких URL-адресов перенаправления, придется настроить несколько идентификаторов и секретов клиента для сторонних поставщиков проверки подлинности, таких как Facebook, Google и Twitter.

В настоящее время служба приложений не поддерживает самостоятельное решение вместе с проверкой подлинности с помощью шлюза, хотя это возможно в [мобильных службах](mobile-services-dotnet-backend-get-started-custom-authentication.md).

### <a id="apim"></a>Служба управления API Azure

При наличии интерфейсов API, которые требуется защитить с помощью проверки подлинности, это можно сделать с помощью службы управления API Azure. Дополнительные сведения об использовании службы управления API Azure с приложениями API см. в блоге Паноса Кефальдиса (Panos Kefaldis): [Преимущества службы управления API для приложений API](http://www.kefalidis.me/2015/06/taking-advantage-of-api-management-for-api-apps/)

## Дальнейшие действия

В этой статье были рассмотрены службы проверки подлинности, которые служба приложений Azure позволяет использовать для приложений API и мобильных приложений. Ниже приводятся несколько ссылок на ресурсы для изучения базовых протоколов проверки подлинности и компонентов проверки подлинности службы приложений.

* [OAuth 2.0, OpenID Connect и JSON Web Tokens (JWT)](#oauth)
* Ресурсы приложений API
	* [Клиентский поток приложений API](#apiaclient)
	* [Серверный поток приложений API](#apiaserver)
	* [Именные вызовы приложений API](#apiaobo)
* Ресурсы для мобильных приложений
	* [Клиентский поток мобильных приложений](#maclient)
	* [Серверный поток мобильных приложений](#maserver)
	* [Именные вызовы для мобильных приложений](#maobo)

### <a id="oauth"></a>OAuth 2.0, OpenID Connect и JSON Web Tokens (JWT)

* [Приступая к работе с протоколом OAuth 2.0](http://shop.oreilly.com/product/0636920021810.do "Приступая к работе с протоколом OAuth 2.0") 
* [Общие сведения о протоколах OAuth2, OpenID Connect и JSON Web Tokens (JWT), курс PluralSight](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction) 
* [Построение и защита RESTful API для нескольких клиентов в ASP.NET, курс PluralSight](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

### <a id="apiaclient"></a>Клиентский поток приложений API

* [Защита приложения API](../app-service-api/app-service-api-dotnet-add-authentication.md) — настройка приложения API применима как к клиентскому, так и к серверному потоку, однако тестовая часть в браузере иллюстрирует серверный поток.
* [Использование приложения API в службе приложений Azure из клиента .NET](../app-service-api/app-service-api-dotnet-consume.md) — пример приложения с вызовом, прошедшим проверку подлинности, иллюстрирует вариант серверного потока. За ним следует раздел, содержащий пример кода для варианта [клиентского потока](../app-service-api/app-service-api-dotnet-consume.md#client-flow).

### <a id="apiaserver"></a>Серверный поток приложений API

* [Защита приложения API](../app-service-api/app-service-api-dotnet-add-authentication.md) — раздел, посвященный настройке параметров приложения API, относится как к клиентскому, так и к серверному потоку. Раздел, посвященный тестированию в браузере, иллюстрирует серверный поток.
* [Использование приложения API в службе приложений Azure из клиента .NET](../app-service-api/app-service-api-dotnet-consume.md) — образец кода для авторизованного запроса иллюстрирует серверный поток. 

### <a id="apiaobo"></a>Именные вызовы приложений API

* [Развертывание и настройка соединителя API для приложения SaaS в службе приложений Azure](../app-service-api/app-service-api-connnect-your-app-to-saas-connector.md) — показывает подготовку встроенного соединителя приложения API, его настройку и вызов с помощью средств браузера.
* [Подключение к платформе SaaS из приложения API ASP.NET в службе приложений Azure](../app-service-api/app-service-api-dotnet-connect-to-saas.md) — описано, как написать собственный соединитель, включая подготовку, настройку и написание кода для пользовательского приложения API, осуществляющего вызовы поставщика SaaS от другого лица.

### <a id="maclient"></a>Клиентский поток для мобильных приложений

* [Добавление единого входа Azure Active Directory в приложение iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-aad-sso-preview.md)

### <a id="maserver"></a>Серверный поток для мобильных приложений

* [Добавление проверки подлинности в приложение iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-users-preview.md)
* [Добавление проверки подлинности в приложение Xamarin.iOS](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md)
* [Добавление проверки подлинности в приложение Xamarin.Android](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-users-preview.md)
* [Добавление проверки подлинности в приложение Windows](../app-service-mobile/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md)

### <a id="maobo"></a>Именные запросы мобильных приложений к защищенным ресурсам

* [Получение маркера доступа и вызов API SharePoint в мобильном приложении](../app-service-mobile/app-service-mobile-dotnet-backend-get-started-connect-to-enterprise.md#obtain-token)

<!---HONumber=AcomDC_0204_2016-->