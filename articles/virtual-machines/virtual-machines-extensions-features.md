<properties
 pageTitle="Расширения и компоненты виртуальной машины | Microsoft Azure"
 description="Узнайте о расширениях, доступных для виртуальных машин Azure и сгруппированных по предоставляемым функциям или улучшениям."
 services="virtual-machines"
 documentationCenter=""
 authors="squillace"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager"/>

<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="12/08/2015"
 ms.author="rasquill"/>
#Обзор расширений и компонентов виртуальной машины

Microsoft Azure предлагает расширения ВМ, которые созданы корпорацией Майкрософт и доверенными сторонними поставщиками и которые предоставляют функции безопасности, среды выполнения, отладки, управления и многие другие. Вы можете использовать их возможности для повышения эффективности работы с виртуальными машинами Azure. Этот раздел описывает различные функции расширений ВМ Azure, доступные для пользователей виртуальных машин Windows и Linux, и содержит ссылки на документацию для пользователей каждой ОС.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



Дополнительные сведения об агентах виртуальных машин и их использовании для поддержки расширений виртуальных машин см. в [обзоре агента и расширений виртуальной машины](virtual-machines-extensions-install.md).

##Расширения виртуальной машины Azure

Расширения ВМ реализуют большую часть важных функций, которые могут вам понадобиться при работе с виртуальными машинами, в том числе такие базовые возможности, как сброс паролей, настройка протокола RDP и множество других. Так как постоянно добавляются новые расширения, количество доступных функций, поддерживаемых виртуальными машинами Azure, неуклонно растет. По умолчанию, когда вы создаете виртуальную машину из коллекции образов, устанавливается несколько основных расширений ВМ, например **IaaSDiagnostics** (пока только для ВМ Windows), **VMAccess** и **BGInfo** (тоже пока только для ВМ Windows). Впрочем, не все расширения реализуются одновременно и для Windows, и для Linux. Это связано с постоянным процессом обновления функций и создания новых расширений.

##Подключение и базовое управление

Следующие модули важны для включения, повторного включения или отключения базовых соединений с виртуальными машинами после их создания и запуска.

|Имя расширения ВМ|Описание функции|Дополнительные сведения
|---|---|---|
|VMAccessAgent (Windows)|Создание, обновление и сброс пользовательской информации и конфигурации подключений RDP и SSH.|[Windows](virtual-machines-extensions-customscript.md)
|VMAccessForLinux (Linux)|Создание, обновление и сброс пользовательской информации и конфигурации подключений RDP и SSH.|[Linux](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)

##Управление развертыванием и конфигурацией

Следующие расширения реализуют различные виды развертывания и различные сценарии и функции управления конфигурацией. Также см. раздел об операциях ВМ и управлении ниже.

|Имя расширения ВМ|Описание функции|Дополнительные сведения|
|---|---|---|
|**MSEnterpriseApplication**|Реализует функции для поддержки в Windows System Center.|[Роли виртуальных машин System Center 2012 R2](http://social.technet.microsoft.com/wiki/contents/articles/18274.system-center-2012-r2-virtual-machine-role-authoring-guide-resource-extension-package.aspx)|
|**Octopus Deploy**(на основе расширения DSC)|Поддерживает автоматизированное развертывание веб-приложений ASP.NET и служб Windows в средах разработки, тестирования и рабочих средах.|[Приступая к работе с Octopus Deploy](http://docs.octopusdeploy.com/display/OD/Getting%20started)|
|**Диспетчер выпусков Visual Studio** (на основе расширения DSC)|Поддерживает непрерывное развертывание с использованием Visual Studio.|[Автоматизирование развертываний с помощью управления выпусками](https://msdn.microsoft.com/Library/vs/alm/Release/overview)|
|**CentosChefClient**|||
|**ChefClient**|Создает клиент Chef в Windows. (Можно использовать также расширение DSC, описанное ниже).|[Chef и Microsoft Azure](https://www.getchef.com/solutions/azure/)|
|**LinuxChefClient**|||
|**DockerExtension**|Устанавливает демон Docker для поддержки удаленных команд Docker.|[Использование расширения виртуальной машины Docker](virtual-machines-docker-vm-extension.md)Подробные сведения см. в [руководстве пользователя по расширению ВМ Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md)|
|**DSC**|Расширение PowerShell DSC (Настройка требуемого состояния).|[Расширение Azure PowerShell DSC (Настройка требуемого состояния)](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx)|
|**PuppetEnterpriseAgent**|Реализует функции Puppet Enterprise. |[Puppet в Azure](http://puppetlabs.com/solutions/microsoft)|
|**CustomScriptExtension** (Windows)**CustomScriptForLinux**(Linux)|Вызывает пользовательские скрипты на виртуальной машине в любое время: при запуске или во время существования.|[Расширение пользовательских сценариев](virtual-machines-extensions-customscript.md) | [Linux](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript)|
|**AzureCATExtensionHandler**|Использует диагностические данные, собранные **IaaSDiagnostics**, и некоторые другие источники данных, например [метрики аналитики хранилища Azure](https://msdn.microsoft.com/library/azure/hh343270.aspx), и преобразует их в объединенные наборы данных в формате, пригодном для использования в процессе управления узлом SAP.|[Расширенный мониторинг Azure для SAP](https://azure.microsoft.com/blog/2014/06/04/azure-enhanced-monitoring-for-sap/)|

##Безопасность и защита

Расширения, описанные в этом разделе, предоставляют критически важные функции безопасности для виртуальных машин Azure.

|Имя расширения ВМ|Описание функции|Дополнительные сведения|
|---|---|---|
|**CloudLinkSecureVMWindowsAgent**|Предоставляет клиентам Microsoft Azure возможность шифровать данные своих виртуальных машин с использованием общей мультитенантной инфраструктуры, а также полностью управлять ключами, используемыми для шифрования данных в хранилище Azure.|[Защита виртуальных машин Microsoft Azure с использованием шифрования BitLocker и собственного шифрования ОС](http://www.cloudlinktech.com/azure)|
|**McAfeeEndpointSecurity**|Защита виртуальных машин от вредоносных программ.|[McAfee](https://www.mcafeeasap.com/MarketingContent/default.aspx)|
|**TrendMicroDSA**|Включает поддержку платформы TrendMicro Deep Security, которая предоставляет функции обнаружения и предотвращения вторжений, брандмауэра, защиты от вредоносных программ, политик Web Reputation, проверки журнала и мониторинга целостности.|[Установка и настройка Trend Micro Deep Security как услуги на ВМ Azure](virtual-machines-install-trend.md)|
|**PortalProtectExtension**|Защита среды Microsoft SharePoint от угроз.|[Защита развертывания SharePoint на Azure](http://blog.trendmicro.com/securing-sharepoint-deployment-azure/)|
|**IaaSAntimalware**|Microsoft Antimalware для облачных служб и виртуальных машин Azure представляет собой систему защиты в реальном времени для обнаружения и удаления вирусов, шпионских и других вредоносных программ. Вы можете настроить оповещения о попытках установки вредоносных или нежелательных программ в вашей системе.|[Загрузите документацию по защите от вредоносных программ](http://go.microsoft.com/fwlink/?linkid=398023&clcid=0x409)|
|**SymantecEndpointProtection**|Symantec Endpoint Protection 12.1.4 обеспечивает защиту и повышение производительности для физических и виртуальных систем.|[Установка и настройка Symantec Endpoint Protection на виртуальной машине Azure](virtual-machines-install-symantec.md)

##Операции ВМ и управление

Поддерживает общие функции по управлению операциями и реакции на события. См. также раздел по управлению развертыванием и конфигурацией, расположенный выше.

|**Имя расширения ВМ**|Описание функции|Дополнительные сведения|
|---|---|---|
|**AzureVmLogCollector**|Вы можете использовать запускаемое по запросу расширение **AzureVMLogCollector** для разовых операций по сбору журналов из одной или нескольких виртуальных машин облачной службы (с веб-ролями или рабочими ролями) и передачи собранных файлов в учетную запись хранения Azure. Все эти действия выполняются без удаленного входа на эти виртуальные машины. |[Расширение AzureLogCollector](virtual-machines-extensions-log-collector.md)|
|**IaaSDiagnostics**|Включает, отключает и настраивает систему диагностики Azure, а также используется **AzureCATExtensionHandler** для поддержки мониторинга SAP.|[Мониторинг виртуальной машины Microsoft Azure с помощью расширения диагностики Azure](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/)|
|**OSPatchingForLinux**|Позволяет администраторам виртуальной машины Azure автоматизировать обновления ОС виртуальной машины с использованием пользовательских конфигураций. Вы можете использовать расширение OSPatching для настройки обновления ОС виртуальных машин. Вы можете выбрать частоту и время установки исправлений ОС, указать, какие исправления следует устанавливать и как выполнять перезагрузку после обновления.|[Запись в блоге о расширении исправлений ОС](https://azure.microsoft.com/blog/2014/10/23/automate-linux-vm-os-updates-using-ospatching-extension/). Также ознакомьтесь с файлом сведений и исходным кодом на сайте Github на странице [OS Patching Extension](https://github.com/Azure/azure-linux-extensions).|

##Разработка и отладка

Данные расширения ВМ приводятся для полноты списка. Они обеспечивают поддержку функций, связанных с Visual Studio, но не предназначены для использования напрямую.

|Имя расширения ВМ|Описание функции|Дополнительные сведения|
|---|---|---|
|**VS14CTPDebugger**|Поддерживает удаленную отладку в Visual Studio с помощью Azure SDK 2.4.|[Удаленная отладка в Visual Studio](https://msdn.microsoft.com/library/y7f5zaaa.aspx)|
|**VS2013Debugger**|Поддерживает удаленную отладку в Visual Studio с помощью Azure SDK 2.4.||
|**VS2012Debugger**|Поддерживает удаленную отладку в Visual Studio с помощью Azure SDK 2.4.||
|**RemoteDebugVS14CTP**|Поддерживает удаленную отладку через Visual Studio с помощью Azure SDK 2.3.||
|**RemoteDebugVS2013**|Поддерживает удаленную отладку через Visual Studio с помощью Azure SDK 2.3.||
|**RemoteDebugVS2012**|Поддерживает удаленную отладку через Visual Studio с помощью Azure SDK 2.3.||
|**WebDeployForVSDevTest**|Устанавливает и настраивает службы IIS и веб-развертывание на Windows Server. Удаление или отключение не поддерживается.|

##Прочие функции

Эти расширения обеспечивают другие полезные функции виртуальной машины.

|Имя расширения ВМ|Описание функции|Дополнительные сведения|
|---|---|---|
|**BGInfo**|Отображает на рабочем столе сводную схему полезной информации о сервере во время использования RDP.|[Расширение BGInfo](https://msdn.microsoft.com/library/dn606289.aspx)|
|**HpcVmDrivers**|Устанавливает, настраивает и поддерживает удаленный доступ к памяти (RDMA) драйверов сетевых устройств на виртуальных машинах размера A8 или A9, работающих под управлением Windows Server 2012 R2 или Windows Server 2012. Включает кластерные виртуальные машины размера A8 или A9 для использования сети RDMA при запуске параллельных приложений MPI.|[Сведения об экземплярах A8, A9, A10 и A11 с большим объемом вычислений](virtual-machines-a8-a9-a10-a11-specs.md)

<!---HONumber=AcomDC_0128_2016-->