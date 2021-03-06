<properties 
    pageTitle="Подключение DocumentDB к службе Поиск Azure с помощью индексаторов | Microsoft Azure" 
    description="В этой статье показано, как использовать индексатор службы поиска Azure с DocumentDB в качестве источника данных."
    services="documentdb" 
    documentationCenter="" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="mimig"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="rest-api" 
    ms.topic="article" 
    ms.tgt_pltfrm="NA" 
    ms.workload="data-services" 
    ms.date="02/01/2016" 
    ms.author="anhoh"/>

#Подключение DocumentDB к службе поиска Azure с помощью индексаторов

Если необходимо реализовать эффективные возможности поиска в данных DocumentDB, рекомендуется использовать индексатор службы поиска Azure для DocumentDB. В этой статье будет показано, как интегрировать Azure DocumentDB со службой поиска Azure без написания кода для поддержки инфраструктуры индексирования.

Для этого необходимо [настроить учетную запись службы Поиск Azure](../search/search-get-started.md#start-with-the-free-service) (не требуется выполнять обновление до стандартного поиска), а затем вызвать [REST API службы Поиск Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) для создания **источника данных** DocumentDB и **индексатора** для него.

##<a id="Concepts"></a>Понятия индексатора в службе Поиск Azure

Служба поиска Azure поддерживает создание и управление источниками данных (включая DocumentDB) и индексаторами, работающими в этих источниках данных.

**Источник данных** определяет, какие данные нужно индексировать, какие учетные данные требуются для доступа к данным и какие политики нужны, чтобы служба Поиск Azure могла эффективно выявлять изменения в данных (такие как измененные или удаленные документы в коллекции). Источник данных определяется как независимый ресурс, который может использоваться несколькими индексаторами.

**Индексатор** описывает процесс передачи данных из источника данных в целевой индекс поиска. Для каждой комбинации целевого индекса и источника данных необходимо запланировать создание одного индексатора. Несмотря на то, что можно использовать несколько индексаторов, выполняющих запись в тот же индекс, индексатор может выполнять запись только в один индекс. Индексатор используется для выполнения следующих задач:

- однократное копирование данных для заполнения индекса;
- Для синхронизации индекса с изменениями в источнике данных по расписанию. Расписание является частью определения индексатора.
- Вызов по требованию обновлений индекса. 

##<a id="CreateDataSource"></a>Шаг 1. Создание источника данных

Вызовите запрос HTTP POST для создания источника данных в службе поиска Azure, включая следующие заголовки запроса.
    
    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

Параметр `api-version` является обязательным. Допустимые значения — `2015-02-28` или более поздняя версия.

Текст запроса содержит определение источника данных, который должен включать следующие поля.

- **name**: имя источника данных.

- **type**: используйте `documentdb`.

- **credentials**:

    - **connectionString**: обязательное поле. Укажите сведения о подключении к базе данных Azure DocumentDB в следующем формате: `AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`

- **container**:

    - **name**: обязательное поле. Укажите коллекцию DocumentDB, которая будет индексироваться. 

    - **query**: необязательное поле. Можно указать запрос на сведение произвольного документа JSON в неструктурированную схему, индексируемую поиском Azure.

- **dataChangeDetectionPolicy**: необязательное поле. Ознакомьтесь с разделом [Политика обнаружения изменения данных](#DataChangeDetectionPolicy) ниже.

- **dataDeletionDetectionPolicy**: необязательное поле. Ознакомьтесь с разделом [Политика обнаружения удаления данных](#DataDeletionDetectionPolicy) ниже.

###<a id="DataChangeDetectionPolicy"></a>Запись измененных документов

Политика обнаружения изменения данных предназначена для эффективного определения измененных элементов данных. В настоящее время единственной поддерживаемой политикой является политика `High Water Mark`, использующая свойство последней измененной отметки времени `_ts`, предоставленное DocumentDB. Эта политика указывается следующим образом.

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts" 
    } 

Кроме того, потребуется добавить `_ts` в проекцию и предложение `WHERE` для запроса. Например:

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark


###<a id="DataDeletionDetectionPolicy"></a>Запись удаленных документов

Строки, удаляемые из исходной таблицы, также следует удалить из индекса поиска. Политика обнаружения удаления данных предназначена для эффективного определения удаленных элементов данных. В настоящее время единственной поддерживаемой политикой является политика `Soft Delete` (удаление помечается особым флагом), которая указывается следующим образом:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a document as deleted" 
    }

> [AZURE.NOTE] При использовании пользовательской проекции в предложение SELECT потребуется включить свойство.

###<a id="CreateDataSourceExample"></a>Пример тела запроса

В следующем примере создается источник данных с настраиваемым запросом и подсказками.

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark" 
        },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

###Ответ

Если источник данных был успешно создан, вы получите ответ HTTP 201 — Создано.

##<a id="CreateIndex"></a>Шаг 2. Создание индекса

Создайте целевой индекс поиска Azure, если это еще не сделано. Создать индекс можно с помощью [пользовательского интерфейса портала Azure](../search/search-get-started.md#test-service-operations) или [API создания индекса](https://msdn.microsoft.com/library/azure/dn798941.aspx).

	POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
	Content-Type: application/json
	api-key: [Search service admin key]


Убедитесь, что схема целевого индекса совместима с исходными документами JSON или выходными данными настраиваемой проекции запроса.

###Рисунок А. Сопоставление типов данных JSON и типов данных службы Поиск Azure

| ТИП ДАННЫХ JSON|	СОВМЕСТИМЫЕ ТИПЫ ПОЛЕЙ ЦЕЛЕВОГО ИНДЕКСА|
|---|---|
|Bool|Edm.Boolean, Edm.String|
|Числа, которые выглядят как целые числа|Edm.Int32, Edm.Int64, Edm.String|
|Числа, которые выглядят как числа с плавающей запятой|Edm.Double, Edm.String|
|Строка|Edm.String|
|Массивы типов-примитивов, например a, b, c |Collection(Edm.String)|
|Строки, которые выглядят как даты| Edm.DateTimeOffset, Edm.String|
|Например, { "тип": "Точка", "координаты": [ долгота, широта ] } | Edm.GeographyPoint |
|Другие объекты JSON|Недоступно|

###<a id="CreateIndexExample"></a>Пример тела запроса

В следующем примере создается индекс с идентификатором и полем описания.

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

###Ответ

Если индекс был успешно создан, вы получите ответ HTTP 201 — Создано.

##<a id="CreateIndexer"></a>Шаг 3. Создание индексатора

Можно создать индексатор в службе поиска Azure с помощью запроса HTTP POST со следующими заголовками.
    
    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

Текст запроса содержит определение индексатора, которое должно включать следующие поля.

- **name**: обязательное поле. Имя индексатора.

- **dataSourceName**: обязательное поле. Имя существующего источника данных.

- **targetIndexName**: обязательное поле. Имя существующего индекса.

- **schedule**: необязательное поле. Ознакомьтесь с разделом [Расписание индексирования](#IndexingSchedule) ниже.

###<a id="IndexingSchedule"></a>Выполнение индексаторов по расписанию

Индексатор может дополнительно задавать расписание. Если расписание уже имеется, индексатор будет выполняться согласно расписанию. Расписание имеет следующие атрибуты.

- **interval**: обязательное поле. Значение длительности, указывающее интервал или период между запусками индексатора. Наименьший допустимый интервал — 5 минут, наибольший — один день. Значение должно быть отформатировано как значение dayTimeDuration XSD (ограниченное подмножество значения [продолжительности ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)). Используется следующий шаблон: `P(nD)(T(nH)(nM))`. Примеры: `PT15M` для каждых 15 минут, `PT2H` для каждых 2 часов. 

- **startTime**: обязательное поле. Параметр UTC datetime, указывающий время начала выполнения индексатора.

###<a id="CreateIndexerExample"></a>Пример тела запроса

В следующем примере создается индексатор, который копирует данные из коллекции, на которую ссылается источник данных `myDocDbDataSource`, в индекс `mySearchIndex` по расписанию, начинающемуся 1 января 2015 г. Периодичность выполнения — ежечасно.

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

###Ответ

Если указатель был успешно создан, вы получите ответ HTTP 201 — Создано.

##<a id="RunIndexer"></a>Шаг 4. Запуск индексатора

Помимо периодического выполнения по расписанию, индексатор можно вызвать по запросу, выполнив следующий запрос HTTP POST.

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

###Ответ

Если индексатор успешно был вызван, вы получите ответ HTTP 202 — Принято.

##<a name="GetIndexerStatus"></a>Шаг 5. Получение состояния индексатора

Для получения текущего состояния и истории выполнения индексатора можно выполнить запрос HTTP GET.

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

###Ответ

Вместе с ответом HTTP 200 — ОК будет возвращен текст ответа, содержащий сведения о общем состоянии работоспособности индексатора, последнем вызове индексатора, а также истории последних вызовов индексатора (при их наличии).

Ответ должен выглядеть так:

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

История выполнения включает не более 50 последних завершенных выполнений, которые сортируются в обратном хронологическом порядке (то есть в ответе первым отображается последнее выполнение).

##<a name="NextSteps"></a>Дальнейшие действия

Поздравляем! Вы только что узнали, как интегрировать Azure DocumentDB со службой поиска Azure с помощью индексатора для DocumentDB.

 - Дополнительные сведения об Azure DocumentDB см. на странице документации по [службе DocumentDB](https://azure.microsoft.com/services/documentdb/).

 - Дополнительные сведения о поиске Azure см. на странице документации по [службе поиска](https://azure.microsoft.com/services/search/).
 

<!---HONumber=AcomDC_0204_2016-->