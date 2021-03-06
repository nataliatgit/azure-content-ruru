<properties 
	pageTitle="Использование Stream Analytics для экспорта данных из Application Insights в Power BI" 
	description="Показывается, как использовать Stream Analytics для обработки экспортированных данных." 
	services="application-insights" 
    documentationCenter=""
	authors="noamben" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/25/2015" 
	ms.author="awills"/>
 
# Использование Stream Analytics для передачи данных из Application Insights в Power BI

В этой статье рассказывается, как использовать [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) для обработки данных, [экспортированных](app-insights-export-telemetry.md) из [Visual Studio Application Insights](app-insights-overview.md). В качестве примера мы отправляем данные в [Microsoft Power BI](https://powerbi.microsoft.com/).


> [AZURE.NOTE] Самый простой способ передавать данные в Power BI из Application Insights — [использовать адаптер](https://powerbi.microsoft.com/ru-RU/documentation/powerbi-content-pack-application-insights/), который можно найти в разделе служб коллекции Power BI. В этой статье мы опишем способ, который является более гибким, но также покажем, как использовать Stream Analytics с Application Insights.

[Microsoft Power BI](https://powerbi.microsoft.com/) представляет данные в виде разнообразных информативных визуальных элементов, а также позволяет объединить информацию из нескольких источников.


![Пример представления Power BI с данными об использовании, полученными из Application Insights](./media/app-insights-export-power-bi/010.png)

[Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) — это служба Azure, которая работает как адаптер, постоянно обрабатывающий данные, экспортируемые из Application Insights.

![Пример представления Power BI с данными об использовании, полученными из Application Insights](./media/app-insights-export-power-bi/020.png)




## Видео

Ноам Бен Зив (Noam Ben Zeev) показывает то, о чем идет речь в этой статье.

> [AZURE.VIDEO export-to-power-bi-from-application-insights]


**Выборка.** Если ваше приложение выполняет отправку больших объемов данных, а вы используете Application Insights SDK для ASP.NET версии 2.0.0-beta3 или более поздней, функция адаптивной выборки может сработать и отправить только часть вашей телеметрии. [Дополнительная информация о выборке.](app-insights-sampling.md)

## Мониторинг приложения с помощью Application Insights

Если вы еще не пробовали это, начните сейчас. Application Insights может осуществлять мониторинг любого устройства или веб-приложения на разных платформах, включая Windows, iOS, Android, J2EE и др. [Начало работы](app-insights-overview.md).

## Создание хранилища в Azure

В результате непрерывного экспорта происходит передача данных в учетную запись хранилища Azure, поэтому необходимо сначала создать хранилище.

1. Создайте "классическую" учетную запись хранения в подписке на [портале Azure](https://portal.azure.com).

    ![На портале Azure выберите «Создать», «Данные», «Хранилище».](./media/app-insights-export-power-bi/030.png)

2. Создание контейнера

    ![В новом хранилище выберите «Контейнеры», щелкните элемент «Контейнеры», а затем — «Добавить».](./media/app-insights-export-power-bi/040.png)

3. Скопируйте ключ доступа к хранилищу.

    Он вскоре потребуется для настройки входных данных для службы Stream Analytics.

    ![В хранилище откройте «Параметры», «Ключи» и сделайте копию первичного ключа доступа.](./media/app-insights-export-power-bi/045.png)

## Запуск непрерывного экспорта в службе хранилища Azure

[Непрерывный экспорт](app-insights-export-telemetry.md) перемещает данные из Application Insights в хранилище Azure.

1. На портале Azure перейдите к ресурсу Application Insights, созданному для приложения.

    ![Выберите «Обзор», «Application Insights», а затем выберите свое приложение.](./media/app-insights-export-power-bi/050.png)

2. Создайте непрерывный экспорт.

    ![Выберите «Параметры», «Непрерывный экспорт», «Добавить».](./media/app-insights-export-power-bi/060.png)


    Выберите учетную запись хранения, созданную ранее:

    ![Установите место назначения экспорта.](./media/app-insights-export-power-bi/070.png)
    
    Задайте необходимые типы событий:

    ![Выберите типы событий.](./media/app-insights-export-power-bi/080.png)

3. Пусть данные накопятся. Предоставьте пользователям возможность поработать с приложением на протяжении некоторого времени. После получения данных телеметрии в [обозревателе метрик](app-insights-metrics-explorer.md) отобразятся статистические диаграммы, а в разделе [Поиск по журналу диагностики](app-insights-diagnostic-search.md) – отдельные события.

    Данные также будут экспортированы в хранилище.

4. Проверьте экспортированные данные. В Visual Studio откройте меню **"Вид" или "Обозреватель облака"** и выберите элемент "Azure" или "Хранилище". (Если этой команды нет в меню, установите пакет SDK Azure: откройте диалоговое окно «Создание проекта», разверните узел «Visual C#/облако» и выберите «Получить Microsoft Azure SDK для .NET».)

    ![](./media/app-insights-export-power-bi/04-data.png)

    Запишите общую часть имени пути, которое образовано от имени приложения и ключа инструментирования.

События записываются в JSON-файлы больших двоичных объектов. Каждый файл может содержать одно или несколько событий. Поэтому нам нужна возможность считывать данные событий и отфильтровывать необходимые поля. С данными можно выполнять любые действия, но сейчас мы будем использовать Stream Analytics для перемещения данных в Power BI.

## Создание экземпляра Azure Stream Analytics

В [классическом портале Azure](https://manage.windowsazure.com/) выберите службу Azure Stream Analytics и создайте новое задание Stream Analytics:


![](./media/app-insights-export-power-bi/090.png)



![](./media/app-insights-export-power-bi/100.png)

При создании нового задания разверните информацию о нем:

![](./media/app-insights-export-power-bi/110.png)


#### Установка расположения большого двоичного объекта

Задайте расположение для приема входных данных из большого двоичного объекта непрерывного экспорта:

![](./media/app-insights-export-power-bi/120.png)

Теперь потребуется первичный ключ доступа из вашей учетной записи хранения, указанной ранее. Задайте его в качестве ключа учетной записи хранения.

![](./media/app-insights-export-power-bi/130.png)

#### Установка шаблона префикса пути 

![](./media/app-insights-export-power-bi/140.png)


Задайте в поле «Формат даты» значение в формате ГГГГ-ММ-ДД (с дефисами).

Шаблон префикса пути указывает, где Stream Analytics находит входные файлы в хранилище. Вам необходимо настроить это поле в соответствии с тем, как функция непрерывного экспорта сохраняет данные. Задайте следующее значение:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

В данном примере:

* `webapplication27` — имя ресурса Application Insights (**только строчные буквы**).
* `1234...` — ключ инструментирования ресурса Application Insights с **удаленными дефисами**. 
* `PageViews` — тип данных, которые необходимо проанализировать. Доступные типы зависят от фильтра, установленного в функции непрерывного экспорта. Изучите экспортированные данные, чтобы увидеть другие доступные типы, а также ознакомьтесь с [моделью экспорта данных](app-insights-export-data-model.md).
* `/{date}/{time}` – шаблон, записанный буквально.

> [AZURE.NOTE] Проверьте хранилище и убедитесь, что вы получаете нужный путь.

#### Завершение начальной настройки

Подтвердите формат сериализации:

![Подтвердите выбор и закройте мастер.](./media/app-insights-export-power-bi/150.png)

Закройте мастер и дождитесь завершения установки.

> [AZURE.TIP] Воспользуйтесь примером команды для загрузки некоторых данных. Сохраните их как тестовый образец для отладки запроса.

## Определение выходных данных

Теперь выберите задание и задайте выходные данные.

![Выбор нового канала и элементов «Выходные данные», «Добавить», «Power BI».](./media/app-insights-export-power-bi/160.png)

Предоставьте свою **рабочую или учебную учетную запись** для обеспечения доступа Stream Analytics к ресурсу Power BI. Затем укажите имя для выходных данных, а также для целевого набора данных и таблицы Power BI.

![Создание трех имен.](./media/app-insights-export-power-bi/170.png)

## Определение запроса

Запрос определяет преобразование входных данных в выходные.

![Выбор задания и элемента «Запрос». Вставка следующего примера.](./media/app-insights-export-power-bi/180.png)


Используйте функцию «Проверить» для проверки выходных данных. Предоставьте ей образец данных, полученный на странице входных данных.

#### Запрос для отображения количества событий

Вставьте следующий запрос.

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-input — это псевдоним, присвоенный входным данным потока.
* pbi-output — это определенный псевдоним выходных данных.
* Мы используем [OUTER APPLY GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx), так как имя события находится во вложенном массиве JSON. Затем элемент Select выбирает имя события, а также количество экземпляров с этим именем за определенный период времени. Предложение [Group By](https://msdn.microsoft.com/library/azure/dn835023.aspx) группирует элементы по периодам времени в 1 минуту.


#### Запрос для отображения значений метрики


```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* Этот запрос позволяет получить время события и значение метрики по телеметрии метрики. Значения метрик находятся в массиве, поэтому мы используем шаблон OUTER APPLY GetElements для извлечения строк. myMetric — это имя метрики. 

#### Запрос для добавления значений свойств измерения

```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* Этот запрос включает в себя значения свойств измерения вне зависимости от того, соответствует ли конкретному измерению фиксированный индекс в массиве.

## Выполнение задания

Дату запуска задания можно выбрать в прошлом.

![Выбор задания и элемента «Запрос». Вставка следующего примера.](./media/app-insights-export-power-bi/190.png)

Подождите, пока задание находится в статусе выполнения.

## Просмотр результатов в Power BI

Откройте Power BI с рабочей или учебной учетной записью и выберите набор данных и таблицу, которые были определены как выходные данные задания Stream Analytics.

![Выбор набора данных и полей в Power BI.](./media/app-insights-export-power-bi/200.png)

Теперь этот набор данных можно использовать в отчетах и панелях мониторинга в [Power BI](https://powerbi.microsoft.com).


![Выбор набора данных и полей в Power BI.](./media/app-insights-export-power-bi/210.png)

## Видео

Ноам Бен Зив (Noam Ben Zeev) показывает, как выполнить экспорт в Power BI.

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## Связанные материалы

* [непрерывный экспорт.](app-insights-export-telemetry.md)
* [Подробный справочник по модели данных типов и значений свойств.](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)
* [Дополнительные примеры и пошаговые руководства](app-insights-code-samples.md)

<!---HONumber=AcomDC_0128_2016-->