<properties
	pageTitle="Добавление API Facebook в приложения логики | Microsoft Azure"
	description="Обзор API Facebook с параметрами интерфейса API REST"
	services=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/25/2016"
   ms.author="mandia"/>

# Начало работы с API Facebook
Подключение к Facebook позволяет оставлять публикации в хронике, получать канал страниц и выполнять другие действия. API Facebook можно использовать из:

- приложений логики. 

>[AZURE.NOTE] Эта версия статьи предназначена для приложений логики со схемой версии 2015-08-01-preview. Чтобы узнать версию схемы 2014-12-01-preview, щелкните [соединитель Facebook](../app-service-logic/app-service-logic-connector-facebook.md).


С помощью Facebook можно:

- формировать бизнес-процессы на основе данных, получаемых из Facebook; 
- использовать триггер при получении новой публикации;
- использовать действия, оставляющие публикации в хронике, получающие канал страниц и выполняющие другие действия. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, можно настроить трансляцию всех публикаций, которые появляются в хронике, в Twitter. 

Сведения о добавлении операции в приложения логики см. в статье [Создание приложения логики](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Триггеры и действия
API Facebook предоставляет следующие триггеры и действия.

| триггеры; | Действия|
| --- | --- |
| <ul><li>При появлении новой публикации в хронике</li></ul> |<ul><li>Получение веб-канала из хроники</li><li>Публикация в хронике</li><li>При появлении новой публикации в хронике</li><li>Получение веб-канала страниц</li><li>Получение публикаций в хронике пользователя</li><li>Публикация на странице</li></ul>

Все API-интерфейсы поддерживают данные в форматах JSON и XML.

## Создание подключения к Facebook
При добавлении API в приложения логики эти приложения необходимо авторизовать для подключения к Facebook.

1. Вход в учетную запись Facebook
2. Выберите **Авторизовать** и разрешите приложениям логики подключаться к Facebook и использовать его. 

После создания подключения нужно ввести свойства Facebook. Эти свойства описаны далее в **справочнике по REST API**.

>[AZURE.TIP] Подключение к Facebook можно также использовать в других приложениях логики.

## Справочник по REST API Swagger
Относится к версии 1.0.

### Получение потока из хроники
Получает веб-канал из хроники вошедшего в систему пользователя. ```GET: /me/feed```

| Имя|Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|fields|string|Нет|запрос|Нет |Укажите поля, которые необходимо вернуть. Пример (идентификатор, имя, фото).|
|ограничение|целое число|Нет|запрос| Нет|Максимальное количество публикаций для извлечения|
|на|строка|Нет|запрос| Нет|Ограничение списка публикаций только теми, в которых указано местонахождение.|
|фильтр|string|Нет|запрос| Нет|Получение только тех публикаций, которые соответствуют определенному фильтру потока.|

#### Ответ
|Имя|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|500|Внутренняя ошибка сервера|
|по умолчанию|Операция завершилась ошибкой.|


### Публикация в хронике
Публикует сообщение о состоянии в хронике пользователя, вошедшего в систему. ```POST: /me/feed```

| Имя|Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|post|string |Да|текст|Нет |Новое сообщение для публикации|

#### Ответ
|Имя|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|500|Внутренняя ошибка сервера|
|по умолчанию|Операция завершилась ошибкой.|


### При появлении новой публикации в хронике
Активирует новый поток, когда в хронике пользователя, вошедшего в систему, появляется новая публикация. ```GET: /trigger/me/feed```

Параметров нет.

#### Ответ
|Имя|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|500|Внутренняя ошибка сервера|
|по умолчанию|Операция завершилась ошибкой.|


### Получение канала страниц
Получает сообщения из веб-канала указанной страницы. ```GET: /{pageId}/feed```

| Имя|Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|pageId|string|Да|path| Нет|Идентификатор страницы, с которой нужно получать сообщения.|
|ограничение|целое число|Нет|запрос| Нет|Максимальное количество публикаций для извлечения|
|include\_hidden|Логическое|Нет|запрос|Нет |Включение или отключение скрытых страницей публикаций|
|fields|string|Нет|запрос|Нет |Укажите поля, которые необходимо вернуть. Пример (идентификатор, имя, фото).|

#### Ответ
|Имя|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|500|Внутренняя ошибка сервера|
|по умолчанию|Операция завершилась ошибкой.|


### Получение временной шкалы пользователя
Получает публикации из хроники пользователя. ```GET: /{userId}/feed```

| Имя|Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|userId|строка|Да|path|Нет |Идентификатор пользователя, хронику которого нужно извлечь.|
|ограничение|целое число|Нет|запрос|Нет |Максимальное количество публикаций для извлечения|
|на|строка|Нет|запрос|Нет |Ограничение списка публикаций только теми, в которых указано местонахождение.|
|фильтр|string|Нет|запрос| Нет|Получение только тех публикаций, которые соответствуют определенному фильтру потока.|
|fields|string|Нет|запрос| Нет|Укажите поля, которые необходимо вернуть. Пример (идентификатор, имя, фото).|

#### Ответ
|Имя|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|500|Внутренняя ошибка сервера|
|по умолчанию|Операция завершилась ошибкой.|


### Публикация на странице
Публикует сообщение на странице Facebook от имени вошедшего в систему пользователя. ```POST: /{pageId}/feed```

| Имя|Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|pageId|string|Да|path|Нет |Идентификатор страницы для публикации.|
|post|many |Да|текст|Нет |Новое сообщение для публикации.|

#### Ответ
|Имя|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|500|Внутренняя ошибка сервера|
|по умолчанию|Операция завершилась ошибкой.|


## Определения объектов

#### GetFeedResponse

|Имя свойства | Тип данных | Обязательно|
|---|---|---|
|data|array|Нет|

#### TriggerFeedResponse

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|data|array|Нет|

#### PostItem: отдельная запись в канале профиля
В качестве профиля может выступать пользователь, страница, приложение или группа.

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|id|string|Нет|
|admin\_creator|array|Нет|
|caption|строка|Нет|
|created\_time|строка|Нет|
|description|строка|Нет|
|feed\_targeting|не определено|Нет|
|from|не определено|Нет|
|icon|string|Нет|
|is\_hidden|Логическое|Нет|
|is\_published|Логическое|Нет|
|link|строка|Нет|
|message|строка|Нет|
|name|строка|Нет|
|object\_id|строка|Нет|
|picture|строка|Нет|
|place|не определено|Нет|
|privacy|не определено|Нет|
|properties|array|Нет|
|источник|строка|Нет|
|status\_type|строка|Нет|
|story|string|Нет|
|targeting|не определено|Нет|
|значение|array|Нет|
|type|string|Нет|
|updated\_time|строка|Нет|
|with\_tags|не определено|Нет|

#### TriggerItem: отдельная запись в канале профиля
В качестве профиля может выступать пользователь, страница, приложение или группа.

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|id|string|Нет|
|created\_time|строка|Нет|
|from|не определено|Нет|
|message|строка|Нет|
|type|string|Нет|

#### AdminItem

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|id|string|Нет|
|link|строка|Нет|

#### PropertyItem

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|name|строка|Нет|
|text|string|Нет|
|href|string|Нет|

#### UserPostFeedRequest

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|message|строка|Да|
|link|строка|Нет|
|picture|строка|Нет|
|name|строка|Нет|
|caption|строка|Нет|
|description|строка|Нет|
|place|строка|Нет|
|tags|строка|Нет|
|privacy|не определено|Нет|
|object\_attachment|string|Нет|

#### PagePostFeedRequest

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|message|строка|Да|
|link|строка|Нет|
|picture|строка|Нет|
|name|строка|Нет|
|caption|строка|Нет|
|description|строка|Нет|
|actions|array|Нет|
|place|строка|Нет|
|tags|строка|Нет|
|object\_attachment|string|Нет|
|targeting|не определено|Нет|
|feed\_targeting|не определено|Нет|
|published|Логическое|Нет|
|scheduled\_publish\_time|string|Нет|
|backdated\_time|string|Нет|
|backdated\_time\_granularity|строка|Нет|
|child\_attachments|array|Нет|
|multi\_share\_end\_card|Логическое|Нет|

#### PostFeedResponse

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|id|string|Нет|

#### ProfileCollection

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|data|array|Нет|

#### UserItem

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|id|string|Нет|
|first\_name|string|Нет|
|last\_name|string|Нет|
|name|строка|Нет|
|gender|строка|Нет|
|about|string|Нет|

#### ActionItem

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|name|строка|Нет|
|link|строка|Нет|

#### TargetItem

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|countries|array|Нет|
|locales|array|Нет|
|regions|array|Нет|
|cities|array|Нет|

#### FeedTargetItem: объект, управляющий каналом новостей для этой публикации
Пользователи в этих группах, скорее всего, увидят эту публикацию, остальные с меньшей вероятностью. Применяется только к страницам.

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|countries|array|Нет|
|regions|array|Нет|
|cities|array|Нет|
|age\_min|целое число|Нет|
|age\_max|целое число|Нет|
|genders|array|Нет|
|relationship\_statuses|array|Нет|
|interested\_in|array|Нет|
|college\_years|array|Нет|
|interests|array|Нет|
|relevant\_until|целое число|Нет|
|education\_statuses|array|Нет|
|locales|array|Нет|

#### PlaceItem

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|id|string|Нет|
|name|строка|Нет|
|overall\_rating|number|Нет|
|location|не определено|Нет|

#### LocationItem

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|city|string|Нет|
|country|string|Нет|
|latitude|number|Нет|
|located\_in|строка|Нет|
|longitude|number|Нет|
|name|строка|Нет|
|region|строка|Нет|
|state|строка|Нет|
|street|строка|Нет|
|zip|строка|Нет|

#### PrivacyItem

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|description|строка|Нет|
|value|строка|Да|
|allow|string|Нет|
|deny|строка|Нет|
|friends|string|Нет|

#### ChildAttachmentsItem

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|link|строка|Нет|
|picture|строка|Нет|
|image\_hash|string|Нет|
|name|строка|Нет|
|description|строка|Нет|

#### PostPhotoRequest

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|url|строка|Да|
|caption|строка|Нет|

#### PostPhotoResponse

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|id|строка|Да|
|post\_id|string|Да|

#### PostVideoRequest

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|videoData|строка|Да|
|description|строка|Да|
|title|string|Да|
|uploadedVideoName|string|Нет|

#### GetPhotoResponse

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|data|не определено|Да|

#### GetPhotoResponseItem

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|url|строка|Да|
|is\_silhouette|Логическое|Да|
|height|string|Нет|
|width|строка|Нет|

#### GetEventResponse

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|data|array|Да|

#### GetEventResponseItem

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|id|строка|Да|
|name|строка|Да|
|start\_time|строка|Нет|
|end\_time|string|Нет|
|timezone|строка|Нет|
|location|строка|Нет|
|description|строка|Нет|
|ticket\_uri|string|Нет|
|rsvp\_status|string|Да|


## Дальнейшие действия

[Создание приложения логики](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0302_2016-->