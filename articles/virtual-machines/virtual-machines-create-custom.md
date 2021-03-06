<properties
	pageTitle="Создание настраиваемой виртуальной машины | Microsoft Azure"
	description="Из этой статьи вы узнаете, как создать настраиваемую виртуальную машину Windows на классическом портале Azure с использованием классической модели развертывания."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/15/2016"
	ms.author="cynthn"/>

	
# Создание настраиваемой виртуальной машины под управлением Windows


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.
 


*Настраиваемая* виртуальная машина — это просто виртуальная машина, созданная с помощью параметра **Из коллекции**, что дает больший выбор настроек, чем параметр **Быстрое создание**. Вот какие параметры доступны:

- подключение виртуальной машины к виртуальной сети;
- установка агента виртуальной машины Azure и расширений виртуальной машины Azure, например для защиты от вредоносных программ;
- добавление виртуальной машины в существующую облачную службу;
- добавление виртуальной машины в существующую учетную запись хранения;
- добавление виртуальной машины в группу доступности.

> [AZURE.IMPORTANT]Если к виртуальной машине необходимо подключаться с помощью имени узла по виртуальной сети или настроить подключения между организациями, при создании виртуальной машины обязательно укажите виртуальную сеть. Виртуальную машину можно настроить для подключения к виртуальной сети только при ее создании. Дополнительную информацию о виртуальных сетях см. в разделе [Обзор виртуальных сетей Azure](virtual-networks-overview.md).


## Создание виртуальной машины


[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

<!---HONumber=AcomDC_0121_2016-->