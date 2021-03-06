<properties 
	pageTitle="Настройка индексатора поиска Azure | Microsoft Azure | Размещенная облачная служба поиска" 
	description="Узнайте, как настроить параметры и политики индексаторов в Поиске Azure, размещенной облачной службе поиска в Microsoft Azure." 
	services="search" 
	documentationCenter="" 
	authors="chaosrealm" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="02/04/2016" 
	ms.author="eugenesh"/>

#Настройка индексатора поиска Azure

Настройка индексатора в службе "Поиск Azure" позволит вам переименовывать поля между источником данных и целевым индексом, преобразовывать строки из таблицы базы данных в коллекции строк, переключать политику результатов обнаружения в источнике данных, кодировать в URL-адрес ключи документа, которые содержат знаки, небезопасные для URL-адреса, а также допускать сбои при индексировании некоторых документов.

Если вы не знакомы с индексаторами поиска Azure, возможно, вы захотите сначала ознакомиться со следующими статьями:

- [Подключение базы данных SQL Azure к Поиску Azure с помощью индексаторов](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [Подключение DocumentDB к службе поиска Azure с помощью индексаторов](../documentdb/documentdb-search-indexer.md)
- [Пакет SDK для .NET с поддержкой индексаторов](https://msdn.microsoft.com/library/dn951165.aspx) или 
- [Справочник по API REST индексаторов](https://msdn.microsoft.com/library/azure/dn946891.aspx)

##Переименование полей между источником данных и целевым индексом##

**Сопоставления полей** являются свойствами, которые согласовывают различия между определениями полей. К наиболее распространенным примерам можно отнести в имена полей, которые нарушают правила именования Поиска Azure. Рассмотрим исходную таблицу, в которой одно или несколько имен полей начинается с символа подчеркивания (например, `_id`). В Поиске Azure запрещено использовать имена полей, начинающиеся с символа подчеркивания, поэтому поле необходимо переименовать.

В следующем примере показано обновление индексатора для включения сопоставления полей, которое «переименовывает» поле `_id` источника данных в поле `id` в целевом индексе:

	PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
    } 

**ПРИМЕЧАНИЕ.** Необходимо использовать предварительную версию API 2015-02-28-Preview, чтобы использовать сопоставления полей.

Можно задать несколько сопоставлений полей.

	"fieldMappings" : [ 
		{ "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
	 ]

В исходных и целевых именах полей не учитывается регистр.

##Преобразование строк из таблицы базы данных в коллекции строк##

Сопоставления полей также можно использовать для преобразования исходных значений полей, используя *функции сопоставления*.

Одна такая функция, `jsonArrayToStringCollection`, анализирует поле, содержащее строку в формате массива JSON, и преобразовывает его в поле Collection(Edm.String) в целевом индексе. В частности, эта функция предназначена для использования с индексатором SQL Azure, поскольку в SQL отсутствует собственные тип данных коллекции. Эта функция используется следующим образом.

	"fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

Например, если исходное поле содержит строку `["red", "white", "blue"]`, то целевое поле типа `Collection(Edm.String)` будет заполнено следующими тремя значениями: `"red"`, `"white"` и `"blue"`.

Обратите внимание, что свойство `targetFieldName` является необязательным; если не указать его, используется значение `sourceFieldName`.

##Переключение политики обнаружения изменений по источнику данных##
  
В Поиске Azure выбор политики обнаружения изменений главным образом определяется тем, что поддерживается источником данных и схемой данных. Со временем, особенно в случае обновления или переноса баз данных, может потребоваться переключиться на другой тип политики обнаружения изменений. Например, вы только что обновили базу данных SQL Azure до более новой версии, поддерживающий интегрированное отслеживание изменений, и теперь хотите сменить политику верхнего предела на интегрированную политику отслеживания изменений. Или, возможно, вы решили использовать другой столбец в качестве верхнего предела.

Если просто вызвать PUT API REST для источника данных, чтобы для обновить его, можно получить 400 ответов с сообщением об ошибке следующего вида:


	"Change detection policy cannot be changed for data source '…' because indexer '…' references this data source and has a non-empty change tracking state. You can use Reset API to reset the indexer's change tracking state, and retry this call."

 Скорее всего, вам будет интересно знать, что это значит и как устранить проблему. Эта ошибка возникает, так как Поиск Azure поддерживает внутреннее состояние, связанное с политикой обнаружения изменений. При изменении политики существующее состояние становится недействительным, так как не применяется к новой политики. Это означает, что индексатор должен начать индексировать источника данных с нуля с помощью новой политики, что подразумевает возможные последствия для вас (например, дополнительную нагрузку на базу данных или дополнительную оплату сетевой пропускной способности). Вот почему Поиск Azure предлагает вызвать [API сброса индексатора](https://msdn.microsoft.com/library/azure/dn946897.aspx), чтобы сбросить состояние, связанное с текущей политикой обнаружения изменений, после чего политику можно будет изменить при помощи обычного вызова PUT для источника данных. Конечно, Поиск Azure может выполнить сброс автоматически, но мы считаем, что это важно, чтобы вы ясно сознавали, что понимаете последствия вызова API сброса.

##Кодирование в URL-адрес ключей документа, которые содержат знаки, небезопасные для URL-адреса##

Поиск Azure ограничивает применение знаков внутри ключевого поля документа, разрешая только знаки, безопасные для URL-адреса, так как пользователи должны иметь возможность для поиска документов по ключам. Таким образом, что происходит, когда документы, которые необходимо индексировать, содержат такие знаки в поле ключа? Если вы индексируете документы самостоятельно с помощью клиентского пакета SDK или REST API, то можете кодировать ключи как URL-адрес. С помощью индексаторов можно указать Поиску Azure кодировать ключи как URL-адрес, задав для параметра **base64EncodeKeys** значение `true` при создании или обновлении индексатора:

    PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "parameters" : { "base64EncodeKeys": true }
    }

Дополнительную информацию о кодировке см. в этой [статье MSDN](http://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx).

ПРИМЕЧАНИЕ. Если требуется использовать поиск или фильтрацию по значениям ключа, понадобится аналогичным образом закодировать ключи, используемые в запросах, чтобы запрос соответствовал закодированному значению, хранящемуся в индексе поиска.


##Допуск сбоев индексирования некоторых документов##

По умолчанию индексатор поиска Azure останавливает индексирование, как только происходит сбой индексации хотя бы в одного документа. В зависимости от сценария можно допустить некоторые сбои (например, если вы повторяете индексацию всего источника данных). Поиск Azure предоставляет два параметра индексатора для настройки такого поведения:

- **maxFailedItems**: количество не прошедших индексирование элементов, превышение которого рассматривается как сбой индексатора. Значение по умолчанию — 0.
- **maxFailedItemsPerBatch**: количество не прошедших индексирование элементов в одном пакете, превышение которого рассматривается как сбой индексатора. Значение по умолчанию — 0.

Эти значения можно изменить в любое время, указав один или оба этих параметра при создании или обновлении индексатора:

	PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
	Content-Type: application/json
	api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }
    }

Даже если вы решили допускать некоторые сбои, информация о том, какие документы не удалось индексировать, возвращается [API получения состояния индексатора](https://msdn.microsoft.com/library/azure/dn946884.aspx).

Пока это так. Если у вас есть какие-либо мысли или предложения по содержимому в будущем, твитните нам с помощью хэш-тега #AzureSearch или отправьте свои идеи на нашу [страницу UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
 

<!---HONumber=AcomDC_0224_2016-->