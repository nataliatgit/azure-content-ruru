<properties 
   pageTitle="Подключение нескольких локальных сайтов к виртуальной сети с использованием VPN-шлюза"
   description="Данная статья поможет настроить подключение нескольких локальных сайтов к виртуальной сети с использованием VPN-шлюза для классической модели развертывания."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="cherylmc" />

# Подключение нескольких локальных сайтов к виртуальной сети

Данная статья относится к подключению нескольких локальных сайтов к виртуальной сети, созданной с использованием классической модели развертывания (также называется управлением службами).

**О моделях развертывания Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]Как только мы подготовим статью с инструкциями для виртуальных сетей, созданных с использованием модели диспетчера ресурсов, я добавлю соответствующую ссылку на эту страницу.

## О подключении

Вы можете подключить несколько локальных сайтов к одной виртуальной сети. Это особенно привлекательно для создания гибридных облачных решений. Создание многосайтового подключения к шлюзу виртуальной сети Azure очень похоже на создание других подключений типа "сеть — сеть". На самом деле вы можете использовать имеющийся VPN-шлюз Azure, если это шлюз с динамической маршрутизацией (на основе маршрутов).

Если к вашей виртуальной сети уже подключен статический шлюз, вы можете изменить его тип на динамический, не перестраивая виртуальную сеть, чтобы обеспечить многосайтовое подключение. Прежде чем менять тип маршрутизации, убедитесь, что локальный VPN-шлюз поддерживает конфигурации VPN на основе маршрутов.

![Многосайтовая VPN](./media/vpn-gateway-multi-site/IC727363.png)

## Что необходимо учесть

**Для внесения изменений в эту виртуальную сеть нельзя использовать классический портал Azure.** Для этого выпуска изменения необходимо будет внести в файл конфигурации сети, а не использовать для этого классический портал Azure. Если внести изменения на классической версии портала, они заменят параметры ссылок на несколько сайтов для этой виртуальной сети. К завершению процедуры многосайтового подключения вы должны будете довольно уверенно использовать файл конфигурации сети. Однако при наличии нескольких людей, работающих над конфигурацией сети, необходимо убедиться, что все знают об этом ограничении. Это не значит, что использовать классический портал Azure вообще нельзя. Вы можете использовать его для всех действий, кроме изменения конфигурации этой конкретной виртуальной сети.

## Перед началом работы

Перед началом настройки убедитесь, что выполнены следующие предварительные требования:

- Подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрироваться в бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/).

- Совместимое оборудование VPN для каждого местного расположения. Ознакомьтесь со статьей [VPN-устройства для создания виртуальных сетей](http://go.microsoft.com/fwlink/p/?linkid=615099), чтобы проверить, является ли совместимым устройство, которое вы хотите использовать.

- Внешний общедоступный IPv4-адрес для каждого VPN-устройства. Этот IP-адрес не может располагаться вне преобразования сетевых адресов (NAT). Это обязательное требование.

- Последняя версия командлетов Azure PowerShell. Скачать и установить последнюю версию вы можете в разделе Windows PowerShell на [странице скачивания](https://azure.microsoft.com/downloads/).

- Пользователь, имеющий опыт в настройке вашего оборудования VPN. Вы не сможете использовать для настройки VPN-устройств сценарии VPN, автоматически созданные на классическом портале Azure. Это означает, что вам нужно будет хорошо понимать, как настраивать VPN-устройство, или воспользоваться помощью соответствующего специалиста.

- Диапазоны IP-адресов, которые вы хотите использовать для виртуальной сети (если она еще не создана).

- Диапазоны IP-адресов для каждого сайта локальной сети, к которым вы будете подключаться. Понадобится убедиться, что диапазоны IP-адресов сайтов локальной сети, к которым требуется подключиться, не перекрываются. В противном случае классический портал Azure или REST API отклонит передаваемую конфигурацию. Например, если два сайта локальной сети содержат диапазон IP-адресов 10.2.3.0/24 и имеется пакет с адресом назначения 10.2.3.3, Azure не сможет определить сайт, на который требуется отправить пакет, так как диапазоны адресов перекрываются. Для предотвращения проблем с маршрутизацией Azure не позволит передать файл конфигурации с перекрывающимися диапазонами адресов.

## Создание виртуальной сети и шлюза

1. **Создайте VPN типа "сеть — сеть" со шлюзом с динамической маршрутизацией (маршрутизацией на основе маршрутов).** Если это уже сделано, отлично! Можно перейти к [экспорту параметров конфигурации виртуальной сети](#export). Если нет, выполните следующее:

	**Если у вас уже есть виртуальная сеть типа "сеть — сеть", но со шлюзом со статической маршрутизацией:** Шаг 1. Измените тип шлюза на шлюз с динамической маршрутизацией. Для многосайтовой VPN требуется шлюз с динамической маршрутизацией. Чтобы изменить тип шлюза, необходимо сначала удалить существующий шлюз, а затем создать новый. Указания см. в разделе [Изменение типа маршрутизации VPN-шлюза](vpn-gateway-configure-vpn-gateway-mp.md/#how-to-change-your-vpn-gateway-type). Шаг 2. Настройте новый шлюз и создайте VPN-туннель. Инструкции см. в разделе [Настройка VPN-шлюза на классическом портале Azurе](vpn-gateway-configure-vpn-gateway-mp.md). Для начала измените тип шлюза на шлюз с динамической маршрутизацией.

	**Если у вас нет виртуальной сети типа "сеть — сеть":** Шаг 1. Создайте виртуальную сеть типа "сеть — сеть" с помощью следующей процедуры: [Создание виртуальной сети с VPN-подключением типа "сеть — сеть" на классическом портале Azure](vpn-gateway-site-to-site-create.md). Шаг 2. Настройте шлюз с динамической маршрутизацией с помощью следующей процедуры: [Настройка VPN-шлюза](vpn-gateway-configure-vpn-gateway-mp.md). Не забудьте выбрать **динамическую маршрутизацию** в качестве типа шлюза.

2. **<a name="export"></a>Экспортируйте параметры конфигурации виртуальной сети.** Чтобы экспортировать файл конфигурации сети, см. раздел [Экспорт параметров сети](../virtual-network/virtual-networks-using-network-configuration-file.md). Экспортируемый файл будет использоваться для настройки новых параметров нескольких сайтов.

3. **Откройте файл конфигурации сети.** Откройте файл конфигурации сети, скачанный на последнем шаге. Используйте любой редактор XML. Файл должен выглядеть следующим образом:

		<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
		  <VirtualNetworkConfiguration>
		    <LocalNetworkSites>
		      <LocalNetworkSite name="Site1">
		        <AddressSpace>
		          <AddressPrefix>10.0.0.0/16</AddressPrefix>
		          <AddressPrefix>10.1.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
		      </LocalNetworkSite>
		      <LocalNetworkSite name="Site2">
		        <AddressSpace>
		          <AddressPrefix>10.2.0.0/16</AddressPrefix>
		          <AddressPrefix>10.3.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
		      </LocalNetworkSite>
		    </LocalNetworkSites>
		    <VirtualNetworkSites>
		      <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
		        <AddressSpace>
		          <AddressPrefix>10.20.0.0/16</AddressPrefix>
		          <AddressPrefix>10.21.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="FE">
		            <AddressPrefix>10.20.0.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="BE">
		            <AddressPrefix>10.20.1.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.20.2.0/29</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="Site1">
		              <Connection type="IPsec" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>
		    </VirtualNetworkSites>
		  </VirtualNetworkConfiguration>
		</NetworkConfiguration>

4. **Добавьте в него ссылки на несколько сайтов.** При добавлении или удалении данных ссылок на сайты будут внесены изменения в элемент ConnectionsToLocalNetwork/LocalNetworkSiteRef. При добавлении новой ссылки на локальный сайт Azure инициирует создание нового туннеля. В следующем примере приведена конфигурации сети для подключения одного сайта.

		<Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

	Чтобы добавить дополнительные ссылки на сайты (создать многосайтовую конфигурацию), просто добавьте дополнительные строки LocalNetworkSiteRef, как показано в следующем примере:

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

5. **Сохраните файл конфигурации сети и импортируйте его.** Чтобы импортировать файл конфигурации сети, см. раздел [Импорт параметров сети](../virtual-network/../virtual-network/virtual-networks-using-network-configuration-file.md#export-and-import-virtual-network-settings-using-the-management-portal). После импорта этого файла с изменениями будут добавлены новые туннели. Туннели будут использовать динамический шлюз, который был создан ранее.

6. **Скачайте общие ключи для VPN-туннелей.** После добавления новых туннелей используйте командлет PowerShell Get-AzureVNetGatewayKey для получения общих ключей IPsec (IKE) для каждого туннеля.

	Например:

		Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

		Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

	Кроме того, при желании вы можете использовать REST API *получения общего ключа шлюза виртуальной сети* для получения общих ключей.

## Проверка подключений

**Проверьте состояние многосайтового туннеля.** После скачивания ключей для каждого туннеля необходимо проверить подключения. Используйте *Get-AzureVnetConnection*, чтобы получить список туннелей виртуальной сети, как показано в следующем примере. VNet1 — имя виртуальной сети.

		Get-AzureVnetConnection -VNetName VNET1
		
		ConnectivityState         : Connected
		EgressBytesTransferred    : 661530
		IngressBytesTransferred   : 519207
		LastConnectionEstablished : 5/2/2014 2:51:40 PM
		LastEventID               : 23401
		LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
		LastEventTimeStamp        : 5/2/2014 2:51:40 PM
		LocalNetworkSiteName      : Site1
		OperationDescription      : Get-AzureVNetConnection
		OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
		OperationStatus           : Succeeded
		
		ConnectivityState         : Connected
		EgressBytesTransferred    : 789398
		IngressBytesTransferred   : 143908
		LastConnectionEstablished : 5/2/2014 3:20:40 PM
		LastEventID               : 23401
		LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
		LastEventTimeStamp        : 5/2/2014 2:51:40 PM
		LocalNetworkSiteName      : Site2
		OperationDescription      : Get-AzureVNetConnection
		OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
		OperationStatus           : Succeeded

## Дальнейшие действия

Чтобы больше узнать о VPN-шлюзах, см. статью [Шлюзы VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

<!---HONumber=AcomDC_0128_2016-->