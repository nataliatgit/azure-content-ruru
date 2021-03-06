<properties
	pageTitle="Изменения в приложениях API службы приложений | Microsoft Azure"
	description="Узнайте о новых возможностях приложений API в службе приложений Azure."
	services="app-service\api"
	documentationCenter=".net"
	authors="mohitsriv"
	manager="wpickett"
	editor="tdykstra"/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/13/2016"
	ms.author="mohisri"/>

# Изменения в приложениях API службы приложений

На мероприятии Connect(), состоявшемся в ноябре 2015 года, было [объявлено](https://azure.microsoft.com/blog/azure-app-service-updates-november-2015/) о ряде улучшений в службе приложений Azure. К ним относятся базовые изменения в приложениях API для лучшей работы с мобильными и веб-приложениями, использование меньшего количества концепций, а также повышение эффективности развертывания и среды выполнения. Соответствующие изменения в создании приложений API с использованием портала управления Azure и нового инструментария вступили в силу 30 ноября 2015 года. В этой статье описаны эти изменения, а также то, как развернуть существующие приложения заново, чтобы использовать все преимущества предоставленных возможностей.


> [AZURE.NOTE] В исходной предварительной версии поддерживалось два основных сценария использования приложений API: 1) использование настраиваемых API в приложениях логики или своих клиентах; 2) использование API Marketplace (зачастую для соединителей программного обеспечения как услуги) в приложениях логики. Эта статья посвящена первому сценарию, т. е. сценарию для настраиваемых интерфейсов API. На начало 2016 года планируется улучшение работы в конструкторе приложений логики, а также оптимизация основных возможностей подключения для API Marketplace. Существующие API Marketplace по прежнему будут доступны в конструкторе приложений логики.

## Изменения функций
Такие ключевые функции приложений API, как проверка подлинности, CORS и использование метаданных API, теперь перемещены прямо в службу приложений. Благодаря этому изменению ключевые функции стали доступными в веб- и мобильных приложениях, а также приложениях API. По факту в диспетчере ресурсов все они используют ресурс одного и того же типа **Microsoft.Web/sites**. Приложениям API больше не нужен шлюз, поэтому они предоставляются без него. Это также упрощает управление API Azure, так как теперь нужно использовать всего один шлюз.

![Обзор приложений API](./media/app-service-api-whats-changed/api-apps-overview.png)

Основной принцип проектирования, заложенный в обновлении приложений API, состоит в том, чтобы можно было использовать API «как есть» на желаемом языке. Если вы уже развернули API как веб- или мобильное приложение*, чтобы использовать преимущества новых функций, не нужно развертывать приложение заново.

> [AZURE.NOTE] *Если вы используете предварительную версию приложений API, подробное руководство по миграции можно просмотреть ниже.

### Проверка подлинности
Существующие и уже готовые функции проверки подлинности для приложений API, мобильных служб, а также веб-приложений теперь унифицированы и доступны в единой колонке проверки подлинности службы приложений Azure на портале управления. Общие сведения о проверке подлинности в службе приложений см. в разделе [Развертывание проверки подлинности и авторизации в службе приложений](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/).

Сценарии использования API предоставляют множество новых возможностей:

- **Прямое использование Azure Active Directory** без кода клиента для обмена токена AAD на токен сеанса. Согласно спецификации токена носителя клиенту достаточно включить токены AAD в заголовок авторизации. Это также означает, что на стороне клиента или сервера больше не требуется устанавливать пакет SDK для конкретной службы приложений. 
- **Взаимодействие между службами или внутренний доступ.** Чтобы пройти проверку подлинности в приложении при использовании управляющей программы или другого клиента, для работы которых обязателен доступ к API без интерфейса, можно запросить токен с помощью субъекта-службы AAD и передать его в службу приложений.
- **Отложенная авторизация.** Во множестве приложений для разных частей приложения есть различные ограничения доступа. Вам может понадобиться, чтобы одни интерфейсы API были общедоступными, а для использования других требовалось выполнение входа. Изначально проверка подлинности и авторизация осуществлялись согласно одному подходу, требующему входа в приложение. Это по-прежнему возможно, но теперь для принятия решений в отношении доступа, когда служба приложений уже выполнила проверку подлинности пользователя, можно использовать код приложения.
 
Дополнительные сведения о новых функциях проверки подлинности см. в статье [Проверка подлинности и авторизация для приложений API в службе приложений Azure](app-service-api-authentication.md). Сведения о миграции существующих приложений API из предыдущей модели приложений API в новую см. в разделе [Перенос существующих приложений API](#migrating-existing-api-apps) далее в этой статье.
 
### CORS
Теперь для настройки CORS используется колонка на портале управления Azure, а не параметр приложения **MS\_CrossDomainOrigins** со значениями, разделенными запятыми. Также приложение можно настроить с помощью таких средств диспетчера ресурсов, как Azure PowerShell, интерфейс командной строки или [обозреватель ресурсов](https://resources.azure.com/). Задайте типу ресурса **Microsoft.Web/sites/config** для ресурса **&lt;site name&gt;/web** свойство **cors**. Например:

    {
        "cors": {
            "allowedOrigins": [
                "https://localhost:44300"
            ]
        }
    } 

### Метаданные API
Колонка определения API теперь доступна в веб-, мобильных приложениях, а также приложениях API. На портале управления можно указать относительный или абсолютный URL-адрес конечной точки, в которой размещено представление Swagger 2.0 вашего API. Кроме того, возможна настройка с помощью инструментария диспетчера ресурсов. Задайте типу ресурса **Microsoft.Web/sites/config** для ресурса **&lt;site name&gt;/web** свойство **apiDefinition**. Например:

    {
        "apiDefinition":
        {
            "url": "https://myStorageAccount.blob.core.windows.net/swagger/apiDefinition.json"
        }
    }

Сейчас нужна общедоступная конечная точка метаданных, не требующая проверки подлинности, которую будут использовать подчиненные клиенты (например, для создания REST API Visual Studio и потока «Добавить API» PowerApps). Это значит, что если используется проверка подлинности службы приложений и необходимо предоставить определение API из самого приложения, вам нужно использовать отложенную проверку подлинности, описанную выше, чтобы маршрут к метаданным Swagger был общедоступен.

## Портал управления
Выбрав на портале **Создать > Интернет+мобильные устройства > Приложение API**, вы создадите приложения API со всеми новыми возможностями, описанными в этой статье. Выбрав **Обзор > Приложения API**, вы только отобразите эти новые приложения API. Когда вы входите в приложение API, колонка использует тот же макет и возможности, что используют веб- и мобильные приложения. Единственное отличие заключается в содержимом быстрого запуска и порядке параметров.

Существующие приложения API (или приложения API Marketplace, созданные из приложений логики) с возможностями предварительной версии, доступными ранее, по-прежнему будут отображаться в конструкторе приложений логики и при просмотре всех ресурсов в группе. Если все же необходимо создать приложение API с возможностями предварительной версии, которые были доступны ранее, используйте пакет, который можно найти на сайте Azure Marketplace, выбрав **Интернет+мобильные устройства > Приложения API (предварительная версия)**.

## Visual Studio

Большая часть средств для веб-приложений совместимо с новыми приложениями API, так как они используют один и тот же базовый тип ресурсов **Microsoft.Web/sites**. Тем не менее, инструментарий Azure Visual Studio следует обновить до версии 2.8.1 или более поздней, так как это предоставит ряд возможностей в зависимости от API. Скачайте пакет SDK на [странице загрузок Azure](https://azure.microsoft.com/downloads/).

В ходе рационализации типов службы приложений также унифицирована функция публикации, которую можно выполнить, выбрав **Публикация > Служба приложений Microsoft Azure**:

![Публикация приложений API](./media/app-service-api-whats-changed/api-apps-publish.png)

Дополнительные сведения о пакете SDK 2.8.1 см. в объявлении в [записи блога](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/).

Кроме того, профиль публикации можно импортировать вручную на портале управления, чтобы включить публикацию. Однако, чтобы использовать Cloud Explorer, создавать код и выбирать или создавать приложения API, необходимо установить пакет SDK 2.8.1 или более поздней версии.

Возможность публикации в существующих приложениях API с возможностями предварительной версии, доступными ранее, сохранится при использовании пакета SDK 2.8.1. Если проект уже опубликован, выполнять какие-либо дополнительные действия не нужно. Чтобы настроить публикацию, в диалоговом окне публикации в раскрывающемся списке **Дополнительные параметры** выберите пункт **Приложения API (классические)**.

## Миграция существующих приложений API
Если настраиваемый API развернут в предыдущей предварительной версии приложений API, до 31 декабря 2015 г. нужно перенести эти приложения в новую модель. Так как и старая, и новая модель используют веб-API в службе приложений, основную часть существующего кода можно использовать снова.

### Размещение и повторное развертывание
Шаги повторного развертывания такие же, как при развертывании любого существующего веб-API в службе приложений. Шаги:

1. Создайте пустое приложение API. Это можно сделать на портале, выбрав «Создать > Приложение API», с использованием публикации в Visual Studio или инструментария диспетчера ресурсов. При использовании шаблонов и средств диспетчера ресурсов задайте параметру **kind** типа ресурса **Microsoft.Web/sites** значение **api**. Это приведет быстрый запуск и параметры на портале управления в соответствие со сценариями API.
2. Подключите проект к пустому приложению API и разверните его, используя любой механизм развертывания, который поддерживает служба приложений. Дополнительные сведения см. в [документации по развертыванию службы приложений Azure](../app-service-web/web-sites-deploy.md). 
  
### Проверка подлинности
Службы проверки подлинности службы приложений поддерживают те же возможности, что и предыдущая модель приложений API. Если вы используете токены сеансов и вам необходимы пакеты SDK, используйте следующие пакеты для клиента и сервера:

- Для клиента: [пакет Azure SDK для мобильного клиента](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).
- Для сервера: [расширение проверки подлинности .NET для мобильных приложений Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/). 

Если же вы используете альфа-версию пакета SDK для службы приложений, такие пакеты являются устаревшими:

- [пакет SDK для службы приложений Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.AppService) для клиента;
- Для сервера: [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service).

Для Azure Active Directory не нужна специальная служба приложений, если вы используете токен AAD напрямую.

### Внутренний доступ
Предыдущая модель приложений API предусматривала встроенный уровень внутреннего доступа. При этом было необходимо использовать пакет SDK для подписи запросов. Как описано ранее в этой статье, субъект-службу AAD можно использовать с новой моделью приложений API в качестве альтернативного метода проверки подлинности с взаимодействием между службами, и при этом не нужно устанавливать специальный пакет SDK для службы приложений. Дополнительные сведения см. в статье [Проверка подлинности с использованием субъекта-службы для приложений API в службе приложений Azure](app-service-api-dotnet-service-principal-auth.md).

### Обнаружение
В предыдущей модели приложений API был API для обнаружения других приложений API в среде выполнения в той же группе ресурсов для одного шлюза. Это особенно полезно в архитектурах, где реализуются шаблоны микрослужб. Хотя эта возможность не поддерживается напрямую, есть несколько доступных вариантов.

1. Используйте API Диспетчера ресурсов Azure для обнаружения.
2. Разместите службу управления API Azure перед API, размещенными в службе приложений. Служба управления API Azure выступает в качестве интерфейсной части и может предоставлять постоянные URL-адреса, доступные извне приложения, даже при изменении внутренней топологии.
3. Вы можете создать собственное приложение API для обнаружения и использовать его для регистрации других приложений при запуске.
4. Во время развертывания задайте для параметров всех приложений API (и клиентов) значения конечных точек других приложений API. Это эффективно в шаблонах развертывания, так как теперь приложения API позволяют управлять URL-адресами.

### Приложения логики
В начале 2016 года конструктор приложений логики будет добавлен для сверхпрозрачной интеграции с новой моделью приложений API. Вместе с тем соединитель HTTP, встроенный в приложения логики, может вызывать любые конечные точки HTTP и поддерживает проверку подлинности с использованием субъекта-службы, которую изначально поддерживают службы проверки подлинности службы приложений. Сведения о том, как в приложениях логики использовать API, размещенные в службе приложений, см. в статье [Использование настраиваемого интерфейса API, размещенного в службе приложений, с приложениями логики](../app-service-logic/app-service-logic-custom-hosted-api.md).

### <a id="documentation"></a> Документация по предыдущей модели приложений API
Некоторые статьи на сайте [azure.microsoft.com](https://azure.microsoft.com/), написанные для старой модели приложений API, больше не применяются к новой модели и будут удалены с сайта. URL-адреса таких статей будут перенаправляться на наиболее релевантные ресурсы, которые применимы к новой модели. Но вы по-прежнему можете просматривать старые статьи в [документации репозитория GitHub для azure.microsoft.com](https://github.com/Azure/azure-content). Большинство статей можно будет найти в папке [articles/app-service-api](https://github.com/Azure/azure-content/tree/master/articles/app-service-api). Ниже приведены ссылки на некоторые из статей, которые будут актуальными, если вы пользуетесь более старыми приложениями API или создаете новый соединитель приложения API из Marketplace.

* [Общие сведения о проверке подлинности](https://github.com/Azure/azure-content/tree/master/articles/app-service/app-service-authentication-overview.md)
* [Защита приложения API](https://github.com/Azure/azure-content/tree/master/articles/app-service-api/app-service-api-dotnet-add-authentication.md)
* [Использование внутреннего приложения API](https://github.com/Azure/azure-content/tree/master/articles/app-service-api/app-service-api-dotnet-consume-internal.md)
* [Использование проверки подлинности клиентского потока](https://github.com/Azure/azure-content/tree/master/articles/app-service-api/app-service-api-authentication-client-flow.md)
* [Развертывание и настройка приложения API соединителя SaaS](https://github.com/Azure/azure-content/tree/master/articles/app-service-api/app-service-api-connnect-your-app-to-saas-connector.md)
* [Подготовка приложения API с помощью нового шлюза](https://github.com/Azure/azure-content/tree/master/articles/app-service-api/app-service-api-arm-new-gateway-provision.md)
* [Отладка приложения API](https://github.com/Azure/azure-content/tree/master/articles/app-service-api/app-service-api-dotnet-debug.md)
* [Подключение к платформе SaaS](https://github.com/Azure/azure-content/tree/master/articles/app-service-api/app-service-api-dotnet-connect-to-saas.md)
* [Усовершенствование приложения API для приложений логики](https://github.com/Azure/azure-content/tree/master/articles/app-service-api/app-service-api-optimize-for-logic-apps.md)
* [Триггеры приложения API](https://github.com/Azure/azure-content/tree/master/articles/app-service-api/app-service-api-dotnet-triggers.md)

## Дальнейшие действия
Дополнительную информацию см. в статьях в [разделе документации по приложениям API](https://azure.microsoft.com/documentation/services/app-service/api/). В эти статьи уже добавлены сведения о новой модели для приложений API. Кроме того, используйте форумы, чтобы получить дополнительные сведения или рекомендации по миграции:

- [Форум MSDN](https://social.msdn.microsoft.com/Forums/ru-RU/home?forum=AzureAPIApps)
- [Переполнение стека](http://stackoverflow.com/questions/tagged/azure-api-apps)

<!---HONumber=AcomDC_0128_2016-->