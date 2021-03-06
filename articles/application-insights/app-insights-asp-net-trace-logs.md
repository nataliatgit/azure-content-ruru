<properties 
	pageTitle="Просмотр журналов трассировки .NET в Application Insights" 
	description="Поиск журналов, созданных с помощью Trace, Log4Net или NLog." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/22/2016" 
	ms.author="awills"/>
 
# Просмотр журналов трассировки .NET в Application Insights  

Если вы используете NLog, log4Net или System.Diagnostics.Trace для диагностической трассировки в приложении ASP.NET, журналы этих платформ можно передавать в [Visual Studio Application Insights][start], где их можно искать и просматривать. Журналы будут объединены с другими данными телеметрии, поступающими из вашего приложения, давая возможность определять трассировки, связанные с обработкой каждого запроса пользователя, и сопоставлять их с другими событиями и отчетами об исключениях.

> [AZURE.NOTE] Требуется модуль записи журнала? Это полезный адаптер для сторонних средств ведения журнала, но если вы еще не используете NLog, log4Net или System.Diagnostics.Trace, рассмотрите возможность вызова [Application Insights TrackTrace()](app-insights-api-custom-events-metrics.md#track-trace) напрямую.


## Установка ведения журнала в приложении

Установите выбранную платформу ведения журналов в своем проекте. При этом должна появиться запись в файлах app.config или web.config.

> Если вы используете System.Diagnostics.Trace, необходимо добавить запись в файл web.config.

## Настройка Application Insights для сбора журналов

**[Добавьте Application Insights в свой проект](app-insights-asp-net.md)**, если вы еще этого не сделали. Вы увидите параметр для включения сборщика журналов.

Или выберите **Настроить Application Insights**, щелкнув правой кнопкой мыши на своем проекте в обозревателе решений. Выберите параметры для включения сборщика журналов.

*Вы не видите меню Application Insights или параметр для включения сборщика журналов?* Попробуйте выполнить [Устранение неполадок](#troubleshooting).


## Ручная установка

Используйте этот метод, если ваш тип проекта не поддерживается программой установки Application Insights (например, если у вас проект настольного приложения Windows).

1. Если вы планируете использовать log4Net или NLog, установите его в свой проект. 
2. В обозревателе решений щелкните правой кнопкой мыши ваш проект и выберите **Управление пакетами NuGet**.
3. Поиск Application Insights

    ![Получите предварительную версию соответствующего адаптера](./media/app-insights-asp-net-trace-logs/appinsights-36nuget.png)

4. Выберите соответствующий пакет из списка.
  + Microsoft.ApplicationInsights.TraceListener (для захвата вызовов System.Diagnostics.Trace)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

Пакет NuGet устанавливает необходимые сборки, а также вносит изменения в файл web.config или app.config.

## Вставка вызовов журнала диагностики

При использовании System.Diagnostics.Trace типичный вызов будет выглядеть следующим образом:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

если вы предпочитаете log4net или NLog:

    logger.Warn("Slow response - database01");


## Непосредственное использование API трассировки

API трассировки в Application Insights можно вызывать напрямую. Адаптеры ведения журналов используют этот API.

Например:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Преимуществом TrackTrace является возможность добавления в сообщения относительно длинных данных, например данных POST.


## Просмотр журналов

Запустите приложение в режиме отладки или разверните его.

В колонке обзора приложения на [портале Application Insights][portal] выберите элемент [Поиск][diagnostic].

![В Application Insights выберите элемент «Поиск»](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![Поиск по журналу диагностики](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

Можно, например:

* фильтровать трассировки журнала или элементы с определенными свойствами;
* просматривать подробные сведения конкретного элемента;
* искать другие данные телеметрии, относящиеся к тому же запросу пользователя (то есть с тем же идентификатором операции); 
* сохранять конфигурацию этой страницы в списке избранного.

> [AZURE.NOTE] **Выборка.** Если ваше приложение выполняет отправку больших объемов данных, а вы используете Application Insights SDK для ASP.NET версии 2.0.0-beta3 или более поздней, функция адаптивной выборки может сработать и отправить только часть вашей телеметрии. [Дополнительная информация о выборке.](app-insights-sampling.md)

## Дальнейшие действия

[Диагностика ошибок и исключений в ASP.NET][exceptions]

[Подробнее о поиске данных диагностики][diagnostic]



## Устранение неполадок

### Как это сделать в Java?

Используйте [адаптеры журналов Java](app-insights-java-trace-logs.md).

### В контекстном меню проекта нет пункта для Application Insights

* Проверьте, установлены ли на этом компьютере средства Application Insights. В меню "Сервис, расширения и обновления" Visual Studio найдите "Средства Application Insights". Если их нет на вкладке "Установлено", перейдите на вкладку "Интернет" и установите их.
* Возможно, этот тип проекта не поддерживается средствами Application Insights. Используйте [ручную установку](#manual-installation).

### В средстве настройки нет параметра для адаптера журналов.

* Сначала необходимо установить платформу ведения журналов.
* Если вы используете System.Diagnostics.Trace, убедитесь, что вы [настроили его в `web.config`](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Установлена ли у вас последняя версия средств Application Insights? В меню **Средства** Visual Studio выберите **Расширения и обновления** и откройте вкладку **Обновления**. Если на ней есть средства Application Insights, щелкните, чтобы обновить их.


### <a name="emptykey"></a>Появляется сообщение об ошибке «ключ инструментирования не может быть пустым»

Вероятно, установка пакета Nuget адаптера ведения журнала была выполнена без установки Application Insights.

В обозревателе решений щелкните правой кнопкой мыши `ApplicationInsights.config` и выберите **Обновить Application Insights**. Появится диалоговое окно с предложением войти в Azure и создать новый ресурс Application Insights или повторно использовать уже существующий. Это должно исправить проблему.

### Я могу видеть трассировки в поиске диагностических данных, но не другие события

Иногда для получения всех событий и запросов через конвейер может потребоваться некоторое время.

### <a name="limits"></a>Какой объем данных сохраняется?

До 500 событий в секунду от каждого приложения. События сохраняются в течение семи дней.

### Мне не удается найти некоторые записи журнала, которые, как мне кажется, должны там быть

Если ваше приложение выполняет отправку больших объемов данных, а вы используете Application Insights SDK для ASP.NET версии 2.0.0-beta3 или более поздней, функция адаптивной выборки может сработать и отправить только часть вашей телеметрии. [Дополнительная информация о выборке.](app-insights-sampling.md)

## <a name="add"></a>Дальнейшие действия

* [Настройка тестов доступности и скорости реагирования][availability]
* [Устранение неполадок][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_0224_2016-->