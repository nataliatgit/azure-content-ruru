<properties 
	pageTitle="Заметки о выпуске расширения Visual Studio для Application Insights" 
	description="Последние обновления средств Visual Studio для Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="aruna" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="acearun"/>
 
# Заметки о выпуске инструментов Application Insights для Visual Studio

## Версия 4.3
### Поиск данных телеметрии из сеансов локальной отладки
В этом выпуске мы представляем возможность поиска в данных телеметрии Application Insights, создаваемых в сеансе отладки Visual Studio. Ранее поиск был возможен, только если приложение было зарегистрировано в Application Insights. Благодаря этому выпуску для приложения достаточно установить пакет SDK для Application Insights. Это обеспечит функцию поиска в локальных данных телеметрии.

#### Если у вас приложение ASP.NET с пакетом SDK для Application Insights

- Выполните отладку приложения.
- Откройте службу поиска по Application Insights одним из следующих способов:
	- Меню "Вид" -> "Другие окна" -> "Поиск по Application Insights".
	- Нажмите кнопку на панели инструментов Application Insights.
	- В обозревателе решений разверните ApplicationInsights.config -> "Search debug session telemetry" (Поиск телеметрии сеанса отладки).
- Если вы еще не зарегистрировались в Application Insights, окно "Поиск" откроется в режиме "Debug session telemetry" (Телеметрия сеанса отладки).
- Щелкните значок поиска, чтобы просмотреть локальные данные телеметрии.

![Передача завершена](./media/app-insights-release-notes-vsix/LocalSearch.png)



##Версия 4.2
В этом выпуске добавлены функции, упрощающие поиск данных в контексте событий, возможность переходить к коду из большего числа событий, инициируемых изменением данных, и упрощенный интерфейс для отправки журналов в Application Insights. Это расширение обновляется ежемесячно. Отзывы и пожелания по добавлению новых функций отправляйте по адресу aidevtools@microsoft.com
###— автоматическое ведение журналов
Если вы уже пользуетесь такими средствами, как NLog, Log4Net или System.Diagnostics, переносить трассировки в Application Insights не придется, поскольку мы интегрировали в обычную настройку конфигурации адаптеры для ведения журналов Application Insights. Если у вас уже настроена одна из этих платформ, выполните описанные ниже действия.
####Если служба Application Insights уже добавлена
- Щелкните правой кнопкой мыши узел проекта и выберите пункт Application Insights, а затем "Настроить Application Insights". В окне конфигурации вы увидите параметр добавления соответствующего адаптера. 
- При создании решения щелкните всплывающее окно в правом верхнем углу экрана. ![Всплывающее уведомление журнала](./media/app-insights-release-notes-vsix/LoggingToast.png)

Установив адаптер для ведения журналов, запустите приложение и убедитесь в том, что данные на вкладке инструментов диагностики выглядят следующим образом: ![Трассировки](./media/app-insights-release-notes-vsix/Traces.png)
###— Пользователь может перейти к коду, вызывающему свойство события телеметрии
С новым выпуском пользователь может щелкнуть любое значение в сведениях о событии и, таким образом, найти соответствующую строку в открытом на данный момент решении. Результаты отобразятся в Visual Studio в списке результатов поиска, как показано ниже: ![Найти совпадения](./media/app-insights-release-notes-vsix/FindMatch.png)
###— Новый экран для незарегистрированных пользователей в окне поиска
Мы улучшили внешний вид окна для поиска данных в рабочей среде. ![Окно поиска](./media/app-insights-release-notes-vsix/SearchWindow.png)
###— Пользователь может просматривать все события телеметрии, связанные с определенным событием
Рядом со сведениями о событии появилась новая вкладка, где приводятся готовые запросы для просмотра всех данных, связанных с интересующим пользователя событием телеметрии. Пример. Запрос содержит поле с идентификатором вызываемой операции, который будет использоваться в каждом событии, связанном с этим запросом. Если при обработке запроса возникнет исключение, оно получит такой же идентификатор операции, как в запросе, что упростит поиск. Теперь, просматривая запрос, пользователь может щелкнуть ссылку "Все данные телеметрии для этой операции", после чего откроется вкладка с новыми результатами поиска. ![Связанные элементы](./media/app-insights-release-notes-vsix/RelatedItems.png)
### — Переход вперед и назад по журналам в поиске
Теперь пользователи могут листать результаты поиска вперед и назад. ![Назад](./media/app-insights-release-notes-vsix/GoBAck.png)

##Версия 4.1
В этом выпуске к уже существующим возможностям добавлены новые функции и улучшения. Чтобы получить этот выпуск, установите на компьютере обновление 1.

### Переход от исключения к методу в исходном коде
Теперь при просмотре исключений, выданных рабочими приложениями, в окне поиска Application Insights пользователи могут переходить к тому методу в коде, где возникло исключение. Вам нужно только загрузить соответствующий проект, а все остальное мы возьмем на себя. (Дополнительные сведения об окне поиска см. ниже в примечаниях к выпуску 4.0.)

#### Как это работает?

Если решение не открыто, функцию поиска Application Insights можно использовать, не открывая решение. В этом случае в области трассировки стека появится информационное сообщение, а многие элементы в трассировке стека будут затеменены.


Если доступны сведения о файле, некоторые пункты могут быть ссылками, но пункт сведений о решении все равно отображается.

Щелкнув гиперссылку, вы перейдете к выбранному методу в вашем коде (номер версии может отличаться, но в последующих выпусках будет добавлена функция перехода к соответствующей версии кода).

![Выбор исключения](./media/app-insights-release-notes-vsix/jumptocode.png)

###Новые точки входа для выполнения поиска в обозревателе решений 

![Точка входа в обозревателе решений](./media/app-insights-release-notes-vsix/searchentry.png)


###Всплывающее уведомление по завершении публикации
Как только проект будет опубликован, появится всплывающее окно, позволяющее увидеть данные Application Insights в рабочей среде.

![Всплывающее окно](./media/app-insights-release-notes-vsix/publishtoast.png)

## Версия 4.0

###Поиск данных Application Insights в среде Visual Studio
Как и при поиске на портале Application Insights, вы можете использовать в качестве фильтров и поисковых запросов типы событий, значения свойств и текст, а также проверять отдельные события.

![Окно поиска](./media/app-insights-release-notes-vsix/search.png)

###Просмотр данных из локального поля в окне средств диагностики

Наряду с другими данными отладки на узле диагностики Visual Studio будут показываться также данные телеметрии. Поддерживается только ASP.NET 4.5. Поддержка ASP. NET 5 будет реализована в будущих выпусках.

![Окно концентратора диагностики](./media/app-insights-release-notes-vsix/diagtools.png)

###Добавление пакета SDK в проект без необходимости входа в Azure

Для добавления пакетов Application Insights в проект через диалоговое окно создания проекта или контекстное меню проекта больше не нужно выполнять вход в Azure. Если вход выполнен, пакет SDK устанавливается и настраивается для отправки телеметрии на портал, как и раньше. Если вход не выполнен, пакет SDK добавляется в проект и генерирует телеметрию для узла диагностики — при желании вы сможете настроить его позднее.

![Диалоговое окно "Новый проект"](./media/app-insights-release-notes-vsix/newproject.png)

###Поддержка устройств

В *Connect();* 2015 мы [анонсировали](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/) HockeyApp — наше решение DevOps для мобильных устройств. HockeyApp помогает рассылать бета-сборки тестировщикам, собирать и анализировать все сбои в приложении и получать отзывы непосредственно от клиентов. HockeyApp поддерживает любую платформу, на которой может быть собрано мобильное приложение, будь то iOS, Android, Windows или межплатформенное решение, такое как Xamarin, Cordova или Unity.

В следующие выпуски расширения Application Insights будут добавлены новые функции, обеспечивающие более интегрированное взаимодействие между HockeyApp и Visual Studio. В настоящее время работу с HockeyApp можно начать, просто добавив ссылку на NuGet. Дополнительные сведения см. в [документации](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone).

 

<!---HONumber=AcomDC_0302_2016-->