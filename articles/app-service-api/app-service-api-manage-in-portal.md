<properties 
	pageTitle="Управление приложениями API" 
	description="Узнайте, как управлять приложениями API в службе приложений Azure с помощью портала Azure и обозревателя сервера Visual Studio." 
	services="app-service\api" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na"
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Управление приложениями API в службе приложений Azure

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

В этой статье описано, как использовать [портал предварительной версии Azure](https://portal.azure.com/) для выполнения задач управления приложением API и его мониторинга. Ниже приведены некоторые задачи, которые вы можете выполнить.

- Настройка аутентификации 
- Включение автомасштабирования
- Просмотр журналов
- Просмотр количества выполняемых запросов и размера данных, которые используются приложением API
- Резервное копирование приложения API и создание предупреждений
- Настройка безопасности доступа на основе ролей

В статье также показано, как выполнять некоторые задачи управления в окне обозревателя решений в Visual Studio.

## Колонки «Приложение API» и «Шлюз» на портале предварительной версии Azure

В службе приложений Azure приложение API является [веб-приложением](../app-service-web/app-service-web-overview.md) с дополнительными возможностями для размещения веб-служб. На портале Azure есть колонка **Приложение API** для управления функциями API, а также колонка **Хост приложений API** для управления базовым веб-приложением.

Каждая группа ресурсов, содержащая хотя бы одно приложение API, также включает *шлюз*. Шлюз выступает в качестве прокси-сервера, выполняя аутентификацию и другие административные функции для всех приложений API в группе ресурсов. Как и приложение API, шлюз является веб-приложением с дополнительными функциональными возможностями, поэтому на портале существует две колонки для управления шлюзом: колонка **Шлюз** для функций шлюза и колонка **Узел шлюза** для управления базовым веб-приложением.

### Задачи, которые можно выполнить только в колонке «Приложение API»

Колонку **Приложение API** можно использовать для выполнения следующих задач.

- Настройка уровня доступа: щелкните элементы **Параметры > Параметры приложения**. Значение по умолчанию является внутренним, что означает, что вызывать приложения API могут только приложения API из той же группы ресурсов. Дополнительные сведения см. в разделе [Защита приложения API](app-service-api-dotnet-add-authentication.md).   
- Настройка политики обновления: щелкните элементы **Параметры > Параметры приложения**. Значение по умолчанию — **Включено**. Это означает, что при публикации в Marketplace новой версии приложения API ваше приложение API будет автоматически обновлено до новой версии, если это изменение является некритическим.  
- Настройка проверки подлинности для исходящих вызовов от приложения API: щелкните элементы **Параметры > Проверка подлинности**. Если приложение API делает вызов внешней службы, которая требует проверки подлинности, значения требуемой конфигурации вводятся здесь. Например, для соединителя Dropbox требуется идентификатор клиента и секрет клиента для доступа к службе Dropbox.
- Настройка [RBAC](../role-based-access-control-configure.md): щелкните элементы **Параметры > Пользователи**. Настраиваемый здесь доступ пользователей определяет только то, кто может получать доступ к функциям приложения API. Для настройки RBAC для функции веб-приложения используйте колонку **Узел приложения API**. Обычно рекомендуется синхронизировать параметры RBAC для приложения API и узла приложения API. Если дать пользователю разрешение на доступ к приложению API, но не дать разрешение на доступ к узлу приложения API, он не сможет использовать функции колонки **Приложение API**, которые в действительности относятся к узлу приложения API.
- Просмотр определения API: щелкните элемент **Определение API** в разделе **Сводка**, чтобы просмотреть список методов, предоставляемых приложением API.
- [Установка диспетчера гибридных соединений](../app-service-logic/app-service-logic-hybrid-connection-manager.md). Диспетчер гибридного подключения предоставляет возможность подключения к локальной системе, такой как SQL Server или SAP. Это гибридное подключение использует служебную шину Azure для подключения и управления безопасностью ресурсов Azure и локальных ресурсов.

### Задачи, которые можно выполнять в колонке «Приложение API» и в колонке «Хост приложений API» 

Колонка **Приложение API** позволяет выполнять множество задач, которые относятся к базовому веб-приложению. Например, вы можете выполнять следующие задачи.

* Останавливать, запускать или перезапускать веб-приложение, в котором размещается приложение API.  
- Добавлять разные метрики производительности, включая известные коды ошибок, такие как коды состояния HTTP 200, 400 или 500, щелкнув **Запросы и ошибки**.
- Просматривать время ответа, количество запросов, отправленных в приложение API, и объем исходящих или входящих данных. 
- Настраивать получение оповещений по электронной почте, если метрика превышает выбранное пороговое значение. 
- Просматривать коэффициент использования **ЦП** приложением API, текущую **квоту использования** в МБ и максимальный объем используемых данных на уровне затрат.
- Определять возможные расходы на запуск приложения API, просматривая **Приблизительные расходы**.
- Просматривать журналы приложений и другие журналы IIS, включая журналы веб-сервера и журналы FREB.
- Открывать обозреватель процессов, выбрав **Процессы**. В нем отображаются веб-экземпляры и их свойства, включая число потоков и использование памяти.

Но эти задачи можно также выполнить с помощью колонки **Хост приложений API**. Именно поэтому эти две колонки имеют практически одинаковый пользовательский интерфейс. Например, кнопки **Остановить**, **Запуск** и **Перезапустить** в колонке **Приложение API** имеют те же функции, что и кнопки **Остановить**, **Запуск** и **Перезапустить<** в колонке **Хост приложений API**. Аналогичным образом, данные мониторинга в колонке **Приложение API** совпадают с данными колонки **Узел приложения API**.

Функции, которые есть в колонке **Приложение API** и которые не дублируется в колонке **Узел приложения API**, перечислены в предыдущем разделе.

### Задачи, которые можно выполнить только в колонке «Хост приложений API»

Колонку **Хост приложений API** можно использовать для всех задач, которые можно выполнять для любого веб-приложения.

### Задачи, которые можно выполнить только в колонке «Шлюз»

Колонку **Шлюз** можно использовать для выполнения следующих задач.

- Конфигурация поставщика проверки подлинности для входящих вызовов приложения API: щелкните элементы **Параметры > Идентификация**. Если шлюзу необходимо выполнить проверку подлинности пользователей перед предоставлением им разрешения для вызова приложения API в группе ресурсов, необходимые значения конфигурации вводятся здесь. Дополнительные сведения см. в разделе [Настройка и тестирование соединителя SaaS в службе приложений Azure](app-service-api-connnect-your-app-to-saas-connector.md). 
- Настройка [RBAC](../role-based-access-control-configure.md): щелкните элементы **Параметры > Пользователи**. Настраиваемый здесь доступ определяет пользователей, которые могут получать доступ к функциям шлюза, а не к функциям всех веб-приложений.

### Задачи, которые можно выполнять в колонке «Шлюз» и в колонке «Узел шлюза» 

В колонках «Шлюз» и «Узел шлюза» используется одинаковый пользовательский интерфейс, точно так же как в колонках «Приложение API» и «Хост приложений API».

### Задачи, которые можно выполнить только в колонке «Узел шлюза»

Колонку **Узел шлюза** можно использовать для всех задач, которые можно выполнять для любого веб-приложения.

## <a id="navigate"></a>Как перейти к колонкам «Приложение API» и «Шлюз» 

Одним из способов доступа к колонке **Приложение API** является использование функции просмотра на портале Azure. На домашней странице портала щелкните элементы **Обзор > Приложения API** для просмотра всех приложений API, которыми можно управлять.

![](./media/app-service-api-manage-in-portal/browse.png)

![](./media/app-service-api-manage-in-portal/apiappslist.png)

### Переход к колонке приложения API

Если щелкнуть строку в колонке списка **Приложения API**, на портале отображаются колонки **Приложение API**.

![](./media/app-service-api-manage-in-portal/apiappblade.png)

### Переход к колонке узла приложения API

Чтобы перейти к колонке **Узел приложения API**, щелкните элемент **Узел приложения API** в колонке **Приложение API**.

![](./media/app-service-api-manage-in-portal/apiappbladetohost.png)

![](./media/app-service-api-manage-in-portal/apiapphostbladenocallouts.png)

### Переход к колонке шлюза

Чтобы перейти к колонке **Шлюз**, щелкните ссылку **Шлюз** в колонке **Приложение API**.
   
![](./media/app-service-api-manage-in-portal/apiappbladegotogateway.png)

![](./media/app-service-api-manage-in-portal/gatewaybladenocallout.png)

### Переход к колонке узла шлюза

Чтобы перейти к колонке **Узел шлюза**, щелкните ссылку **Узел шлюза** в колонке **Шлюз**.
   
![](./media/app-service-api-manage-in-portal/gatewaybladetohost.png)

![](./media/app-service-api-manage-in-portal/gatewayhost.png)

## Доступ к приложениям API в обозревателе сервера в Visual Studio

В **обозревателе сервера** в Visual Studio можно запускать сеанс удаленной отладки, просматривать журналы потоковой передачи и выбирать пункт меню, который открывает колонку «Приложение API» на портале.

Чтобы получить доступ к приложению API в обозревателе сервера, щелкните **Azure > Служба приложений > [имя группы ресурсов] > [имя приложения API]**, как показано на рисунке.

![](./media/app-service-api-manage-in-portal/se.png)

## Дальнейшие действия

В этой статье было показано, как использовать портал Azure для выполнения задач управления приложениями API. Для приложений API, устанавливаемых из коллекции приложений API, см. также статью [Управление и мониторинг встроенных приложений API и соединителей](../app-service-logic/app-service-logic-monitor-your-connectors.md).

Дополнительную информацию об управлении приложениями API с помощью командной строки см. в статьях раздела меню **Автоматизация**, которое отображается в левой части статьи (в широких окнах браузера) или в верхней части статьи (в узких окнах).

<!---HONumber=AcomDC_0114_2016-->