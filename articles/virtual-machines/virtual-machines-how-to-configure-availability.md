<properties
	pageTitle="Настройка группы доступности для классических виртуальных машин | Microsoft Azure"
	description="Настройка группы доступности для новой или существующей виртуальной машины в классической модели развертывания с помощью классического портала Azure и Azure PowerShell."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="cynthn"/>

# Как настроить группу доступности для виртуальных машин в классической модели развертывания

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов. Группы доступности можно также настраивать в развертываниях диспетчера ресурсов.


Группа доступности позволяет поддерживать доступность виртуальных машин во время простоев, например связанных с обслуживанием. Размещение двух или более одинаково настроенных виртуальных машин в группе доступности создает избыточность, необходимую для обеспечения доступности приложений или служб, выполняемых на виртуальной машине. Дополнительные сведения см. в статье [Управление доступностью виртуальных машин][].

Чтобы обеспечить постоянную доступность и эффективность работы приложения, рекомендуется использовать и группы доступности, и балансировку нагрузки конечных точек. Информация о балансировке нагрузки конечных точек представлена в статье [Балансировка нагрузки для служб инфраструктуры Azure][].

В классической модели развертывания поместить виртуальную машину в группы доступности можно одним из двух способов:

- [Вариант 1. Одновременное создание виртуальной машины и группы доступности][]. Затем добавляйте создаваемые виртуальные машины в эту группу.
- [Вариант 2. Добавление существующей виртуальной машины к группе доступности][].

>[AZURE.NOTE] В классической модели виртуальные машины, которые вы хотите поместить в одну группу доступности, должны относиться к одной облачной службе.

## <a id="createset"> </a>Вариант 1. Одновременное создание виртуальной машины и группы доступности##

Для этого можно использовать классический портал Azure или команды Azure PowerShell.

На классическом портале Azure

1. Войдите на классический портал Azure, если вы этого еще не сделали.

2. На панели команд нажмите **Создать**.

3. Щелкните **Виртуальная машина** и **Из коллекции**.

4. На первых двух экранах выберите образ, укажите имя пользователя и пароль и т. д. Более подробно этот процесс описан в статье [Создание виртуальной машины под управлением Windows][].

5. На третьем экране вы можете настроить ресурсы сети, хранилища и доступности. Выполните следующее:

	1. Выберите имя нужной облачной службы. Оставьте выбранным вариант **Создать новую облачную службу**, если вы не добавляете новую виртуальную машину в существующую облачную службу виртуальных машин. Затем укажите имя в поле **DNS-имя облачной службы**. DNS-имя становится частью URI, который используется для связи с виртуальной машиной. Облачная служба играет роль группы связи и изоляции. Все виртуальные машины в одной облачной службе могут взаимодействовать друг с другом, их можно настроить для балансировки нагрузки и добавить в одну группу доступности.

	2. В поле **Регион, территориальная группа, виртуальная сеть** укажите виртуальную сеть, если вы планируете ее использовать. **Внимание!** Если вы хотите, чтобы виртуальная машина использовала виртуальную сеть, при создании этой виртуальной машины обязательно подключите ее к виртуальной сети. Вы не сможете подключить виртуальную машину к виртуальной сети после создания ВМ. Дополнительные сведения см. в статье [Общие сведения о виртуальных сетях][].

	3. Создайте группу доступности. В поле **Группа доступности** выберите вариант **Создать группу доступности**. Укажите имя группы.

	4. Создайте конечные точки по умолчанию и при необходимости добавьте дополнительные конечные точки. Их также можно добавить позже.

	![Создание группы доступности для новой виртуальной машины](./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png)

6. На четвертом экране выберите расширения, которые хотите установить. Расширения обеспечивают функциональность, которая облегчает управление виртуальной машиной, например запуск антивирусного ПО или восстановление паролей. Информацию см. в разделе [Агент виртуальных машин Azure и расширения виртуальных машин](virtual-machines-extensions-agent-about.md).

7.	Щелкните стрелку, чтобы создать виртуальную машину и группу доступности.

	Из панели мониторинга новой виртуальной машины щелкните **Настройки**, чтобы убедиться, что виртуальная машина принадлежит к новой группе доступности.

Чтобы использовать команды Azure PowerShell для создания виртуальной машины Azure и добавления ее в новую или существующую группу доступности, ознакомьтесь со следующими статьями.

- [Использование Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)
- [Использование Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Linux](virtual-machines-ps-create-preconfigure-linux-vms.md)

## <a id="addmachine"> </a>Вариант 2. Добавление существующей виртуальной машины к группе доступности##

На классическом портале Azure можно добавить существующие виртуальные машины к существующей группе доступности либо создать для них новую. (Учитывайте, что виртуальные машины из одной группы доступности должны входить в одну облачную службу.) Выполняемые действия практически идентичны. С помощью Azure PowerShell можно добавить виртуальную машину к существующей группе доступности.

1. Войдите на классический портал Azure, если вы этого еще не сделали.

2. На панели команд щелкните **Виртуальные машины**.

3. Выберите из списка имя виртуальной машины, которую хотите добавить к группе.

4. На вкладках под именем виртуальной машины щелкните **Настроить**.

5. В разделе "Параметры" найдите **Группу доступности**. Выполните одно из следующих действий.

	О. Выберите пункт **Создать группу доступности** и введите имя для этой группы.

	B. Выберите пункт **Выбрать группу доступности** и выберите имя группы из списка.

	![Создание группы доступности для существующей виртуальной машины](./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png)

6. Щелкните **Сохранить**.

Чтобы использовать команды Azure PowerShell, откройте сеанс Azure PowerShell уровня администратора и выполните следующую команду. Для заполнителей (таких как &lt;VmCloudServiceName&gt;) замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

>[AZURE.NOTE] Возможно, вам нужно будет перезапустить виртуальную машину, чтобы завершить ее добавление к группе доступности.

## Дополнительные ресурсы

[Статьи по виртуальным машинам в управлении службами]

<!-- LINKS -->
[Вариант 1. Одновременное создание виртуальной машины и группы доступности]: #createset
[Вариант 2. Добавление существующей виртуальной машины к группе доступности]: #addmachine

[Балансировка нагрузки для служб инфраструктуры Azure]: virtual-machines-load-balance.md
[Управление доступностью виртуальных машин]: virtual-machines-manage-availability.md
[Создание виртуальной машины под управлением Windows]: virtual-machines-windows-tutorial.md
[Общие сведения о виртуальных сетях]: virtual-networks-overview.md
[Статьи по виртуальным машинам в управлении службами]: https://azure.microsoft.com/documentation/articles/?tag=azure-service-management&service=virtual-machines

<!---HONumber=AcomDC_0204_2016-->