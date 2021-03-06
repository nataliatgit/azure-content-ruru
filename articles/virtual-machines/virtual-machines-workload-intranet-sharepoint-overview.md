<properties
	pageTitle="Развертывание фермы SharePoint Server 2013 | Microsoft Azure"
	description="Высокодоступные фермы SharePoint Server 2013 с использованием групп доступности AlwaysOn для SQL Server развертываются в Azure в пять этапов."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="josephd"/>

# Развертывание среды SharePoint с группами доступности AlwaysOn для SQL Server на платформе Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.

В этой статье приведены пошаговые инструкции по развертыванию фермы SharePoint 2013 для интрасети с группами доступности AlwaysOn для SQL Server. В состав фермы входят перечисленные ниже компьютеры.

- Два веб-сервера SharePoint
- Два сервера приложений SharePoint
- Два сервера баз данных
- Один сервер узла большинства кластера
- два контроллера домена.

Вот схема конфигурации (для каждого сервера указаны шаблонные имена):

![](./media/virtual-machines-workload-intranet-sharepoint-overview/workload-spsqlao_05.png)

Два компьютера для каждой роли обеспечивают высокий уровень доступности. Все виртуальные машины расположены в одном регионе. Каждая группа виртуальных машин, выполняющих определенную роль, находится в собственной группе доступности.

## Спецификация

Для такой типичной конфигурации требуется следующий набор компонентов и служб Azure:

- девять виртуальных машин;
- четыре дополнительных диска данных для контроллеров домена и серверов SQL Server;
- четыре группы доступности;
- одна распределенная виртуальная сеть;
- одна учетная запись хранения;
- одна подписка Azure.

Вот список виртуальных машин и их размеры по умолчанию для данной конфигурации.

Элемент | Описание виртуальной машины | Образ из коллекции | Размер по умолчанию
--- | --- | --- | ---
1\. | Первый контроллер домена | Центр обработки данных Windows Server 2012 R2 | A2
2\. | Второй контроллер домена | Центр обработки данных Windows Server 2012 R2 | A2
3\. | Первый сервер базы данных | Microsoft SQL Server 2014 Enterprise — Windows Server 2012 R2 | A5
4\. | Второй сервер базы данных | Microsoft SQL Server 2014 Enterprise — Windows Server 2012 R2 | A5
5\. | Узел большинства кластера | Центр обработки данных Windows Server 2012 R2 | A1
6\. | Первый сервер приложений SharePoint | Microsoft SharePoint Server 2013 Trial — Windows Server 2012 R2 | A4
7\. | Второй сервер приложений SharePoint | Microsoft SharePoint Server 2013 Trial — Windows Server 2012 R2 | A4
8\. | Первый веб-сервер SharePoint | Microsoft SharePoint Server 2013 Trial — Windows Server 2012 R2 | A4
9\. | Второй веб-сервер SharePoint | Microsoft SharePoint Server 2013 Trial — Windows Server 2012 R2 | A4

Чтобы вычислить расчетную стоимость данной конфигурации, см. [Калькулятор цен Azure](https://azure.microsoft.com/pricing/calculator/).

1. В разделе **Модули** выберите параметр **Рассчитать**, а затем щелкните **Виртуальные машины** необходимое число раз, чтобы получился список из девяти виртуальных машин.
2. Для каждой виртуальной машины укажите следующие параметры:
	- Необходимый регион
	- Тип — **Windows**
	- Уровень цен — **Стандартный**
	- **Размер экземпляра** — размер по умолчанию из предыдущей таблицы или предполагаемый размер

> [AZURE.NOTE] Калькулятор цен Azure не включает дополнительные затраты на лицензирование SQL Server для двух виртуальных машин под управлением SQL Server 2014 Enterprise. Дополнительные сведения см. в статье [Цены на виртуальные машины — SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

## Этапы развертывания

Ниже перечислены этапы развертывания этой конфигурации.

- [Этап 1. Настройка Azure](virtual-machines-workload-intranet-sharepoint-phase1.md). Создание учетных записей хранения, групп доступности и распределенной виртуальной сети.
- [Этап 2. Настройка контроллеров домена](virtual-machines-workload-intranet-sharepoint-phase2.md). Создание и настройка реплик контроллеров домена доменных служб Active (AD DS).
- [Этап 3. Настройка инфраструктуры SQL Server](virtual-machines-workload-intranet-sharepoint-phase3.md). Создание и настройка виртуальных машин SQL Server, их подготовка для работы со средой SharePoint и создание кластера.
- [Этап 4. Настройка серверов SharePoint](virtual-machines-workload-intranet-sharepoint-phase4.md). Создание и настройка четырех виртуальных машин SharePoint.
- [Этап 5. Создание группы доступности и добавление баз данных SharePoint](virtual-machines-workload-intranet-sharepoint-phase5.md). Подготовка баз данных и создание группы доступности AlwaysOn для SQL Server.

Данное развертывание фермы SharePoint с группами доступности AlwaysOn для SQL Server соответствует [этой схеме](http://go.microsoft.com/fwlink/?LinkId=394788) и выполняется в соответствии с актуальными рекомендациями.

Эта схема представляет собой пошаговое руководство по созданию функциональной высокодоступной фермы SharePoint в интрасети на базе служб инфраструктуры Azure в соответствии с определенной архитектурой. Дополнительные советы и рекомендации по развертыванию SharePoint 2013 в среде Azure на базе различных архитектур см. в статье [Архитектуры Microsoft Azure для SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx).

Помните о перечисленных ниже моментах.

- Если вы являетесь опытным специалистом по внедрению SharePoint, внесите необходимые изменения в этапы 3–5 в соответствии с собственными требованиями к ферме.
- Если у вас уже есть гибридное облачное развертывание Azure, внесите необходимые изменения в этапы 1–2 (или пропустите их) для создания новой фермы SharePoint в соответствующей подсети.
- Все серверы расположены в одной подсети виртуальной сети Azure. Для реализации дополнительных мер безопасности, эквивалентных изоляции подсетей, можно воспользоваться [сетевыми группами безопасности](../virtual-network/virtual-networks-nsg.md).

Инструкции по созданию среды для разработки или тестирования либо экспериментальной проверке этой конфигурации см. в статье [Настройка фермы SharePoint интрасети в гибридном облаке для тестирования](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

Дополнительные сведения о среде SharePoint с группами доступности AlwaysOn для SQL Server см. в статье [Настройка групп обеспечения доступности SQL Server 2012 AlwaysOn для SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

> [AZURE.NOTE] Корпорация Майкрософт выпустила SharePoint Server 2016 IT (предварительная версия). Для облегчения установки и тестирования предварительной версии вы можете воспользоваться образом с SharePoint Server 2016 IT (предварительная версия) и предустановленными обязательными компонентами из коллекции виртуальных машин Azure. Дополнительные сведения вы можете найти в статье [Тестирование SharePoint Server 2016 IT (предварительная версия) в Azure](https://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Дальнейшие действия

- [Этап 1](virtual-machines-workload-intranet-sharepoint-phase1.md). Начальные действия по настройке этой рабочей нагрузки.

<!---HONumber=AcomDC_0128_2016-->