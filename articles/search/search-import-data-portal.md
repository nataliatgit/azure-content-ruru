<properties
	pageTitle="Импорт данных в службу поиска Azure с помощью портала | Microsoft Azure | Размещенная облачная служба поиска"
	description="Передача данных в индекс в службе поиска Azure с помощью портала."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="Azure Portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="heidist"/>

# Импорт данных в службу поиска Azure с помощью портала
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST API](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Панель мониторинга службы поиска Azure на портале Azure содержит команду **Импорт данных**, которая поможет вам принять данные в службе поиска Azure. Эта команда использует встроенные индексаторы, которые сканируют существующий источник данных, а также создают и передают документы на основе набора строк в источнике данных.

Импорт данных с помощью мастера состоит из трех компонентов:

- подключение к источнику данных;
- целевой индекс, в который передаются данные (во многих случаях мастер создает его автоматически);
- расписание, выполняемое сейчас или через регулярные интервалы.

Для использования индексатора или команды **Импорт данных** необходимо, чтобы первичный источник данных представлял собой один из поддерживаемых источников данных: базу данных SQL Azure, реляционную базу данных SQL Server на виртуальной машине Azure или службу Azure DocumentDB.

Данные можно импортировать только из одной таблицы, представления или эквивалентной структуры данных. Возможно, вам сначала придется создать эту структуру в источнике данных приложения, чтобы получить правильные метаданные и входные данные для индекса поиска.

Вы можете опробовать этот рабочий процесс, используя демонстрационные данные. См. статью [Начало работы со службой поиска Azure на портале Azure](search-get-started-portal.md).

##Настройка импорта данных

1. Войдите на [портал Azure](https://portal.azure.com).

2. Откройте служебную панель службы поиска Azure. Найти панель мониторинга можно несколькими способами.
	- На навигационной панели щелкните **Home** (Главная). На главной странице отображаются плитки для всех служб, на которые вы подписались. Щелкните плитку, чтобы открыть панель мониторинга службы.
	- На навигационной панели последовательно выберите **Просмотреть все** > **Фильтровать по** > **Службы поиска** и найдите в списке нужную службу поиска.

3. В верхней части панели мониторинга службы отобразится панель команд, включающая команду **Импорт данных**. Щелкните **Импорт данных**, чтобы открыть колонку «Импорт данных».

4. Щелкните **Подключиться к данным**, чтобы указать определение источника данных, используемое в индексаторе. Доступные параметры:
	- 	Существующий источник данных ссылается на определение источника данных, созданное ранее для индексатора. Если индексаторы в службе поиска уже определены, определение источника данных можно переделать под другую операцию импорта.
	- 	SQL Azure используется для выбора подключения к источнику данных в базе данных SQL Azure или базе данных SQL Server на виртуальной машине Azure. 
	- 	DocumentDB используется для выбора подключения к источнику данных для этого типа источника данных. 

   В случае с SQL Azure и DocumentDB подписка должна содержать базу данных. Вы можете вставить строку подключения, если она известна, или выбрать источник данных, ранее созданный пользователем, обладающим правами записи в вашей подписке.

5. Щелкните **Настроить целевой индекс**, чтобы завершить создание индекса по умолчанию.
	- **Ключ** — обязательное поле. Поле, выбранное для ключа, должно представлять собой строку, которая содержит уникальные значения.
	- Имя и тип поля обычно заполняются автоматически. Тип данных можно изменять.
	- Выберите атрибуты для каждого поля:
		- «Доступный для получения»: возвращает поле в результатах поиска.
		- «Фильтруемый»: позволяет ссылаться на поле в выражениях фильтра.
		- «Сортируемый»: позволяет использовать поле при сортировке.
		- «Аспектируемый»: позволяет использовать поле в фасетной навигации.
		- «Доступный для поиска»: позволяет использовать полнотекстовый поиск.
	- Щелкните вкладку **Анализатор**, чтобы указать анализатора языка на уровне поля. Дополнительные сведения см. в статье [Создание определения индекса для многоязычного документа в службе поиска Azure](search-language-support.md).
	- Щелкните **Средство подбора**, чтобы включить автозавершение или предложения упреждающих запросов.

6. Щелкните **Импортировать данные**, чтобы выполнить операцию импорта с помощью параметра «Запустить сейчас» или настроить регулярное расписание.

Во время только что завершенной операции импорта данных был незаметно создан индексатор. Теперь этот индексатор можно отредактировать напрямую, изменив любой из его компонентов.
	
##Изменение существующего индексатора

На панели мониторинга службы дважды щелкните плитку «Индексатор», чтобы открыть список всех индексаторов, созданных для вашей подписки. Дважды щелкните один из индексаторов, чтобы запустить, изменить или удалить его.

<!---HONumber=AcomDC_0224_2016-->