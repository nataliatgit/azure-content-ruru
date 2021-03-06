<properties
	pageTitle="Высокий уровень доступности и аварийное восстановление для SQL Server | Microsoft Azure"
	description="Обсуждение различных типов стратегий HADR для SQL Server на виртуальных машинах Azure."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="01/22/2016"
	ms.author="jroth" />

# Высокий уровень доступности и аварийное восстановление для SQL Server на виртуальных машинах Azure

## Обзор

Виртуальные машины Microsoft Azure с SQL Server могут помочь удешевить базу данных, обеспечивающую высокий уровень доступности и аварийное восстановление (HADR). Виртуальные машины Azure поддерживают большинство решений HADR для SQL Server как для использования только в службе Azure, так и в качестве гибридных решений. Если решение предназначено только для использования в службе Azure, в ней выполняется вся система HADR. В гибридной конфигурации часть решения выполняется в Azure, а другая часть — в локальной сети организации. Гибкость среды Azure позволяет полностью или частично перемещать ресурсы в Azure в соответствии с бюджетом и требованиями систем баз данных SQL Server к HADR.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.


## Целесообразность использования решения HADR

Вам следует убедиться, что ваша система баз данных поддерживает возможности HADR, оговоренные в соглашении об уровне обслуживания (SLA). Тот факт, что Azure обеспечивает механизмы высокого уровня доступности, такие как восстановление облачных служб и обнаружение восстановления после сбоя для виртуальных машин, сам по себе не гарантирует, что вы выполните условия этого соглашения. Эти механизмы поддерживают высокий уровень доступности исключительно виртуальных машин, а не экземпляра SQL Server, который выполняется на этих виртуальных машинах. В работе экземпляра SQL Server может возникнуть ошибка, даже если виртуальная машина подключена и исправна. Кроме того, даже доступные в Azure механизмы обеспечения высокого уровня доступности допускают простои виртуальных машин, связанные с такими событиями, как восстановление после сбоев программного или аппаратного обеспечения либо обновление операционной системы.

Кроме того, геоизбыточного хранилища (GRS) в Azure, которое реализуется с помощью такой функции, как георепликация, может оказаться недостаточно для аварийного восстановления баз данных. Так как при георепликации данные отправляются асинхронно, последние обновления могут быть потеряны в случае сбоя. Дополнительные сведения об ограничениях относительно георепликации приведены [в разделе со сведениями о том, что георепликация не поддерживается для файлов данных и журналов на отдельных дисках](#geo-replication-support).

## Архитектуры развертывания HADR

В Azure поддерживаются следующие технологии HADR для SQL Server.

- [Группы доступности AlwaysOn](https://technet.microsoft.com/library/hh510230.aspx)
- [Зеркальное отображение базы данных](https://technet.microsoft.com/library/ms189852.aspx)
- [Доставка журналов](https://technet.microsoft.com/library/ms187103.aspx)
- [Резервное копирование и восстановление с помощью службы хранилища больших двоичных объектов Azure](https://msdn.microsoft.com/library/jj919148.aspx)
- [Экземпляры отказоустойчивого кластера AlwaysOn](https://technet.microsoft.com/library/ms189134.aspx)

Эти технологии можно объединить, чтобы реализовать решение SQL Server, поддерживающее возможности высокого уровня доступности и аварийного восстановления. В зависимости от используемой технологии для гибридного развертывания может потребоваться VPN-туннель с виртуальной сетью Azure. В разделах ниже приведено несколько примеров архитектур развертывания.

## Только в Azure: решения высокого уровня доступности

У вас может быть решение высокого уровня доступности для баз данных SQL Server в Azure, использующее группы доступности AlwaysOn или зеркальное отображение базы данных.

|Технология|Примеры архитектур|
|---|---|
|**Группы доступности AlwaysOn**|Все реплики доступности, выполняемые на виртуальных машинах Azure для обеспечения высокого уровня доступности в одном регионе. Необходимо настроить виртуальную машину контроллера домена, так как для отказоустойчивой кластеризации Windows Server (WSFC) требуется домен Active Directory.<br/> ![Группы доступности AlwaysOn](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_ha_always_on.gif)<br/>Дополнительные сведения см. в статье [Настройка групп доступности AlwaysOn в Azure (графический пользовательский интерфейс)](virtual-machines-sql-server-alwayson-availability-groups-gui.md).|
|**Зеркальное отображение базы данных**|Основной, зеркальный и следящий серверы работают в одном центре обработки данных Azure для обеспечения высокой доступности. Для развертывания можно использовать контроллер домена.<br/>![Зеркальное отображение базы данных](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_ha_dbmirroring1.gif)<br/>Вы также можете развернуть ту же конфигурацию зеркального отображения базы данных без контроллера домена, используя сертификаты сервера.<br/>![Зеркальное отображение базы данных](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_ha_dbmirroring2.gif)|
|**Экземпляры отказоустойчивого кластера AlwaysOn**|Экземпляры отказоустойчивого кластера (FCI), которые требуют наличия общего хранилища, можно создавать двумя способами.<br/><br/>1. Экземпляр FCI в кластере WSFC с двумя узлами, выполняющемся на виртуальных машинах Azure, с хранилищем, поддерживаемым сторонним решением кластеризации. Конкретный пример, в котором используется SIOS DataKeeper, см. в статье [Обеспечение высокой доступности для общей папки с помощью WSFC и стороннего программного обеспечения SIOS Datakeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>2. Экземпляр FCI в кластере WSFC с двумя узлами, выполняющемся на виртуальных машинах Azure, с удаленным общим хранилищем блоков цели iSCSI через ExpressRoute. Например, решение NetApp Private Storage (NPS) предоставляет цели iSCSI через ExpressRoute с Equinix для виртуальных машин Azure.<br/><br/>По любым вопросам, связанным с доступом к данным при отработке отказа, касающимся сторонних решений репликации данных и общих хранилищ, следует обратиться к их поставщикам.<br/><br/>Обратите внимание, что использование экземпляра FCI поверх [хранилища файлов Azure](https://azure.microsoft.com/services/storage/files/) пока не поддерживается, поскольку это решение не использует хранилище Premium. Мы работаем над тем, чтобы обеспечить поддержку этого сценария в ближайшее время.|

## Только в Azure: решения аварийного восстановления

У вас может быть решение аварийного восстановления для баз данных SQL Server в Azure, использующее группы доступности AlwaysOn, зеркальное отображение базы данных или резервное копирование и восстановление с большими двоичными объектами хранилища.

|Технология|Примеры архитектур|
|---|---|
|**Группы доступности AlwaysOn**|Реплики доступности, выполняемые в нескольких центрах обработки данных на виртуальных машинах Azure для аварийного восстановления. Это межрегиональное решение обеспечивает защиту от простоя всего сайта. <br/> ![Группы доступности AlwaysOn](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_dr_alwayson.png)<br/>В пределах региона все реплики должны находиться в одной облачной службе и одной виртуальной сети. Так как каждый регион будет включать отдельную виртуальную сеть, для этих решений требуется подключение между виртуальными сетями. Дополнительную информацию см. в разделе [Создание виртуальной сети с VPN-подключением типа "сеть-сеть" с помощью классического портала Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).|
|**Зеркальное отображение базы данных**|Основной, зеркальный и другие серверы, выполняемые в разных центрах обработки данных для аварийного восстановления. Для развертывания необходимо использовать сертификаты сервера, так как домен Active Directory не может охватывать несколько центров обработки данных.<br/>![Зеркальное отображение базы данных](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_dr_dbmirroring.gif)|
|**Резервное копирование и восстановление с помощью службы хранилища больших двоичных объектов Azure**|Производственные базы данных, резервные копии которых создаются непосредственно в хранилище больших двоичных объектов в другом центре обработки данных для аварийного восстановления.<br/>![Архивация и восстановление](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_dr_backup_restore.gif)<br/>Дополнительные сведения см. в статье [Резервное копирование и восстановление SQL Server в виртуальных машинах Azure](virtual-machines-sql-server-backup-and-restore.md).|

## Гибридная ИТ-среда: решения аварийного восстановления

У вас может быть решение аварийного восстановления для баз данных SQL Server в гибридной ИТ-среде, использующее группы доступности AlwaysOn, зеркальное отображение базы данных и доставку журналов, а также резервное копирование и восстановление с помощью хранилища больших двоичных объектов Azure.

|Технология|Примеры архитектур|
|---|---|
|**Группы доступности AlwaysOn**|Некоторые реплики доступности, выполняемые на виртуальных машинах Azure, и другие реплики, которые выполняются локально для аварийного восстановления на нескольких сайтах. Рабочий сайт может размещаться локально или в центре обработки данных Azure.<br/>![Группы доступности AlwaysOn](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/hybrid_dr_alwayson.gif)<br/>Так как все реплики доступности должны находиться в одном кластере WSFC, он должен охватывать обе сети (кластер WSFC с несколькими подсетями). В этой конфигурации требуется VPN-подключение между Azure и локальной сетью.<br/><br/>Кроме того, для успешного аварийного восстановления баз данных вам следует установить реплику контроллер домена на сайте аварийного восстановления.<br/><br/>Реплику Azure можно добавить в существующую группу доступности AlwaysOn с помощью мастера добавления реплики в среде SSMS. Дополнительные сведения см. в учебнике, в котором рассматривается расширение группы доступности AlwaysOn в Azure.|
|**Зеркальное отображение базы данных**|Один участник выполняется на виртуальной машине Azure, а другой — локально для межсайтового аварийного восстановления с использованием сертификатов сервера. Участники не обязательно должны находиться в одном домене Active Directory. Кроме того, не требуется VPN-подключение.<br/>![Зеркальное отображение базы данных](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/hybrid_dr_dbmirroring.gif)<br/>В другом сценарии зеркального отображения базы данных один участник выполняется на виртуальной машине Azure, а другой — локально в том же домене Active Directory для межсайтового аварийного восстановления. Необходимо [VPN-подключение между виртуальной сетью Azure и локальной сетью](../vpn-gateway/vpn-gateway-site-to-site-create.md).<br/><br/>Для успешного аварийного восстановления баз данных также следует установить реплику контроллера домена на сайте аварийного восстановления.|
|**Доставка журналов**|Один сервер запущен на виртуальной машине Azure, а другой — в локальной сети для межсайтового аварийного восстановления. Доставка журналов зависит от общего доступа к файлам Windows, поэтому требуется VPN-подключение между виртуальной сетью Azure и локальной сетью.<br/>![Доставка журналов](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/hybrid_dr_log_shipping.gif)<br/>Для успешного аварийного восстановления баз данных также следует установить реплику контроллера домена на сайте аварийного восстановления.|
|**Резервное копирование и восстановление с помощью службы хранилища больших двоичных объектов Azure**|Локальные рабочие базы данных, резервные копии которых создаются непосредственно в хранилище больших двоичных объектов Azure для аварийного восстановления.<br/>![Архивация и восстановление](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/hybrid_dr_backup_restore.gif)<br/>Дополнительные сведения см. в статье [Резервное копирование и восстановление SQL Server в виртуальных машинах Azure](virtual-machines-sql-server-backup-and-restore.md).|

## Важные соображения в отношении HADR для SQL Server в Azure

Виртуальные машины, хранилища и сети Azure имеют рабочие характеристики, отличные от характеристик локальной, невиртуализированной ИТ-инфраструктуры. Чтобы успешно реализовать решение HADR для SQL Server в Azure, вы должны понимать эти различия и учитывать их при разработке решения.

### Узлы с высоким уровнем доступности в группе доступности

Группы доступности в Azure позволяют размещать узлы с высоким уровнем доступности в отдельные домены сбоя и домены обновления. Для размещения виртуальных машин Azure в одной группе доступности их необходимо развернуть в одной облачной службе. В одной группе доступности можно разместить только узлы из одной облачной службы. Дополнительные сведения см. в статье [Управление доступностью виртуальных машин](virtual-machines-manage-availability.md).

### Поведение кластера WSFC в сети Azure

Несоответствие службы DHCP в Azure стандарту RFC может привести к сбою некоторых создаваемых конфигураций кластера WSFC, так как сетевому имени кластера назначается повторяющийся IP-адрес, например IP-адрес одного из узлов кластера. Это актуально, если вам нужно реализовать группы доступности AlwaysOn, зависящие от функции WSFC.

Рассмотрим сценарий, в котором вы создаете и подключаете к сети кластер с двумя узлами.

1. Кластер подключается к сети, после чего узел NODE1 запрашивает динамически назначаемый IP-адрес для сетевого имени кластера.

1. Служба DHCP не назначает других IP-адресов, кроме собственного IP-адреса узла NODE1, так как она распознает, что запрос поступает от самого узла NODE1.

1. ОС Windows обнаруживает, что повторяющийся адрес назначен как узлу NODE1, так и сетевому имени кластера, поэтому группе кластера по умолчанию не удается подключиться к сети.

1. Группа кластера по умолчанию перемещается на узел NODE2, который рассматривает IP-адрес узла NODE1 как IP-адрес кластера и подключает группу кластера по умолчанию к сети.

1. Когда узел NODE2 пытается установить соединение с узлом NODE1, пакеты, направляемые на NODE1, не покидают узел NODE2, так как он разрешает IP-адрес узла NODE1 для самого себя. Узлу NODE2 не удается установить соединение с узлом NODE1, что приводит к потере кворума и завершению работы кластера.

1. Тем временем узел NODE1 может отправлять пакеты на узел NODE2, который не может отвечать на них. Узел NODE1 теряет кворум и завершает работу кластера.

Этого можно избежать путем назначения сетевому имени кластера неиспользуемого статического IP-адреса, например IP-адреса локальной связи (такого как 169.254.1.1), чтобы позволит подключить сетевое имя кластера к сети. Указания по упрощению этого процесса см. в статье [Настройка отказоустойчивого кластера Windows в Azure для групп доступности AlwaysOn](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Дополнительные сведения см. в статье [Настройка групп доступности AlwaysOn в Azure (графический пользовательский интерфейс)](virtual-machines-sql-server-alwayson-availability-groups-gui.md).

### Поддержка прослушивателя группы доступности

Прослушиватели группы доступности поддерживаются на виртуальных машинах Azure, работающих под управлением Windows Server 2008 R2, Windows Server 2012 и Windows Server 2012 R2. Это поддержка возможна благодаря использованию конечных точек балансировки нагрузки на виртуальных машинах Azure, представляющих собой узлы группы доступности. Необходимо выполнить специальные шаги по настройке, чтобы прослушиватели работали с клиентскими приложениями, запущенными в Azure и выполняемыми локально.

Существует два основных способа настройки: внешний (общедоступный) или внутренний прослушиватель. Внешний (общедоступный) прослушиватель связан с общедоступным виртуальным IP-адресом, доступным через Интернет. При использовании внешнего прослушивателя необходимо включить прямой ответ от сервера. Это означает, что необходимо подключиться к прослушивателю с компьютера, который не находится в той же облачной службе, что узлы группы доступности AlwaysOn. Другой вариант — внутренний прослушиватель, который использует внутренний балансировщик нагрузки (ILB). Внутренний прослушиватель поддерживает только клиентов, находящихся в той же виртуальной сети.

Если группа доступности охватывает несколько подсетей Azure (например, развернутую систему, включающую несколько регионов Azure), строка подключения клиента должна включать текст **MultisubnetFailover=True**. В результате будут выполняться попытки параллельного подключения к репликам в разных подсетях. Инструкции по настройке прослушивателя см. в разделах:

- [Настройка прослушивателя внутренней подсистемы балансировки нагрузки для группы доступности AlwaysOn в Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).
- [Настройка внешнего прослушивателя для групп доступности AlwaysOn в Azure](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md).

Вы по-прежнему можете подключаются к отдельным репликам доступности, напрямую подключаясь к экземпляру службы. Кроме того, так как группы доступности AlwaysOn обратно совместимы с клиентами зеркального отображения базы данных, вы можете подключаться к репликам доступности как к участникам зеркального отображения, пока реплики настроены аналогично зеркальному отображению базы данных:

- имеется по одной первичной и вторичной реплике;

- вторичная реплика настроена как недоступная для чтения (для параметра **Вторичная реплика для чтения** установлено значение **Нет**).

Ниже приведен пример строки подключения клиента, соответствующий этой конфигурации зеркального отображения базы данных с использованием ADO.NET или SQL Server Native Client.

	Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Дополнительные сведения о подключении клиента см. в следующих статьях.

- [Использование ключевых слов строки подключения с собственным клиентом SQL Server](https://msdn.microsoft.com/library/ms130822.aspx)
- [Подключение клиентов к сеансу зеркального отображения базы данных (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
- [Подключение к прослушивателю группы доступности в гибридной ИТ-среде](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
- [Прослушиватели групп доступности, возможность подключения клиентов и отработка отказа приложений (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
- [Использование строк подключения к зеркально отображаемой базе данных с группами доступности](https://technet.microsoft.com/library/hh213417.aspx)

### Задержки сети в гибридной ИТ-среде

Решение HADR следует развернуть, исходя из предположения, что между локальной сетью и Azure могут возникать значительные задержки сети. При развертывании реплик в Azure вместо синхронной следует использовать асинхронную фиксацию для режима синхронизации. Развертывая серверы зеркального отображения базы данных как локально, так и в Azure, используйте режим высокой производительности вместо режима высокой безопасности.

### Поддержка георепликации

Георепликация на дисках Azure не поддерживает хранение файлов данных и журнала из одной базы данных на отдельных дисках. GRS реплицирует изменения на каждом диске независимо и асинхронно. Этот механизм обеспечивает единый порядок записи на одном диске в геореплицированной копии, но не в геореплицированных копиях нескольких дисков. Если настроить базу данных на хранение файлов данных и журнала на отдельных дисках, восстановленные после сбоя диски могут содержать копию файла данных, созданную позже файла журнала, что нарушит упреждающее протоколирование в SQL Server и свойствах ACID транзакций. Если вы не можете отключить георепликацию в учетной записи хранения, все файлы данных и журнала базы данных следует хранить на одном диске. Если необходимо использовать несколько дисков из-за размера базы данных, для обеспечения избыточности данных необходимо развернуто одно из перечисленных выше решений для аварийного восстановления.

## Дальнейшие действия

Если требуется создать виртуальную машину Azure с SQL Server, см. статью [Подготовка виртуальной машины SQL Server в Azure](virtual-machines-provision-sql-server.md).

Чтобы обеспечить максимальную производительность SQL Server на виртуальной машине Azure, см. указания в статье [Рекомендации по оптимизации производительности SQL Server в виртуальных машинах Azure](virtual-machines-sql-server-performance-best-practices.md).

Другие темы, связанные с запуском SQL Server на виртуальных машинах Azure, рассматриваются в статье [SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-infrastructure-services.md).

### Другие ресурсы:

- [Установка нового леса Active Directory в виртуальной сети Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
- [Создание кластера WSFC для групп доступности AlwaysOn на виртуальной машине Azure](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

<!---HONumber=AcomDC_0128_2016-->