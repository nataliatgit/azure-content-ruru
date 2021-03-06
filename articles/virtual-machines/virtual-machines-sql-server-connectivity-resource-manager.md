<properties 
	pageTitle="Подключение к виртуальной машине SQL Server в Azure (диспетчер ресурсов) | Microsoft Azure"
	description="В этом разделе используются ресурсы, созданные на основе классической модели развертывания, и описывается подключение к серверу SQL Server на виртуальной машине в Azure. Сценарии различаются в зависимости от конфигурации сети и расположения клиента."
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
	ms.date="12/18/2015"
	ms.author="jroth" />

# Подключение к виртуальной машине SQL Server в Azure (диспетчер ресурсов)

> [AZURE.SELECTOR]
- [Resource Manager](virtual-machines-sql-server-connectivity-resource-manager.md)
- [Classic](virtual-machines-sql-server-connectivity.md)

## Обзор

Процедура настройки подключения к системе SQL Server, выполняющейся на виртуальной машине Azure в диспетчере ресурсов, не сильно отличается от действий, которые нужно выполнить с локальным экземпляром SQL Server. Вам снова потребуется настроить брандмауэр, проверку подлинности и вход в базу данных.

Однако виртуальные машины Azure отличаются некоторые характерными особенностями подключения к SQL Server. В этой статье сначала рассматриваются некоторые [общие сценарии подключения](#connection-scenarios), а затем предоставляются [подробные инструкции по настройке подключения к SQL Server на виртуальной машине Azure](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

Эта статья посвящена подключению. Полное пошаговое руководство по подготовке и подключению см. в статье [Подготовка виртуальной машины SQL Server в Azure](virtual-machines-provision-sql-server.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.

## Сценарии подключения

Способ подключения клиента к системе SQL Server, выполняемой на виртуальной машине, зависит от расположения клиента, а также конфигурации компьютера и сети. Ниже приведены соответствующие сценарии.

- [Подключение к SQL Server через Интернет](#connect-to-sql-server-over-the-internet)
- [Подключение к SQL Server в пределах одной виртуальной сети](#connect-to-sql-server-in-the-same-virtual-network)

### Подключение к SQL Server через Интернет

Если вы хотите подключиться к СУБД SQL Server из Интернета, нужно выполнить некоторые необходимые действия, такие как настройка брандмауэра, включение проверки подлинности SQL и настройка группы безопасности вашей сети. У вас должно быть настроено правило группы безопасности сети, которое разрешает трафик TCP на порт 1433.

При использовании портала для подготовки образа виртуальной машины SQL Server с помощью диспетчера ресурсов следующие действия будут выполнены автоматически при выборе варианта подключения SQL **Общедоступное**:

![](./media/virtual-machines-sql-server-connectivity-resource-manager/sql-vm-portal-connectivity.png)

Если это не было сделано во время подготовки, можно настроить SQL Server и виртуальные машины вручную, выполнив [действия, описанные в этой статье, чтобы настроить подключение вручную](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

После этого любой клиент с доступом к Интернету может подключиться к экземпляру SQL Server, указав общедоступный IP-адрес виртуальной машины или имя DNS, назначенное этому IP-адресу. Если порт SQL Server — 1433, необходимо указать его в строке подключения.

	"Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Хотя таким образом можно обеспечить подключение клиентов через Интернет, это не значит, что любой пользователь сможет подключиться к вашей системе SQL Server. Внешние клиенты должны иметь правильные имя пользователя и пароль. Для обеспечения дополнительной безопасности можно использовать другой порт вместо хорошо известного 1433. Например, если настроить SQL Server на прослушивание порта 1500 и установить соответствующие правила брандмауэра группы безопасности сети, то для подключения нужно будет добавить номер порта к имени сервера, как показано в следующем примере:

	"Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

>[AZURE.NOTE] Важно отметить, что при использовании этого метода для соединения с SQL Server все возвращаемые данные считаются трафиком, исходящим из центра обработки данных. Может взиматься обычная [плата за передачу исходящих данных](https://azure.microsoft.com/pricing/details/data-transfers/). Это справедливо, даже если вы передаете данные с другого компьютера или облачной службы в одном центре обработки данных Azure, так как трафик по-прежнему проходит через общедоступный балансировщик нагрузки в Azure.

### Подключение к SQL Server в пределах одной виртуальной сети

[Виртуальная сеть](..\virtual-network\virtual-networks-overview.md) поддерживает дополнительные сценарии. Вы можете подключить виртуальные машины в одной виртуальной сети, даже если они расположены в разных группах ресурсов. Если используется [VPN типа «сеть-сеть»](../vpn-gateway/vpn-gateway-site-to-site-create.md), можно создать гибридную архитектуру, обеспечивающую подключение виртуальных машин к локальным сетям и компьютерам.

Виртуальные сети также позволяют присоединить ваши виртуальные машины Azure к домену. Это единственный способ использовать проверку подлинности Windows в SQL Server. Другие сценарии подключения требуют проверки подлинности SQL с использованием имен пользователей и паролей.

При использовании портала для подготовки образа виртуальной машины SQL Server с диспетчером ресурсов надлежащие правила брандмауэра для обмена данными в виртуальной сети будут настроены при выборе варианта подключения SQL **Частное**. Если это не было сделано во время подготовки, можно настроить SQL Server и виртуальные машины вручную, выполнив [действия, описанные в этой статье, чтобы настроить подключение вручную](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm). Однако если вы собираетесь настроить доменную среду и проверку подлинности Windows, не нужно выполнять описанные в этой статье действия по настройке проверки подлинности SQL и входа в систему. Также не нужно настраивать правила групп безопасности сети для доступа через Интернет.

Если вы настроили DNS в своей виртуальной сети, то сможете подключиться к экземпляру SQL Server, указав имя узла виртуальной машины SQL Server в строке подключения. В следующем примере также предполагается, что настроена проверка подлинности Windows, а пользователю предоставили доступ к экземпляру SQL Server.

	"Server=mysqlvm;Integrated Security=true" 

Обратите внимание, что в этом сценарии также можно указать IP-адрес виртуальной машины.

## Действия по ручной настройке подключения к SQL Server на виртуальной машине Azure

Ниже показано, как вручную настроить подключение к экземпляру SQL Server и затем при необходимости подключиться через Интернет с помощью SQL Server Management Studio (SSMS). Обратите внимание, что многие из этих действий выполняются автоматически при выборе соответствующего варианта подключения к SQL Server на портале.

Чтобы подключиться к экземпляру SQL Server из другой виртуальной машины или через Интернет, необходимо выполнить задачи, описанные в следующих разделах:

- [Открытие портов TCP в брандмауэре Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Настройка SQL Server для прослушивания через протокол TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Настройка SQL Server для аутентификации в смешанном режиме](#configure-sql-server-for-mixed-mode-authentication)
- [Создание имен входа для аутентификации SQL Server](#create-sql-server-authentication-logins)
- [Настройка имени DNS для общедоступного IP-адреса](#configure-a-dns-label-for-the-public-ip-address)
- [Подключение к ядру СУБД с другого компьютера](#connect-to-the-database-engine-from-another-computer)

[AZURE.INCLUDE [Подключение к SQL Server на виртуальной машине](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Подключение к SQL Server на виртуальной машине в диспетчере ресурсов](../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[AZURE.INCLUDE [Подключение к SQL Server на виртуальной машине в диспетчере ресурсов](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## Дальнейшие действия

Инструкции по подготовке и действия по подключению приведены в статье [Подготовка виртуальной машины SQL Server в Azure](virtual-machines-provision-sql-server.md).

Важно просмотреть все рекомендации по обеспечению безопасности для системы SQL Server, выполняемой на виртуальной машине Azure. Дополнительные сведения см. в статье [Вопросы безопасности SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-security-considerations.md).

Другие темы, связанные с запуском SQL Server на виртуальных машинах Azure, рассматриваются в статье [SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_0128_2016-->