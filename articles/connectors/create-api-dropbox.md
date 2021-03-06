<properties
	pageTitle="Добавление API Dropbox в службу PowerApps Enterprise или приложения логики | Microsoft Azure"
	description="Обзор API Dropbox с параметрами интерфейса API REST"
	services=""
    suite=""
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
   ms.date="03/02/2016"
   ms.author="mandia"/>

# Начало работы с API Dropbox 
Подключение к Dropbox позволяет управлять файлами, например: создавать файлы, получать их и т. д. API Dropbox можно использовать из:

- приложений логики; 
- PowerApps.

> [AZURE.SELECTOR]
- [Приложения логики](../articles/connectors/create-api-dropbox.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-dropbox.md)

С помощью Dropbox можно:

- формировать бизнес-процессы на основе данных, получаемых из Dropbox; 
- использовать триггеры при создании или обновлении файла;
- использовать действия по созданию, удалению файлов и т. д. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, новый файл, созданный в Dropbox, можно отправить по электронной почте с помощью Office 365.
- Добавьте API Dropbox в службу PowerApps Enterprise. После этого пользователи смогут использовать этот API в своих приложениях. 

Сведения о добавлении API в PowerApps Enterprise см. в статье [Регистрация API в PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Сведения о добавлении операции в приложения логики см. в статье [Создание приложения логики](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Триггеры и действия
Dropbox предоставляет следующие триггеры и действия.

триггеры; | Действия
--- | ---
<ul><li>При создании файла</li><li>При изменении файла</li></ul> | <ul><li>Создание файла</li><li>При создании файла</li><li>Копирование файла</li><li>Удаление файла</li><li>Извлечение архива в папку</li><li>Получение содержимого файла с помощью идентификатора</li><li>Получение файла с помощью пути</li><li>Получение метаданных файла с помощью идентификатора</li><li>Получение метаданных файла с помощью пути</li><li>Обновление файла</li><li>При изменении файла</li></ul>

Все API-интерфейсы поддерживают данные в форматах JSON и XML.

## Создание подключения к Dropbox

### Добавление дополнительных настроек в PowerApps
При добавлении Dropbox в PowerApps Enterprise нужно ввести значения **ключа приложения** и **секрета приложения** для приложения Dropbox. В приложении Dropbox также используется **URL-адрес перенаправления**. Если у вас нет приложения Dropbox, создайте его, выполнив приведенные ниже действия:

1. Войдите в [Dropbox][1].
2. Перейдите на сайт разработчика Dropbox и выберите **Мои приложения**: ![сайт разработчика Dropbox][8]  
3. Щелкните **Создать приложение**: ![создание приложения Dropbox][9]  
4. В окне **Создание нового приложения на платформе Dropbox**:  

	1. В поле **Choose API** (Выбор API) выберите пункт **Dropbox API**.
	2. В поле **Choose the type of access you need** (Выберите необходимый тип доступа) выберите пункт **Full Dropbox…** (Весь Dropbox…).  
	3. Введите имя своего приложения.  

	![создание приложения Dropbox, страница 1][10]

5. На странице настройки приложения:

	1. В поле **OAuth 2** введите значение **URL-адреса перенаправления**, которое отображалось при добавлении API Dropbox на портале Azure. Выберите **Добавить**.  
	2. Щелкните ссылку **Show** (Показать), чтобы показать **секрет приложения**.  

	![создание приложения Dropbox, страница 2][11]

Скопируйте и вставьте значения **ключа приложения** и **секрета приложения** в конфигурацию Dropbox на портале Azure.

### Добавление дополнительных настроек в приложения логики
При добавлении API в приложения логики эти приложения необходимо авторизовать для подключения к Dropbox.

1. Войдите в учетную запись Dropbox.
2. Выберите **Авторизовать** и разрешите приложениям логики подключаться к Dropbox и использовать его. 

После создания подключения укажите свойства Dropbox, такие как путь к папке или имя файла. Эти свойства описаны далее в **справочнике по REST API**.

>[AZURE.TIP] Подключение к Dropbox можно также использовать в других приложениях логики.

## Справочник по REST API Swagger
Относится к версии 1.0.

### Создание файла    
Передает файл в Dropbox. ```POST: /datasets/default/files```

| Имя| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|folderPath|строка|Да|запрос|Нет |Путь к папке для отправки файла в Dropbox|
|name|строка|Да|запрос|Нет |Имя файла, создаваемого в Dropbox|
|body|string(binary) |Да|текст|Нет |Содержимое файла, отправляемого в Dropbox|

#### Ответ
|Имя|Описание|
|---|---|
|200|ОК|
|по умолчанию|Операция завершилась ошибкой.|


### При создании файла    
Активирует поток при создании файла в папке Dropbox. ```GET: /datasets/default/triggers/onnewfile```

| Имя| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|folderId|string|Да|запрос|Нет |Уникальный идентификатор папки в Dropbox|

#### Ответ
|Имя|Описание|
|---|---|
|200|ОК|
|по умолчанию|Операция завершилась ошибкой.|


### Копирование файла    
Копирует файл в Dropbox. ```POST: /datasets/default/copyFile```

| Имя| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|источник|string|Да|запрос|Нет |URL-адрес исходного файла|
|ресурс destination|строка|Да|запрос| Нет|Путь к конечному файлу в Dropbox, включая имя конечного файла|
|перезаписать|Логическое|Нет|запрос|Нет |Перезаписывает конечный файл, если задано значение "true"|

#### Ответ
|Имя|Описание|
|---|---|
|200|ОК|
|по умолчанию|Операция завершилась ошибкой.|


### Удаление файла    
Удаляет файл из Dropbox. ```DELETE: /datasets/default/files/{id}```

| Имя| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|id|строка|Да|path|Нет|Уникальный идентификатор файла, удаляемого из Dropbox|

#### Ответ
|Имя|Описание|
|---|---|
|200|ОК|
|по умолчанию|Операция завершилась ошибкой.|


### Извлечение архива в папку    
Извлекает файл архива в папку в Dropbox (например: ZIP-файл). **```POST: /datasets/default/extractFolderV2```**

| Имя| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|источник|string|Да|запрос|Нет |Путь к файлу архива|
|ресурс destination|строка|Да|запрос|Нет |Путь в Dropbox для извлечения содержимого архива|
|перезаписать|Логическое|Нет|запрос|Нет |Перезаписывает конечные файлы, если задано значение "true"|

#### Ответ
|Имя|Описание|
|---|---|
|200|ОК|
|по умолчанию|Операция завершилась ошибкой.|


### Получение содержимого файла с помощью идентификатора    
Получает содержимое файла из Dropbox с помощью идентификатора. ```GET: /datasets/default/files/{id}/content```

| Имя| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|id|строка|Да|path|Нет |Уникальный идентификатор файла в Dropbox|

#### Ответ
|Имя|Описание|
|---|---|
|200|ОК|
|по умолчанию|Операция завершилась ошибкой.|


### Получение содержимого файла с помощью пути    
Получает содержимое файла из Dropbox с помощью пути. ```GET: /datasets/default/GetFileContentByPath```

| Имя| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|path|строка|Да|запрос|Нет |Уникальный путь к файлу в Dropbox|

#### Ответ
|Имя|Описание|
|---|---|
|200|ОК|
|по умолчанию|Операция завершилась ошибкой.|


### Получение метаданных файла с помощью идентификатора    
Извлекает метаданные файла из Dropbox с помощью идентификатора файла. ```GET: /datasets/default/files/{id}```

| Имя| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|id|строка|Да|path|Нет |Уникальный идентификатор файла в Dropbox|

#### Ответ
|Имя|Описание|
|---|---|
|200|ОК|
|по умолчанию|Операция завершилась ошибкой.|


### Получение метаданных файла с помощью пути    
Извлекает метаданные файла из Dropbox с помощью пути. ```GET: /datasets/default/GetFileByPath```

| Имя| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|path|строка|Да|запрос|Нет |Уникальный путь к файлу в Dropbox|

#### Ответ
|Имя|Описание|
|---|---|
|200|ОК|
|по умолчанию|Операция завершилась ошибкой.|


### Обновление файла    
Обновляет файл в Dropbox. ```PUT: /datasets/default/files/{id}```

| Имя| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|id|строка|Да|path| Нет|Уникальный идентификатор файла, обновляемого в Dropbox|
|body|string(binary) |Да|текст|Нет |Содержимое файла, обновляемого в Dropbox|

#### Ответ
|Имя|Описание|
|---|---|
|200|ОК|
|по умолчанию|Операция завершилась ошибкой.|


### При изменении файла    
Активирует поток при изменении файла в папке Dropbox. ```GET: /datasets/default/triggers/onupdatedfile```

| Имя| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|folderId|string|Да|запрос|Нет |Уникальный идентификатор папки в Dropbox|

#### Ответ
|Имя|Описание|
|---|---|
|200|ОК|
|по умолчанию|Операция завершилась ошибкой.|


## Определения объектов

#### DataSetsMetadata

|Имя свойства | Тип данных | Обязательно|
|---|---|---|
|tabular|Не определен|Нет|
|blob-объект|Не определен|Нет|

#### TabularDataSetsMetadata

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|источник|строка|Нет|
|displayName|строка|Нет|
|urlEncoding|string|Нет|
|tableDisplayName|string|Нет|
|tablePluralName|string|Нет|

#### BlobDataSetsMetadata

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|источник|строка|Нет|
|displayName|строка|Нет|
|urlEncoding|string|Нет|

#### BlobMetadata

|Имя свойства | Тип данных |Обязательно|
|---|---|---|
|Идентификатор|string|Нет|
|Имя|строка|Нет|
|DisplayName|строка|Нет|
|Путь|string|Нет|
|LastModified|string|Нет|
|Размер|целое число|Нет|
|MediaType|строка|Нет|
|IsFolder|Логическое|Нет|
|ETag|строка|Нет|
|FileLocator|строка|Нет|

## Дальнейшие действия
После добавления API Dropbox в PowerApps Enterprise [предоставьте пользователям разрешения](../power-apps/powerapps-manage-api-connection-user-access.md) на использование API в своих приложениях.

[Создание приложения логики](../app-service-logic/app-service-logic-create-a-logic-app.md).


<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[8]: ./media/create-api-dropbox/dropbox-developer-site.png
[9]: ./media/create-api-dropbox/dropbox-create-app.png
[10]: ./media/create-api-dropbox/dropbox-create-app-page1.png
[11]: ./media/create-api-dropbox/dropbox-create-app-page2.png

<!---HONumber=AcomDC_0302_2016-->