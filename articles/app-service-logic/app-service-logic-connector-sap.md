<properties
   pageTitle="Использование соединителя SAP в приложениях логики | Служба приложений Microsoft Azure"
   description="Как создать и настроить соединитель SAP или приложение API и использовать его в приложении логики в службе приложений Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="harishkragarwal"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/18/2016"
   ms.author="sameerch"/>


# Приступая к работе с соединителем SAP: добавление в приложение логики
>[AZURE.NOTE] Эта версия статьи предназначена для приложений логики со схемой версии 2014-12-01-preview.

Подключитесь к локальному протоколу SAP для вызова RFC или получения метаданных. Существуют сценарии, в которых необходимо работать с системой SAP, установленной локально за брандмауэром. Используя в потоке соединитель SAP, можно реализовать множество сценариев. Вот несколько примеров.

1.	Предоставление раздела данных, размещенных в SAP, через Интернет или внешний интерфейс мобильного пользователя.
2.	Публикация данных в SAP после должной обработки.
3.	Извлечение данных из SAP для использования в бизнес-процессе.

Соединители можно использовать в приложениях логики для извлечения, обработки или отправки данных в рамках потока. Соединитель SAP можно добавить в рабочий процесс компании и обрабатывать данные в рамках этого процесса приложения логики.


Для реализации этих сценариев необходимо выполнить следующее.

1. Создать экземпляр приложения API «Соединитель SAP».
2. Установить гибридное подключение приложения API для взаимодействия с локальной системой SAP.
3. Использование созданного приложения API в приложении логики для реализации нужного бизнес-процесса


## Создать экземпляр приложения API «Соединитель SAP». ##

Соединитель можно создать в приложении логики или непосредственно в Azure Marketplace. Создание соединителя в Marketplace

1. На начальной панели Azure выберите **Marketplace**.
2. Выполните поиск по запросу «Соединитель SAP», выберите его, а затем нажмите **Создать**.
3. Настройте его следующим образом.
	1. Предоставьте общие сведения, такие как имя, план обслуживания приложения и т. д., в первой колонке.

	2. В параметрах пакета предоставьте учетные данные SAP. Также укажите строку подключения к служебной шине Azure. Она используется для установки гибридного подключения к локальной среде SAP.

	3. Параметры RFC, TRFC, BAPI и IDOC необходимо настроить в соответствии с нужным сценарием. При указании нескольких значений разделяйте их запятыми.

![][1]

## Настройка только что созданного приложения API «Соединитель SAP» ##

Перейдите в только что созданное приложение API, последовательно выбрав «Обзор» > «Приложения API» > <Name of the API App just created>, и вы увидите следующее поведение. Настройка не завершена, так как гибридное подключение еще не установлено. ![][2]

Для подключения к *любой* конечной точке SAP соединителю SAP требуется гибридное подключение. Чтобы установить гибридное подключение, выполните следующие действия:

1. Скопируйте основную строку подключения.
2. Щелкните ссылку «Загрузить и настроить».
3. Выполните инициированный процесс установки и при запросе предоставьте основную строку подключения.
4. После завершения процесса установки появится диалоговое окно, подобное приведенному ниже. ![][3]

Подробнее об [интеграции с локальным сервером SAP](app-service-logic-integrate-with-an-on-premise-sap-server.md).

Теперь, если вы снова перейдете в созданное приложение API, то увидите, что гибридное подключение имеет состояние «Подключено». ![][4]

Примечание. Если вы хотите переключиться на дополнительную строку подключения, просто повторите установку гибридного подключения и вместо основной строки подключения предоставьте дополнительную.

## Использование в приложении логики ##

Соединитель SAP может использоваться в приложении логики только как действие/шаг.

При создании или изменении приложения логики выберите приложение API «Соединитель SAP», созданное выше. Появится список всех возможных действий, из которого можно выбрать нужное действие. ![][5]

После выбора действия появится список входных параметров для этого действия. Предоставьте соответствующие значения и щелкните значок галочки. ![][6]

Шаг или действие будет показано в приложении логики как настроенное. Будут отображаться результаты выполнения операции, которые можно использовать в качестве входных данных в следующем шаге. ![][7]

Завершите приложение логики, чтобы определить бизнес-процесс, и затем выполните его, чтобы добиться нужной цели.

## Дополнительные возможности соединителя
После создания соединителя его можно добавить в рабочий бизнес-процесс с помощью приложения логики. См. раздел [Что такое приложения логики?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Если вы хотите начать работу с приложениями логики Azure до создания учетной записи Azure, перейдите на веб-сайт [пробного использования приложений логики](https://tryappservice.azure.com/?appservice=logic). На этом сайте вы сможете быстро создать кратковременное приложение логики начального уровня в службе приложений. Никаких кредитных карт и обязательств.

Справку по API REST Swagger см. в статье [Справочные материалы по соединителям и приложениям API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Можно также просматривать статистику производительности и управлять безопасностью соединителя. См. статью [Управление встроенными приложениями API и соединителями, а также их мониторинг](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-sap/Create.jpg
[2]: ./media/app-service-logic-connector-sap/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sap/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sap/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sap/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sap/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sap/LogicApp3.jpg

<!---HONumber=AcomDC_0224_2016-->