<properties 
	pageTitle="Обнаружение и диагностика сбоев в приложениях Магазина Windows и Windows Phone с помощью Application Insights" 
	description="Анализ проблем производительности в приложении устройства Windows с помощью Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/21/2015" 
	ms.author="awills"/>

# Обнаружение и диагностика сбоев в приложениях Магазина Windows и Windows Phone с помощью Application Insights

*Служба Application Insights доступна в предварительной версии.*

Если у пользователей возникают сбои в приложении, необходимо узнать об этом быстро и в подробностях. С помощью Application Insights можно отслеживать, как часто происходят сбои, получать оповещения при возникновении сбоев и изучить отчеты об отдельных случаях.

«Сбой» означает, что приложение завершает работу из-за неперехваченного исключения. Если приложение перехватывает исключение, можно сообщить о нем с помощью [API TrackException][apiexceptions], не останавливая работу. В этом случае исключение не будет запротоколировано как сбой.


## Отслеживание частоты сбоев

Если этого еще не сделано, добавьте [Application Insights в свой проект приложения][windows] и повторно опубликуйте его.

Сбои отображаются в колонке обзора приложения [на портале Application Insights][portal].

![](./media/app-insights-windows-crashes/appinsights-d018-oview.png)

Можно изменить диапазон времени, показываемый на диаграмме.


## Установка оповещения для обнаружения сбоев

![В диаграмме сбоев щелкните сначала "Правила оповещения", а затем "Добавить оповещение"](./media/app-insights-windows-crashes/appinsights-d023-alert.png)

## Диагностика сбоев

Чтобы узнать, происходит ли для некоторых версий вашего приложения больше аварийных завершений, чем для других, щелкните диаграмму сбоев и затем сегментируйте по версиям приложения:

![](./media/app-insights-windows-crashes/appinsights-d26crashSegment.png)


Для обнаружения исключений, которые приводят к сбоям, откройте колонку поиска по журналу диагностики. Может потребоваться удалить другие типы данных телеметрии, чтобы сосредоточить внимание на исключениях:

![](./media/app-insights-windows-crashes/appinsights-d26crashExceptions.png)

[Подробнее о поиске по журналу диагностики][diagnostic].
 

Щелкните любое исключение, чтобы увидеть подробные сведения о нем, включая связанные свойства и трассировку стека.

![](./media/app-insights-windows-crashes/appinsights-d26crash.png)

См. другие исключения и события, произошедшие рядом с этим исключением.


![](./media/app-insights-windows-crashes/appinsights-d26crashRelated.png)

## Вставка журналов трассировки и событий

Для диагностики неполадок можно [вставить вызовы трассировки и осуществлять поиск в журналах в Application Insights][diagnostic].

## <a name="debug"></a>Режим отладки и режим выпуска

#### Отладка

При сборке в режиме отладки события передаются по мере их создания. При потере подключения к Интернету и выходе из приложения до восстановления подключения данные автономной телеметрии удаляются.

#### Выпуск

Если сборка выполняется в конфигурации выпуска, события хранятся на устройстве и передаются, когда приложение возобновляет работу. Данные также отправляются при первом использовании приложения. Если при запуске отсутствует подключение к Интернету, предыдущие данные телеметрии, а также данные телеметрии для текущего жизненного цикла сохраняются и отправляются при следующем возобновлении.

## <a name="next"></a>Дальнейшие действия

[Обнаружение, рассмотрение и диагностика проблем с помощью Application Insights][detect]

[API Application Insights][api]

[Запись журналов диагностики][trace]

[Устранение неполадок](app-insights-windows-troubleshoot.md)




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_1203_2015-->