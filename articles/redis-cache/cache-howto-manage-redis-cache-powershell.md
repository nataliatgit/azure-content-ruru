<properties
	pageTitle="Управление кэшем Redis для Azure с использованием Azure PowerShell | Microsoft Azure"
	description="Узнайте, как осуществлять администрирование кэша Redis для Azure с помощью Azure PowerShell."
	services="redis-cache"
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="sdanie"/>

# Управление кэшем Redis для Azure с использованием Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [Azure CLI](cache-manage-cli.md)

В этом разделе демонстрируется выполнение таких обычных задач, как создание, обновление и масштабирование экземпляров кэша Redis для Azure, повторное создание ключей доступа и просмотр сведений о кэшах. Полный список командлетов PowerShell для работы с кэшем Redis для Azure см. в разделе [Командлеты кэша Redis для Azure](https://msdn.microsoft.com/library/azure/mt634513.aspx).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](#classic) описаны далее в этой статье.

## Предварительные требования

Если вы уже установили Azure PowerShell, необходимо использовать Azure PowerShell 1.0.0 или более поздней версии. Установленную версию Azure PowerShell можно узнать в командной строке Azure PowerShell с помощью такой команды:

	Get-Module azure | format-table version

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Сначала необходимо войти в систему Azure с помощью следующей команды.

	Login-AzureRmAccount

Укажите адрес электронной почты и пароль своей учетной записи Azure в диалоговом окне входа в Microsoft Azure.

Далее, если у вас есть несколько подписок Azure, необходимо задать лишь одну из них. Чтобы просмотреть список текущих подписок, выполните следующую команду.

	Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Чтобы указать подписку, выполните указанную ниже команду. В приведенном ниже примере имя подписки — `ContosoSubscription`.

	Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Чтобы использовать Windows PowerShell с диспетчером ресурсов Azure, необходимо следующее:

- Windows PowerShell версии 3.0 или 4.0. Чтобы найти версию Windows PowerShell, введите `$PSVersionTable` и убедитесь, что для `PSVersion` указано значение 3.0 или 4.0. Чтобы установить совместимую версию, см. статью [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) или [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Чтобы получить подробную справку для любого командлета, встречающегося в этом учебнике, используйте командлет Get-Help.

	Get-Help <cmdlet-name> -Detailed

Например, чтобы получить справку для командлета `New-AzureRmRedisCache`, введите:

	Get-Help New-AzureRmRedisCache -Detailed

## Подключение к облаку Azure Government или Azure China

Среда Azure по умолчанию — это `AzureCloud`, представляющий экземпляр глобального облака Azure. Чтобы подключиться к другому экземпляру, используйте команду `Add-AzureRmAccount`, заменив параметр `-Environment` или -`EnvironmentName` на нужную вам среду или имя среды.

Чтобы просмотреть список доступных сред, выполните командлет `Get-AzureRmEnvironment`.

### Подключение к облаку Azure Government

Чтобы подключиться к облаку Azure Government, используйте одну из следующих команд.

	Add-AzureRMAccount -EnvironmentName AzureUSGovernment

или

	Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

Чтобы создать кэш в облаке Azure Government, используйте одно из следующих расположений.

-	Правительство штата Вирджиния
-	Правительство штата Айова

Дополнительные сведения об облаке Azure Government см. в статье [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) и в [Руководстве для разработчиков Microsoft Azure Government](azure-government-developer-guide.md).

### Подключение к облаку Azure China

Чтобы подключиться к облаку Azure China, используйте одну из следующих команд.

	Add-AzureRMAccount -EnvironmentName AzureChinaCloud

или

	Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

Чтобы создать кэш в облаке Azure China, используйте одно из следующих расположений.

-	Восток Китая
-	Север Китая

Дополнительные сведения об облаке Azure China см. в статье [AzureChinaCloud для Azure под управлением 21Vianet в Китае](http://www.windowsazure.cn/).

## Свойства, используемые в командлетах PowerShell кэша Redis для Azure

Приведенная ниже таблица содержит свойства и описания параметров, часто используемых при создании экземпляров кэша Redis для Azure с помощью Azure PowerShell и управления такими экземплярами.

| Параметр | Описание | значение по умолчанию |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| Имя | Имя кэша | |
| Расположение | Расположение кэша | |
| ResourceGroupName | Имя группы ресурсов, в которой необходимо создать кэш | |
| Размер | Размер кэша. Допустимые значения: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250 МБ, 1 ГБ, 2,5 ГБ, 6 ГБ, 13 ГБ, 26 ГБ, 53 ГБ | 1 ГБ |
| ShardCount | Число сегментов, которые будут созданы при создании кэша уровня Premium с включенной кластеризацией. Допустимые значения: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU | Определяет SKU кэша. Допустимые значения: Basic, Standard, Premium | Standard |
| RedisConfiguration | Определяет параметры конфигурации Redis для ключей maxmemory-delta, maxmemory-policy и notify-keyspace-events. Обратите внимание на то, что ключи maxmemory-delta и notify-keyspace-events доступны только для кэшей уровня Standard и Premium. | |
| EnableNonSslPort | Определяет, включен ли порт без SSL. | Ложь |
| MaxMemoryPolicy | Этот параметр устарел, вместо него используется параметр RedisConfiguration. | |
| StaticIP | При размещении кэша в виртуальной сети определяет уникальный IP-адрес подсети для кэша. Если IP-адрес не указан, он автоматически выбирается из подсети. | |
| Подсеть | При размещении кэша в виртуальной сети определяет имя подсети, в которой будет развернут кэш. | |
| Виртуальная сеть | При размещении кэша в виртуальной сети определяет идентификатор ресурса виртуальной сети, в которой будет развернут кэш. | |
| KeyType | Определяет, какой ключ доступа будет создаваться повторно при обновлении ключей доступа. Допустимые значения: Primary, Secondary | | | |


## Создание кэша Redis

Новые экземпляры кэша Redis для Azure создаются с помощью командлета [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx).

>[AZURE.IMPORTANT] Когда кэш Redis создается в подписке с использованием портала Azure впервые, портал регистрирует пространство имен `Microsoft.Cache` для этой подписки. Если первый кэш Redis создается в подписке с использованием PowerShell, необходимо зарегистрировать пространство имен с помощью представленной ниже команды, иначе команды `New-AzureRmRedisCache` и `Get-AzureRmRedisCache` завершатся неудачей.
>
>`Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`

Чтобы увидеть список доступных параметров свойства `New-AzureRmRedisCache` и их описания, выполните следующую команду:

	PS C:\> Get-Help New-AzureRmRedisCache -detailed
	
	NAME
	    New-AzureRmRedisCache
	
	SYNOPSIS
	    Creates a new redis cache.
	
	SYNTAX
	    New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
	    [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
	    <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
	    [<CommonParameters>]
	
	DESCRIPTION
	    The New-AzureRmRedisCache cmdlet creates a new redis cache.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache to create.
	
	    -ResourceGroupName <String>
	        Name of resource group in which to create the redis cache.
	
	    -Location <String>
	        Location in which to create the redis cache.
	
	    -RedisVersion <String>
	        RedisVersion is deprecated and will be removed in future release.
	
	    -Size <String>
	        Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
	        C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
	
	    -Sku <String>
	        Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
	
	    -MaxMemoryPolicy <String>
	        The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
	        MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
	
	    -RedisConfiguration <Hashtable>
	        All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
	        rdb-backup-frequency, maxmemory-delta, maxmemory-policy, notify-keyspace-events, maxmemory-samples,
	        slowlog-log-slower-than, slowlog-max-len, list-max-ziplist-entries, list-max-ziplist-value,
	        hash-max-ziplist-entries, hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries,
	        zset-max-ziplist-value etc.
	
	    -EnableNonSslPort <Boolean>
	        EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
	        non-SSL port will be disabled. Possible values are true and false.

	    -ShardCount <Integer>
	        The number of shards to create on a Premium Cluster Cache.
	
	    -VirtualNetwork <String>
	        The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format:
	        /subscriptions/{subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}
	
	    -Subnet <String>
	        Required when deploying a redis cache inside an existing Azure Virtual Network.
	
	    -StaticIP <String>
	        Required when deploying a redis cache inside an existing Azure Virtual Network.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Чтобы создать кэш с параметрами по умолчанию, выполните следующую команду:

	New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name` и `Location` — обязательные параметры; остальные указываются по желанию и имеют значения по умолчанию. При выполнении предыдущей команды создается экземпляр кэша Redis для Azure SKU Standard с заданным именем, расположением и группой ресурсов; размер экземпляра — 1 ГБ, порт без SSL отключен.

Для создания кэша уровня Premium укажите размер P1 (6-60 ГБ), P2 (13-130 ГБ), (26-260 ГБ), P3 и P4 (53-530 ГБ). Чтобы включить кластеризацию, укажите количество сегментов с помощью параметра `ShardCount`. В следующем примере создается кэш P1 уровня Premium с 3 сегментами. Размер кэша P1 уровня Premium — 6 ГБ, и, поскольку мы указали три сегмента, общий размер составляет 18 ГБ (3 x 6 ГБ).

	New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Чтобы указать значения для параметра `RedisConfiuration`, включите их в фигурные скобки (`{}`) как пары "ключ-значение" типа `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. В следующем примере создается кэш уровня Standard размером 1 ГБ с политикой максимальной памяти `allkeys-random` и уведомлениями пространства ключей, настроенными с помощью `KEA`. Дополнительные сведения см. в разделах [Уведомления пространства ключей (дополнительные параметры)](cache-configure.md#keyspace-notifications-advanced-settings) и [Политика максимальной памяти и зарезервированная максимальная память](cache-configure.md#maxmemory-policy-and-maxmemory-reserved).

	New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

## Обновление кэша Redis

Экземпляры кэша Redis для Azure обновляются с помощью командлета [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx).

Чтобы увидеть список доступных параметров свойства `Set-AzureRmRedisCache` и их описания, выполните следующую команду:

	PS C:\> Get-Help Set-AzureRmRedisCache -detailed
	
	NAME
	    Set-AzureRmRedisCache
	
	SYNOPSIS
	    Set redis cache updatable parameters.
	
	SYNTAX
	    Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
	    [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
	    <Integer>] [<CommonParameters>]
	
	DESCRIPTION
	    The Set-AzureRmRedisCache cmdlet sets redis cache parameters.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache to update.
	
	    -ResourceGroupName <String>
	        Name of the resource group for the cache.
	
	    -Size <String>
	        Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
	        C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
	
	    -Sku <String>
	        Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
	
	    -MaxMemoryPolicy <String>
	        The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
	        MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
	
	    -RedisConfiguration <Hashtable>
	        All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
	        rdb-backup-frequency, maxmemory-delta, maxmemory-policy, notify-keyspace-events, maxmemory-samples,
	        slowlog-log-slower-than, slowlog-max-len, list-max-ziplist-entries, list-max-ziplist-value,
	        hash-max-ziplist-entries, hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries,
	        zset-max-ziplist-value etc.
	
	    -EnableNonSslPort <Boolean>
	        EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
	        currently configured value. Possible values are true and false.
	
	    -ShardCount <Integer>
	        The number of shards to create on a Premium Cluster Cache.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

`Set-AzureRmRedisCache` можно использовать для обновления таких свойств, как значения `Size`, `Sku`, `EnableNonSslPort` и `RedisConfiguration`.

Следующая команда обновляет политику максимальной памяти кэша Redis с именем myCache.

	Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>
## Масштабирование кэша Redis

`Set-AzureRmRedisCache` можно использовать для масштабирования экземпляров кэша Redis для Azure при изменении свойств `Size`, `Sku` или `ShardCount`.

>[AZURE.NOTE]Масштабирование кэша с помощью PowerShell подчиняется тем же ограничениям и правилам, что и масштабирование кэша на портале Azure. Вы можете выполнить масштабирование до другой ценовой категории со следующими ограничениями.
>
>-	Вы не можете выполнить масштабирование кэша до уровня **Премиум** или с уровня "Премиум" до другого уровня.
>-	Вы не можете выполнить масштабирование кэша с уровня **Стандартный** до уровня **Базовый**.
>-	Вы можете выполнить масштабирование кэша с уровня **Basic** до уровня **Standard**, но вам не удастся одновременно с этим изменить размер кэша. Если требуется изменить размер, можно выполнить последующую операцию масштабирования до нужного размера.
>-	Вам не удастся выполнить масштабирование с большего размера до размера **C0 (250 МБ)**.
>
>Дополнительные сведения см. в статье [Масштабирование кэша Redis для Azure](cache-how-to-scale.md).

В следующем примере демонстрируется масштабирование кэша с именем `myCache` в кэш размером 2,5 ГБ. Обратите внимание на, что команда будет работать в кэше уровня Basic или Standard.

	Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

После запуска этой команды будет возвращено состояние кэша (аналогично вызову `Get-AzureRmRedisCache`). Обратите внимание на то, что параметр `ProvisioningState` имеет значение `Scaling`.

	PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB
	
	
	Name               : mycache
	Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
	                     crosoft.Cache/Redis/mycache
	Location           : South Central US
	Type               : Microsoft.Cache/Redis
	HostName           : mycache.redis.cache.windows.net
	Port               : 6379
	ProvisioningState  : Scaling
	SslPort            : 6380
	RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
	                     [maxmemory-delta, 150]...}
	EnableNonSslPort   : False
	RedisVersion       : 3.0
	Size               : 1GB
	Sku                : Standard
	ResourceGroupName  : mygroup
	PrimaryKey         : ....
	SecondaryKey       : ....
	VirtualNetwork     :
	Subnet             :
	StaticIP           :
	TenantSettings     : {}
	ShardCount         :

По завершении операции масштабирования значение параметра `ProvisioningState` изменится на `Succeeded`. Если масштабирование необходимо повторить, например изменить уровень кэша с Basic на Standard, а затем скорректировать его размер, нужно дождаться завершения предыдущей операции; в противном случае возникнет ошибка следующего вида:

	Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## Получение сведений о кэше Redis

Вы можете получить сведения об использовании кэша с помощью командлета [Get-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx).

Чтобы увидеть список и описание доступных параметров для свойства `Get-AzureRmRedisCache`, выполните следующую команду:

	PS C:\> Get-Help Get-AzureRmRedisCache -detailed
	
	NAME
	    Get-AzureRmRedisCache
	
	SYNOPSIS
	    Gets details about a single cache or all caches in the specified resource group or all caches in the current
	    subscription.
	
	SYNTAX
	    Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]
	
	DESCRIPTION
	    The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
	    ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
	    specific cache name provided.
	
	    If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.
	
	    If no parameters are given than it will return details about all caches the current subscription.
	
	PARAMETERS
	    -Name <String>
	        The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
	        returns the details for the cache.
	
	    -ResourceGroupName <String>
	        The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
	        then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
	        parameter is provided, then details for all caches in the resource group are returned.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Чтобы получить сведения обо всех кэшах в текущей подписке, выполните командлет `Get-AzureRmRedisCache` без параметров.

	Get-AzureRmRedisCache

Чтобы получить сведения обо всех кэшах в определенной группе ресурсов, выполните командлет `Get-AzureRmRedisCache` с параметром `ResourceGroupName`.

	Get-AzureRmRedisCache -ResourceGroupName myGroup

Чтобы получить сведения о конкретном кэше, выполните командлет `Get-AzureRmRedisCache` с параметром `Name`, содержащим имя кэша, и параметром `ResourceGroupName` с группой ресурсов, содержащей этот кэш.

	PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
	
	Name               : mycache
	Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
	                     crosoft.Cache/Redis/mycache
	Location           : South Central US
	Type               : Microsoft.Cache/Redis
	HostName           : mycache.redis.cache.windows.net
	Port               : 6379
	ProvisioningState  : Succeeded
	SslPort            : 6380
	RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
	                     [maxclients, 1000]...}
	EnableNonSslPort   : False
	RedisVersion       : 3.0
	Size               : 1GB
	Sku                : Standard
	ResourceGroupName  : myGroup
	VirtualNetwork     :
	Subnet             :
	StaticIP           :
	TenantSettings     : {}
	ShardCount         :

## Получение ключей доступа для кэша Redis

Для получения ключей доступа к кэшу вы можете использовать командлет [Get-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx).

Чтобы увидеть список и описание доступных параметров для свойства `Get-AzureRmRedisCacheKey`, выполните следующую команду:

	PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed
	
	NAME
	    Get-AzureRmRedisCacheKey
	
	SYNOPSIS
	    Gets the accesskeys for the specified redis cache.
	
	
	SYNTAX
	    Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]
	
	DESCRIPTION
	    The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache.
	
	    -ResourceGroupName <String>
	        Name of the resource group for the cache.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Чтобы получить ключи к кэшу, вызовите командлет `Get-AzureRmRedisCacheKey` и передайте имя кэша и имя группы ресурсов, содержащей этот кэш.

	PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup
	
	PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
	SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## Повторное создание ключей доступа для кэша Redis

Для повторного создания ключей доступа к кэшу вы можете использовать командлет [New-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634512.aspx).

Чтобы увидеть список и описание доступных параметров для свойства `New-AzureRmRedisCacheKey`, выполните следующую команду:

	PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed
	
	NAME
	    New-AzureRmRedisCacheKey
	
	SYNOPSIS
	    Regenerates the access key of a redis cache.
	
	SYNTAX
	    New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]
	
	DESCRIPTION
	    The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache.
	
	    -ResourceGroupName <String>
	        Name of the resource group for the cache.
	
	    -KeyType <String>
	        Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.
	
	    -Force
	        When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
	
Чтобы повторно создать первичный или вторичный ключ кэша, вызовите командлет `New-AzureRmRedisCacheKey`, передайте имя и группу ресурсов и укажите значение `Primary` или `Secondary` для параметра `KeyType`. В приведенном ниже примере создается вторичный ключ доступа для кэша.

	PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary
	
	Confirm
	Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
	
	
	PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
	SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## Удаление кэша Redis

Для удаления кэша Redis используйте командлет [Remove-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634515.aspx).

Чтобы увидеть список и описание доступных параметров для свойства `Remove-AzureRmRedisCache`, выполните следующую команду:

	PS C:\> Get-Help Remove-AzureRmRedisCache -detailed
	
	NAME
	    Remove-AzureRmRedisCache
	
	SYNOPSIS
	    Remove redis cache if exists.
	
	SYNTAX
	    Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>
	
	DESCRIPTION
	    The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache to remove.
	
	    -ResourceGroupName <String>
	        Name of the resource group of the cache to remove.
	
	    -Force
	        When the Force parameter is provided, the cache is removed without any confirmation prompts.
	
	    -PassThru
	        By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
	        is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

В приведенном ниже примере удаляется кэш с именем `myCache`.

	PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
	
	Confirm
	Are you sure you want to remove redis cache 'myCache'?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

<a name="classic"></a>
## Управление экземплярами кэша Redis для Azure с использованием классической модели развертывания PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](cache-howto-manage-redis-cache-powershell.md), описанной в начале этой статьи.

Следующий скрипт демонстрирует создание, обновление и удаление кэша Redis для Azure с использованием классической модели развертывания.
		
		$VerbosePreference = "Continue"

    	# Create a new cache with date string to make name unique.
		$cacheName = "MovieCache" + $(Get-Date -Format ('ddhhmm'))
		$location = "West US"
		$resourceGroupName = "Default-Web-WestUS"
		
		$movieCache = New-AzureRedisCache -Location $location -Name $cacheName  -ResourceGroupName $resourceGroupName -Size 250MB -Sku Basic
		
		# Wait until the Cache service is provisioned.
		
		for ($i = 0; $i -le 60; $i++)
		{
		    Start-Sleep -s 30
		    $cacheGet = Get-AzureRedisCache -ResourceGroupName $resourceGroupName -Name $cacheName
		    if ([string]::Compare("succeeded", $cacheGet[0].ProvisioningState, $True) -eq 0)
		    {
		        break
		    }
		    If($i -eq 60)
		    {
		        exit
		    }
		}
		
		# Update the access keys.
		
		Write-Verbose "PrimaryKey: $($movieCache.PrimaryKey)"
		New-AzureRedisCacheKey -KeyType "Primary" -Name $cacheName  -ResourceGroupName $resourceGroupName -Force
		$cacheKeys = Get-AzureRedisCacheKey -ResourceGroupName $resourceGroupName  -Name $cacheName
		Write-Verbose "PrimaryKey: $($cacheKeys.PrimaryKey)"
		
		# Use Set-AzureRedisCache to set Redis cache updatable parameters.
		# Set the memory policy to Least Recently Used.
		
		Set-AzureRedisCache -Name $cacheName -ResourceGroupName $resourceGroupName -RedisConfiguration @{"maxmemory-policy" = "AllKeys-LRU"}
		
		# Delete the cache.
		
		Remove-AzureRedisCache -Name $movieCache.Name -ResourceGroupName $movieCache.ResourceGroupName  -Force

## Дальнейшие действия

Дополнительные сведения об использовании Windows PowerShell с Azure см. в следующих ресурсах:

- [Документация по командлету кэша Redis для Azure на MSDN](https://msdn.microsoft.com/library/azure/mt634513.aspx)
- [Командлеты диспетчера ресурсов Azure](http://go.microsoft.com/fwlink/?LinkID=394765): узнайте, как использовать командлеты в модуле диспетчера ресурсов Azure.
- [Использование групп ресурсов для управления ресурсами Azure](../azure-portal/resource-group-portal.md): узнайте, как создавать группы ресурсов и управлять ими на портале Azure.
- [Блог Azure](http://blogs.msdn.com/windowsazure): узнайте о новых возможностях в Azure.
- [Блог Windows PowerShell](http://blogs.msdn.com/powershell): узнайте о новых возможностях в Windows PowerShell.
- [Блог "Hey, Scripting Блог](http://blogs.technet.com/b/heyscriptingguy/): реальные советы и рекомендации от сообщества Windows PowerShell.

<!---HONumber=AcomDC_0211_2016-->