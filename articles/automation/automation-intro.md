<properties
	pageTitle="Что такое служба автоматизации Azure | Microsoft Azure"
	description="Узнайте, какие преимущества обеспечивает служба автоматизации Azure, и получите ответы на часто задаваемые вопросы, чтобы приступить к созданию и использованию модулей Runbook и службы Azure Automation DSC."
	services="automation"
	documentationCenter=""
	authors="mgoedtel"
	manager="stevenka"
	editor=""/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="02/23/2016"
	ms.author="magoedte;bwren"/>

# Обзор службы автоматизации Azure


Служба автоматизации Microsoft Azure позволяет пользователям автоматизировать задачи, типичные для выполнения в облачной среде и среде предприятия. Такие задачи обычно выполняются вручную, занимают много времени, подвержены ошибкам и часто повторяются. Она позволяет экономить время и повышает надежность обычных административных задач и даже планирует их автоматическое выполнение через определенные интервалы. Можно автоматизировать процессы, используя модули Runbook, или автоматизировать управление конфигурацией, используя службу настройки требуемого состояния (DSC). В этой статье содержится краткий обзор службы автоматизации Azure и рассматриваются некоторые часто задаваемые вопросы. За более подробной информацией по различным темам обращайтесь к другим статьям данной библиотеки.


## Автоматизация процессов с помощью модулей Runbook

Runbook — это набор задач, которые выполняют какой-либо автоматизированный процесс в службе автоматизации Azure. Это может быть простой процесс, такой как запуск виртуальной машины и создание записи журнала, или сложный Runbook, который включает несколько меньших модулей Runbook и выполняет сложный процесс с участием нескольких ресурсов или даже нескольких облаков и локальных сред.

Например, у вас может иметься существующий ручной процесс для усечения базы данных SQL при приближении к максимальному размеру, который включает несколько шагов, таких как подключение к серверу, подключение к базе данных, получение текущего размера базы данных, проверка превышения порогового значения, выполнение усечения и оповещение пользователей. Вместо выполнения этих шагов вручную можно создать модуль Runbook, который выполнит все эти задачи в рамках одного процесса. Вы запускаете Runbook, вводите необходимые сведения, такие как имя сервера SQL Server, имя базы данных и адрес электронной почты получателя, после чего процесс выполняется без вашего участия.


## Что может автоматизировать модуль Runbook?

Runbook в службе автоматизации Azure основаны на Windows PowerShell или рабочем процессе Windows PowerShell, поэтому они делают все, что может делать PowerShell. Если приложение или служба имеет API, Runbook может работать с ним. Если для приложения есть модуль PowerShell, можно загрузить его в службу автоматизации Azure и включить соответствующие командлеты в модуль Runbook. Модули Runbook в службе автоматизации Azure выполняются в облаке Azure, а значит, могут получать доступ к любым облачным или внешним ресурсам, к которым можно обращаться из облака. [Гибридная рабочая роль Runbook](automation-hybrid-runbook-worker.md) позволяет выполнять модули Runbook в локальном центре обработки данных, управляя локальными ресурсами.


## Получение модулей Runbook из сообщества

[Коллекция Runbook](automation-runbook-gallery.md#runbooks-in-runbook-gallery) содержит модули Runbook корпорации Майкрософт и сообщества, которые можно использовать в среде без изменений или настроить по своему усмотрению. Используя их для справки, вы можете научиться создавать собственные модули Runbook. Кроме того, вы можете загружать собственные модули Runbook в коллекцию, если считаете, что они могут оказаться полезными для других пользователей.


## Создание модулей Runbook с помощью службы автоматизации Azure 

Вы можете [создавать собственные модули Runbook](automation-creating-importing-runbook.md) с нуля или изменять модули в [коллекции Runbook](http://msdn.microsoft.com/library/azure/dn781422.aspx) в соответствии с собственными требованиями. Вы можете выбрать три разных [типа Runbook](automation-runbook-types.md) на основе ваших требований и возможностей PowerShell. Если вы предпочитаете работать непосредственно с кодом PowerShell, то можно использовать [Runbook PowerShell](automation-runbook-types.md#powershell-runbooks) или [Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks), которые вы можете изменять в автономном режиме или в [текстовом редакторе](http://msdn.microsoft.com/library/azure/dn879137.aspx) на портале Azure. Если вы предпочитаете изменять модули Runbook, не прибегая к работе над кодом, создайте [графический модуль Runbook](automation-runbook-types.md#graphical-runbooks). Для этого можно использовать [графический редактор](automation-graphical-authoring-intro.md) на портале Azure.

Предпочитаете смотреть, а не читать? Посмотрите указанный ниже видеоролик, показанный на презентации Microsoft Ignite в мае 2015 года. Примечание. Несмотря на то, что основные концепции и жизненный цикл, о которых рассказывается в этом видеоролике, по-прежнему актуальны, с момента записи этого видеоролика служба автоматизации Azure сделала большой шаг вперед. Теперь она оснащена более функциональным пользовательским интерфейсом на портале Azure и поддерживает дополнительные возможности.

> [AZURE.VIDEO microsoft-ignite-2015-automating-operational-and-management-tasks-using-azure-automation]


## Автоматизация управления конфигурацией с помощью службы настройки требуемого состояния 

[PowerShell DSC](https://technet.microsoft.com/library/dn249912.aspx) — это новая платформа управления, позволяющая управлять конфигурацией физических узлов и виртуальных машин с помощью декларативного синтаксиса PowerShell, а также развертывать и принудительно выполнять ее. Конфигурации можно определить на центральном опрашивающем сервере DSC, что даст целевым компьютерам возможность автоматически получать и применять их. Служба DSC предоставляет набор командлетов PowerShell, которые можно использовать для управления конфигурациями и ресурсами.

[Служба Azure Automation DSC](automation-dsc-overview.md) — это облачное решение для PowerShell DSC, которое предоставляет службы, необходимые для корпоративных сред. Вы можете управлять ресурсами DSC в службе автоматизации Azure и применять конфигурации к виртуальным машинам или физическим компьютерам, получающим их с опрашивающего сервера DSC в облаке Azure. Кроме того, вы получаете доступ к службам отчетов, информирующим о важных событиях, например в случае отклонения узлов от назначенной им конфигурации или применения новой конфигурации.


## Создание собственных конфигураций DSC с помощью службы автоматизации Azure

[Конфигурации DSC](automation-dsc-overview.md#azure-automation-dsc-terms) указывают требуемое состояние узла. К нескольким узлам можно применить одну конфигурацию, чтобы гарантировать их идентичное состояние. Можно создать конфигурацию с помощью любого текстового редактора на локальном компьютере, а затем импортировать ее в службу автоматизации Azure, где ее можно компилировать и применить к узлам.


## Получение модулей и конфигураций 

[Модули PowerShell](automation-runbook-gallery.md#modules-in-powershell-gallery), содержащие командлеты, которые разрешается использовать в модулях Runbook и конфигурациях DSC, можно получить из [коллекции PowerShell](http://www.powershellgallery.com/). Вы можете открыть эту коллекцию на портале Azure и импортировать модули непосредственно в службу автоматизации Azure. Также можно скачать и импортировать модули вручную. Модули нельзя устанавливать непосредственно с портала Azure, но вы можете скачать и установить их, как любой другой модуль.


## Примеры практического применения автоматизации Azure 

Ниже приведено несколько примеров сценариев, которые можно автоматизировать с помощью службы автоматизации Azure.

* Создание и копирование виртуальных машин в разных подписках Azure. 
* Планирование копирования файла с локального компьютера в контейнер хранилища BLOB-объектов Azure. 
* Автоматизация функций безопасности, таких как отклонение запросов от клиента при обнаружении атаки типа "отказ в обслуживании". 
* Гарантия того, что компьютеры постоянно соответствуют заданным политикам безопасности.
* Управление непрерывным развертыванием кода приложений в облаке и локальной инфраструктуре. 
* Построение леса Active Directory в Azure для лабораторной среды. 
* Усечение таблицы в базе данных SQL, если размер базы данных приближается к максимальному. 
* Удаленное обновление параметров среды для веб-сайта Azure. 


## Как служба автоматизации Azure связана с другими средствами автоматизации?

Функция [Service Management Automation (SMA)](http://technet.microsoft.com/library/dn469260.aspx) предназначена для автоматизации задач управления в частном облаке. Она устанавливается локально в центре обработки данных в качестве компонента [Microsoft Azure Pack](https://www.microsoft.com/ru-RU/server-cloud/). SMA и служба автоматизации Azure используют один и тот же формат Runbook, основанный на Windows PowerShell и рабочем процессе Windows PowerShell, однако SMA не поддерживает [графические Runbook](automation-graphical-authoring-intro.md).

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) предназначен для автоматизации локальных ресурсов. Он использует не такой формат модулей Runbook, как служба автоматизации Azure и автоматизация управления службами, и имеет графический интерфейс для создания модулей Runbook без каких-либо сценариев. Его модули Runbook формируются действиями из пакетов интеграции, написанными специально для оркестратора.


## Где можно получить дополнительные сведения? 

Дополнительные сведения о службе автоматизации Azure и создании собственных модулей Runbook можно найти в различных источниках.

* **Библиотека службы автоматизации Azure**. Сейчас вы находитесь в ней. Статьи в этой библиотеке содержат полную документацию по настройке и администрированию службы автоматизации Azure, а также по созданию собственных модулей Runbook. 
* [Командлеты Azure PowerShell](http://msdn.microsoft.com/library/jj156055.aspx). Здесь приведены сведения об автоматизации операций Azure с помощью Windows PowerShell. Модули Runbook используют эти командлеты для работы с ресурсами Azure. 
* [Блог об управлении](https://azure.microsoft.com/blog/tag/azure-automation/) содержит последние сведения о службе автоматизации Azure и других технологиях управления корпорации Майкрософт. Подпишитесь на этот блог и следите за новостями от группы разработчиков службы автоматизации Azure. 
* [Форум о службе автоматизации](http://go.microsoft.com/fwlink/p/?LinkId=390561). Тут можно публиковать вопросы о службе автоматизации Azure и адресовать их сообществу и представителям Майкрософт. 
* [Командлеты службы автоматизации Azure](https://msdn.microsoft.com/library/mt244122.aspx) предоставляют сведения для автоматизации задач администрирования. Они включают командлеты для управления учетными записями автоматизации, ресурсами, модулями Runbook и DSC.


## Можно ли оставить отзыв? 

**Да! Нам нужна обратная связь.** Если вы ищете модуль интеграции или определенное решение для службы автоматизации Azure, в котором используются модули Runbook, разместите соответствующий запрос на сайте "Центр сценариев". Если вы хотите оставить отзыв или запрос на ту или иную функцию для службы автоматизации Azure, оставляйте их на сайте [User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback). Спасибо!

<!---HONumber=AcomDC_0302_2016-->