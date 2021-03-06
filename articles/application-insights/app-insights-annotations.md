<properties
    pageTitle="Заметки о выпуске для Application Insights | Microsoft Azure"
    description="Добавление маркеров развертывания или сборки для диаграмм обозревателя метрик в Application Insights."
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

# Заметки к выпуску в Application Insights

Заметки к выпуску на диаграммах [обозревателя метрик](app-insights-metrics-explorer.md) показывают, где развернута новая сборка. С их помощью легко увидеть, повлияли ли ваши изменения на производительность приложения. Заметки к выпуску могут быть созданы автоматически [системой сборки Visual Studio Team Services](https://www.visualstudio.com/ru-RU/get-started/build/build-your-app-vs).

![Пример заметок с видимой корреляцией с временем ответа сервера](./media/app-insights-annotations/00.png)

Заметки к выпуску являются функцией службы облачной сборки и выпуска Visual Studio Team Services.

## Установка расширения заметок (однократно)

Чтобы получить возможность создания заметок к выпуску, необходимо установить одно из расширений Team Service, доступных в магазине Visual Studio.

1. Войдите в свой проект в [Visual Studio Team Services](https://www.visualstudio.com/ru-RU/get-started/setup/sign-up-for-visual-studio-online).
2. В магазине Visual Studio [найдите расширение заметок к выпуску](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) и добавьте его к своей учетной записи Team Services.

![В верхнем правом углу веб-страницы Team Services откройте "Магазин". Выберите Visual Team Services и нажмите кнопку "Дополнительно" в разделе "Сборка и выпуск".](./media/app-insights-annotations/10.png)

Это необходимо сделать только один раз для учетной записи Visual Studio Team Services. Теперь можно настроить заметки к выпуску для любого проекта в вашей учетной записи.

## Получение ключа API из Application Insights

Необходимо сделать это для каждого шаблона выпуска, в котором должны создаваться заметки к выпуску.


1. Выполните вход в [портал Microsoft Azure](https://portal.azure.com) и откройте ресурс Application Insights, который используется для мониторинга вашего приложения. (Или [создайте новый](app-insights-overview.md), если вы этого еще не сделали.)
2. Откройте **Параметры**, **Доступ к API** и сделайте копию значения **ИД Application Insights**.

    ![На сайте portal.azure.com откройте ресурс Application Insights и выберите "Параметры". Откройте "Доступ к API". Скопируйте идентификатор приложения.](./media/app-insights-annotations/20.png)

2. В отдельном окне браузера откройте (или создайте) шаблон выпуска, который управляет развертываниями из Visual Studio Team Services.

    Добавьте задачу и выберите в меню задачу заметок к выпуску Application Insights.

    Вставьте **идентификатор приложения**, скопированный из колонки "Доступ к API".

    ![В Visual Studio Team Services откройте "Выпуск", выберите определение выпуска и нажмите кнопку "Изменить". Щелкните "Добавьте задачу" и выберите "Заметки к выпуску Application Insights". Вставьте ИД Application Insights.](./media/app-insights-annotations/30.png)

3. Задайте в качестве значения поля **APIKey** переменную `$(ApiKey)`.

4. В колонке "Доступ к API" создайте новый ключ API и сделайте его копию.

    ![В колонке "Доступ к API" в окне Azure щелкните "Создать ключ API". Введите комментарий, щелкните "Написать заметки" и нажмите кнопку "Создать ключ". Скопируйте новый ключ.](./media/app-insights-annotations/40.png)

4. Перейдите на вкладку "Конфигурация" шаблона выпуска.

    Создайте определение переменной для `ApiKey`.

    Вставьте ключ API в определение переменной ApiKey.

    ![В окне Team Services перейдите на вкладку "Конфигурация" и щелкните "Добавить переменную". Задайте "ApiKey" в качестве имени и в поле "Значение" вставьте ключ, который был только что создан.](./media/app-insights-annotations/50.png)


5. Наконец, **сохраните** определение выпуска.

## Примечания к выпуску

Теперь при каждом развертывании нового выпуска с помощью шаблона выпуска заметки будут отправляться в Application Insights. Заметки будут отображаться на диаграммах в обозревателе метрик.

Щелкните любой маркер заметки, чтобы открыть подробные сведения о выпуске, включая запросившую сторону, ветвь системы управления версиями, определение выпуска, среду и многое другое.


![Щелкните любой маркер заметки о выпуске.](./media/app-insights-annotations/60.png)

<!---HONumber=AcomDC_0224_2016-->