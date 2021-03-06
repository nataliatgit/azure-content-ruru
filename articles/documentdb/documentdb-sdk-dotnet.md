<properties 
	pageTitle="Пакет SDK для DocumentDB .NET | Microsoft Azure" 
	description="Сведения о пакете SDK для .NET, включая даты выхода, даты выбытия и изменения, внесенные в каждую версию пакета SDK для DocumentDB .NET." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/19/2016" 
	ms.author="ryancraw"/>

# Пакет SDK для DocumentDB

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##Пакет SDK для DocumentDB .NET

<table> <tr><td>**Загрузка**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr> <tr><td>**Документация**</td><td>[Справочник по пакету SDK для .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr> <tr><td>**Примеры**</td><td>[Примеры кода для .NET](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)</td></tr> <tr><td>**Начало работы**</td><td>[Начало работы с пакетом SDK для DocumentDB .NET](documentdb-get-started.md)</td></tr> <tr><td>**Поддерживаемая версия платформы**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr> </table></br>

## Заметки о выпуске

### <a name="1.5.3"/>[1\.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
  - **[Исправлено]**. Запрос к конечной точке DocumentDB порождает "System.Net.Http.HttpRequestException: ошибка при копировании содержимого в поток".

### <a name="1.5.2"/>[1\.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
  - Расширенная поддержка LINQ, включая новые операторы разбиения по страницам, условные выражения и сравнение диапазонов.
    - Оператор Take, позволяющий выполнить операцию SELECT TOP в LINQ.
    - Оператор CompareTo, позволяющий сравнивать диапазоны строк.
    - Условный оператор (?) и оператор объединения (??).
  - **[Исправлено]**. Порождение исключения ArgumentOutOfRangeException при комбинировании проекции Model с Where-In в запросе LINQ. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="1.5.1"/>[1\.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[Исправлено]** Если операция Select не является последней, поставщик LINQ не использовал проекции и неверно определял операцию SELECT *. [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58).

### <a name="1.5.0"/>[1\.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - Реализован метод Upsert, добавлен метод UpsertXXXAsync.
 - Повышение производительности для всех запросов.
 - Поддержка поставщиком LINQ условных операций, функций объединения и методов CompareTo для строковых значений.
 - **[Исправлено]** Поставщик LINQ: реализация метода Contains для списков для создания таких же запросов SQL, как для IEnumerable и массивов.
 - **[Исправлено]** BackoffRetryUtility повторно использует то же сообщение HttpRequestMessage вместо создания нового при повторной попытке.
 - **[Устарело]** UriFactory.CreateCollection: теперь следует использовать UriFactory.CreateDocumentCollection.
 
### <a name="1.4.1"/>[1\.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[Исправлено]** Проблемы локализации при использовании региональных параметров, отличных от en, например nl-NL и т. д. 
 
### <a name="1.4.0"/>[1\.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - Маршрутизация на основе идентификатора.
    - Новый вспомогательный класс UriFactory для создания ссылок на ресурсы на основании идентификатора.
    - Новые перегрузки в классе DocumentClient, принимающие URI в качестве параметров.
  - Для геопространственных данных в LINQ добавлены методы IsValid() и IsValidDetailed().
  - Улучшена поддержка поставщика LINQ.
    - **Математические функции:** Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate.
    - **Строковые функции:** Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper.
    - **Функции массивов:** Concat, Contains, Count.
    - Оператор **IN**.

### <a name="1.3.0"/>[1\.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - Добавлена поддержка изменения политики индексации.
    - Новый метод ReplaceDocumentCollectionAsync в классе DocumentClient.
    - Новое свойство IndexTransformationProgress в ResourceResponse<T> для отслеживания процесса изменения политики индексирования (в процентах).
    - Свойство DocumentCollection.IndexingPolicy теперь является изменяемым.
  - Добавлена поддержка индексирования и создания запросов к пространственным типам данных.
    - Новое пространство имен Microsoft.Azure.Documents.Spatial для сериализации и десериализации пространственных типов, таких как Point и Polygon.
    - Новый класс SpatialIndex для индексирования данных GeoJSON, хранящихся в DocumentDB.
  - **[Исправлено]** Из выражения LINQ [#38](https://github.com/Azure/azure-documentdb-net/issues/38) создавался неверный SQL-запрос.

### <a name="1.2.0"/>[1\.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- Зависимость от Newtonsoft.Json 5.0.7. 
- Изменения для поддержки Order By.
  - Поставщик LINQ поддерживает OrderBy() или OrderByDescending().
  - IndexingPolicy поддерживает Order By. 
  
		**Следует учесть: потенциальное изменение, нарушающее функциональность** 
  
    	Если имеется существующий код, который предоставляет коллекции с пользовательской политикой индексирования, он должен быть обновлен и должен поддерживать новый класс IndexingPolicy. Если такой политики нет, это изменение на вас не влияет.

### <a name="1.1.0"/>[1\.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- Новые классы HashPartitionResolver и RangePartitionResolver и интерфейс IPartitionResolver для поддержки секционирования данных.
- Сериализация DataContract.
- Поддержка GUID в поставщике LINQ.
- Поддержка пользовательских функций в LINQ.

### <a name="1.0.0"/>[1\.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- Пакет SDK общей доступности.

> [AZURE.NOTE]
Изменилось название пакета NuGet при переходе от предварительной версии к общедоступной. Имя изменилось с **Microsoft.Azure.Documents.Client** на **Microsoft.Azure.DocumentDB**. <br/>


### <a name="0.9.x-preview"/>[0\.9.x-preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- [Устарело] Пакеты SDK для предварительной версии.

## Даты выпуска и выбытия
Корпорация Майкрософт отправит уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

Новые функции, возможности и оптимизации добавляются только в текущую версию пакета SDK, поэтому рекомендуется как можно раньше обновлять пакет SDK до последней версии.

Любые запросы к DocumentDB с помощью выведенного из эксплуатации SDK будут отклоняться службой.

> [AZURE.WARNING]
Все версии пакета SDK для Azure DocumentDB для .NET версии ниже **1.0.0** будут удалены **29 февраля 2016 г.**
 
<br/>
 
| Version (версия) | Дата выпуска | Дата вывода 
| ---	  | ---	         | ---
| [1\.5.3](#1.5.3) | 19 февраля 2016 г. |--- 
| [1\.5.2](#1.5.2) | 14 декабря 2015 г. |--- 
| [1\.5.1](#1.5.1) | 23 ноября 2015 г. |--- 
| [1\.5.0](#1.5.0) | 5 октября 2015 г. |--- 
| [1\.4.1](#1.4.1) | 25 августа 2015 г. |--- 
| [1\.4.0](#1.4.0) | 13 августа 2015 г. |--- 
| [1\.3.0](#1.3.0) | 5 августа 2015 г. |--- 
| [1\.2.0](#1.2.0) | 6 июля 2015 г. |--- 
| [1\.1.0](#1.1.0) | 30 апреля 2015 г. |--- 
| [1\.0.0](#1.0.0) | 8 апреля 2015 г. |--- 
| [0\.9.3-prelease](#0.9.x-preview) | 12 марта 2015 г. | 29 февраля 2016 г. 
| [0\.9.2-prelease](#0.9.x-preview) | Января 2015 г. | 29 февраля 2016 г. 
| [0\.9.1-prelease](#0.9.x-preview) | 13 октября 2014 г. | 29 февраля 2016 г. 
| [0\.9.0-prelease](#0.9.x-preview) | 21 августа 2014 г. | 29 февраля 2016 г.

## Часто задаваемые вопросы
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## См. также

Дополнительные сведения о DocumentDB см. на странице документации по [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0224_2016-->