<properties pageTitle="Настройка принудительного тунеллирования для VPN-шлюзов с помощью PowerShell | Microsoft Azure" description="Если используется виртуальная сеть (классическая модель развертывания) с распределенным VPN-шлюзом, вы можете перенаправлять или "принудительно" отправлять весь интернет-трафик обратно в локальное расположение." services="vpn-gateway" documentationCenter="na" authors="cherylmc" manager="carmonm" editor="" tags="azure-service-management"/>
<tags  
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/24/2016"
   ms.author="cherylmc" />

# Настройка принудительного туннелирования для VPN-шлюзов с помощью классической модели развертывания

> [AZURE.SELECTOR]
- [PowerShell — управление службами](vpn-gateway-about-forced-tunneling.md)
- [PowerShell — Resource Manager](vpn-gateway-forced-tunneling-rm.md)

Принудительное туннелирование позволяет перенаправлять или "принудительно направлять" весь Интернет-трафик обратно в локальное расположение через VPN типа "сеть-сеть" для проверки и аудита. Это критически важное требование безопасности, имеющееся в большинстве корпоративных ИТ-политик. Без принудительного туннелирования Интернет-трафик из виртуальных машин в Azure будет всегда поступать из инфраструктуры сети Azure непосредственно в Интернет, без возможности его проверки или аудита. Неавторизованный доступ в Интернет может привести к раскрытию информации или другим нарушениям безопасности.


[AZURE.INCLUDE [vpn-gateway-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)]


**О моделях развертывания Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Требования и рекомендации

В Azure принудительное туннелирование настраивается с помощью пользовательских маршрутов виртуальной сети. Перенаправление трафика на локальный сайт выполняется с помощью маршрута по умолчанию к VPN-шлюзу Azure. В разделе ниже перечислены текущие ограничения таблицы маршрутизации и маршрутов для виртуальной сети Azure.


-  Каждая подсеть виртуальной сети имеет встроенные системные таблицы маршрутизации. Системная таблица маршрутизации имеет указанные ниже 3 группы маршрутов.

	- **Маршруты локальной виртуальной сети.** Они ведут непосредственно к виртуальным машинам назначения в той же виртуальной сети.
	
	- **Локальные маршруты.** Ведут к VPN-шлюзу Azure.
	
	- **Маршрут по умолчанию.** Ведет напрямую в Интернет. Обратите внимание, что пакеты, предназначенные для частных IP-адресов, не входящих в предыдущие два маршрута, будут потеряны.


-  Задав пользовательские маршруты, вы сможете создать таблицу маршрутизации для добавления маршрута по умолчанию, а затем сопоставить таблицу маршрутизации с подсетями вашей виртуальной сети для включения принудительного туннелирования в этих подсетях.

- Из числа локальных межорганизационных сайтов, подключенных к виртуальной сети, необходимо выбрать "сайт по умолчанию".

- Принудительное туннелирование должно быть сопоставлено с виртуальной сетью, в которой есть VPN-шлюз с динамической маршрутизацией (а не статический шлюз).
 
- С помощью этого механизма невозможно настроить принудительное туннелирование ExpressRoute. Такое туннелирование включается, когда предлагается маршрут по умолчанию с помощью сеансов пиринга BGP ExpressRoute. Дополнительные сведения см. в [документации по ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).



## Общие сведения о настройке

В примере ниже для интерфейсной подсети не применяется принудительное туннелирование. Рабочие нагрузки в интерфейсной подсети могут продолжать принимать запросы клиентов непосредственно из Интернета и отвечать на них. Для подсетей среднего уровня и внутренних подсетей применяется принудительное туннелирование.

Любые исходящие подключения из этих двух подсетей к Интернету будут принудительно перенаправлены обратно на локальный сайт через один из VPN-туннелей, работающих по протоколу S2S. Это позволяет ограничивать и проверять доступ в Интернет с виртуальных машин или облачных служб в Azure, при этом поддерживая необходимую многоуровневую архитектуру служб. Кроме того, вы можете применять принудительное туннелирование для всех виртуальных сетей, если в них нет рабочих нагрузок, требующих взаимодействия с Интернетом.


![Принудительное туннелирование](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)



## Перед началом работы

Перед началом настройки убедитесь, что у вас есть следующее.

- Подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрировать бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/).

- Настроенная виртуальная сеть.

- Последняя версия командлетов Azure PowerShell, использующая установщик веб-платформы. Последнюю версию для установки можно загрузить в разделе **Windows PowerShell** на странице [Загрузки](https://azure.microsoft.com/downloads/).

## Настройка принудительного туннелирования

Выполнив описанную ниже процедуру, вы сможете настроить принудительное туннелирование в виртуальной сети. В приведенном ниже примере файла конфигурации виртуальной сети (netcfg) описаны необходимые действия по настройке.



	<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
	</VirtualNetworkSite>

В данном примере у виртуальной сети MultiTier-VNet имеются 3 подсети (*Frontend*, *Midtier* и *Backend*) с 4 распределенными подключениями *DefaultSiteHQ* и 3 *филиалами*. Выполнив указанные ниже действия, можно настроить подключение *DefaultSiteHQ* в качестве подключения к сайту по умолчанию для принудительного туннелирования и настроить принудительное туннелирование для подсетей *Midtier* и *Backend*.


1. Создайте таблицу маршрутизации. Для этого воспользуйтесь указанным ниже командлетом.

		New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"

1. Добавьте маршрут по умолчанию в таблицу маршрутизации.

	Приведенный ниже командлет добавляет маршрут по умолчанию в таблицу маршрутизации, созданную в действии 1. Обратите внимание, что поддерживается только такой маршрут, когда указан префикс места назначения "0.0.0.0/0" для значения VPNGateway свойства nexthop.
 
		Set-AzureRoute –RouteTableName "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway

1. Сопоставьте таблицу маршрутизации с подсетями.

	После создания таблицы маршрутизации и добавления маршрута с помощью командлета ниже добавьте таблицу маршрутизации в подсеть виртуальной сети или сопоставьте таблицу с этой подсетью. В примерах ниже показано, как добавить таблицу маршрутизации MyRouteTable в подсети Midtier и Backend виртуальной сети MultiTier-VNet.

		Set-AzureSubnetRouteTable -VNetName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"

		Set-AzureSubnetRouteTable -VNetName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"

1. Назначьте сайт по умолчанию для принудительного туннелирования.

	В предыдущем действии с помощью сценариев с командлетами была создана таблица маршрутизации, которая затем была сопоставлена с двумя подсетями виртуальной сети. В последнем действии среди подключений к нескольким сайтам в виртуальной сети необходимо выбрать локальный сайт, который будет сайтом по умолчанию или туннелем.

		$DefaultSite = @("DefaultSiteHQ")
		Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"

## Дополнительные командлеты PowerShell

Ниже указаны некоторые дополнительные командлеты PowerShell, которые могут пригодиться при работе с конфигурациями принудительного туннелирования.

**Удаление таблицы маршрутизации:**

	Remove-AzureRouteTable -RouteTableName <routeTableName>

**Отображение таблицы маршрутизации:**

	Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]

**Удаление маршрута из таблицы маршрутизации:**

	Remove-AzureRouteTable –Name <routeTableName>

**Удаление маршрута из подсети:**

	Remove-AzureSubnetRouteTable –VNetName <virtualNetworkName> -SubnetName <subnetName>

**Отображение таблицы маршрутизации, сопоставленной с подсетью:**
	
	Get-AzureSubnetRouteTable -VNetName <virtualNetworkName> -SubnetName <subnetName>

**Удаление сайта по умолчанию из VPN-шлюза виртуальной сети:**

	Remove-AzureVnetGatewayDefaultSites -VNetName <virtualNetworkName>

<!---HONumber=AcomDC_0302_2016-->