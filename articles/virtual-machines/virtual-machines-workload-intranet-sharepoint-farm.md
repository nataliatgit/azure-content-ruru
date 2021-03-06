<properties
	pageTitle="Ферма SharePoint Server 2013 в Azure | Microsoft Azure"
	description="Определение полезности фермы SharePoint Server 2013 в Azure, настройка тестовой среды и развертывание конфигурации высокого уровня доступности."
	services="virtual-machines"
	documentationCenter=""
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

# Службы инфраструктуры Azure: интрасетевая ферма SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.

Работа с фермой SharePoint в Microsoft Azure имеет целый ряд преимуществ: простота настройки, возможность быстро увеличить емкость фермы или оптимизировать основные функции. Многие фермы SharePoint перерастают из стандартных высокодоступных трехуровневых конфигураций в фермы с десятком, а то и больше серверов, оптимизированных для повышения производительности или выполнения определенных задач (например, распределенное кэширование или поиск).

Виртуальные машины и виртуальная сеть служб инфраструктуры Azure позволяют быстро развернуть и запустить ферму SharePoint, которая прозрачно подключена к локальной сети. Например, вы можете настроить следующее:

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)

Так как виртуальная сеть Azure является расширением локальной сети (соблюдаются все требования к маршрутизации трафика и именованию), пользователи будут работать с ней так же, как если бы она была размещена в локальном центре обработки данных.

Такая конфигурация позволяет легко расширить ферму SharePoint путем добавления новых виртуальных машин Azure. В этом случае текущие затраты на оборудование и обслуживание будут ниже, чем затраты на ферму, развернутую в центре обработки данных.

Размещение интрасетевой фермы SharePoint в службах инфраструктуры Azure — это пример бизнес-приложения. Общие сведения см. в статье [Архитектурный проект бизнес-приложений](http://msdn.microsoft.com/dn630664).

Следующим шагом является настройка размещенной в Azure интрасетевой фермы SharePoint для разработки и тестирования.

> [AZURE.NOTE] Корпорация Майкрософт выпустила SharePoint Server 2016 IT (предварительная версия). Для облегчения установки и тестирования предварительной версии вы можете воспользоваться образом с SharePoint Server 2016 IT (предварительная версия) и предустановленными обязательными компонентами из коллекции виртуальных машин Azure. Дополнительные сведения можно найти в статье [Тестирование SharePoint Server 2016 IT (предварительная версия) в Azure](https://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Создание интрасетевой фермы SharePoint для разработки и тестирования, размещенной в Azure

Существует два варианта создания среды разработки и тестирования для фермы SharePoint, размещенной в Azure:

- создание исключительно облачной виртуальной сети;
- создание распределенной виртуальной сети.

Эти среды вы можете создать бесплатно, используя свою [подписку Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) или [пробную подписку Azure](https://azure.microsoft.com/pricing/free-trial/).

### создание исключительно облачной виртуальной сети;

Такая виртуальная сеть не соединена с локальной сетью. Если вы хотите быстро создать базовую или высокодоступную ферму SharePoint, см. статью [Создание ферм серверов SharePoint](virtual-machines-sharepoint-farm-azure-preview.md). Базовая конфигурация фермы SharePoint выглядит так:

![](./media/virtual-machines-workload-intranet-sharepoint-farm/Non-HAFarm.png)

### Распределенная виртуальная сеть

Распределенная виртуальная сеть соединена с локальной сетью через подключение VPN или ExpressRoute по принципу "сайт — сайт". Если вы хотите создать среду разработки и тестирования с практически финальной конфигурацией, чтобы поэкспериментировать с подключением к серверу SharePoint и удаленным администрированием через VPN-подключение, см. статью [Настройка фермы SharePoint интрасети в гибридном облаке для тестирования](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

![](./media/virtual-machines-workload-intranet-sharepoint-farm/CreateSPFarmHybridCloud.png)

Далее вам нужно создать высокодоступную интрасетевую ферму SharePoint в Azure.

## Развертывание интрасетевой фермы SharePoint, размещенной в Azure

Типичная конфигурация функциональной высокодоступной интрасетевой фермы SharePoint выглядит так:

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)

Такая конфигурация состоит из:

- интрасетевой фермы SharePoint с двумя серверами в Интернете и уровнями приложений и баз данных;
- групп доступности SQL Server AlwaysOn с двумя серверами SQL Server и узлом большинства в кластере;
- Два контроллера домена реплики из локального домена Active Directory.

Эта же конфигурация в виде инфографики показана в статье [SharePoint с SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788).

Развертывание этой конфигурации выполняется в следующем порядке.

- Этап 1. Настройка Azure.

	С помощью Azure PowerShell создайте учетную запись хранения, группы доступности и распределенную виртуальную сеть. Подробные сведения см. в статье, посвященной [этапу 1](virtual-machines-workload-intranet-sharepoint-phase1.md).

- Этап 2. Настройка контроллеров домена.

	Настройте два контроллера домена реплик Active Directory и параметры DNS для виртуальной сети. Подробные сведения см. в статье, посвященной [этапу 2](virtual-machines-workload-intranet-sharepoint-phase2.md).

- Этап 3. Настройка инфраструктуры сервера SQL Server

	Подготовьте виртуальные машины SQL Server для работы с SharePoint и создайте кластер SQL Server. Подробные сведения см. в статье, посвященной [этапу 3](virtual-machines-workload-intranet-sharepoint-phase3.md).

- Этап 4. Настройка серверов SharePoint

	Настройте четыре виртуальные машины SharePoint для новой фермы SharePoint. Подробные сведения см. в статье, посвященной [этапу 4](virtual-machines-workload-intranet-sharepoint-phase4.md).

- Этап 5. Создание группы доступности AlwaysOn

	Подготовьте базы данных SharePoint, создайте группу доступности AlwaysOn и добавьте в нее подготовленные базы данных. Подробные сведения см. в статье, посвященной [этапу 5](virtual-machines-workload-intranet-sharepoint-phase5.md).

Сведения о том, как расширить такую ферму SharePoint, см. в статье [Архитектуры Microsoft Azure для SharePoint 2013](http://technet.microsoft.com/library/dn635309.aspx).

## Дальнейшие действия

- Прежде чем приступить к настройке, ознакомьтесь с [общими сведениями](virtual-machines-workload-intranet-sharepoint-overview.md) о рабочей нагрузке.

<!---HONumber=AcomDC_0128_2016-->