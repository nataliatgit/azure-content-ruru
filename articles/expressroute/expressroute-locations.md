<properties
   pageTitle="Расположения ExpressRoute | Microsoft Azure"
   description="В этой статье приведена подробная информация о расположениях, где предлагаются услуги, и способах подключения к регионам Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/15/2016"
   ms.author="cherylmc" />

# Партнеры и одноранговые расположения ExpressRoute

В данной статье приведены таблицы со сведениями о поставщиках услуг подключения ExpressRoute, географическом покрытии ExpressRoute, облачных службах Майкрософт, поддерживаемых через ExpressRoute, и системных интеграторах ExpressRoute.

## Поставщики услуг подключения ExpressRoute

ExpressRoute поддерживается во всех регионах и расположениях Azure. На следующей карте обозначены регионы Azure и расположения ExpressRoute. Расположения ExpressRoute соответствуют тем территориям, где Майкрософт взаимодействует с несколькими одноранговыми поставщиками услуг.

![](./media/expressroute-locations/expressroute-locations-map.png)

Вы сможете получить доступ ко всем службам Azure во всех регионах соответствующего геополитического региона, если вы подключены по меньшей мере к одному расположению ExpressRoute в этом регионе. В следующей таблице сопоставлены регионы Azure с расположениями ExpressRoute в пределах геополитических регионов.

|**Геополитический регион**|**Регионы Azure**|**Расположения ExpressRoute**|
|---|---|---|
|**Северная Америка**|Восточная часть США, западная часть США, восточная часть США 2, центральная часть США, юго-центральная часть США, северо-центральная часть США, центральная часть Канады, восточная часть Канады|Атланта, Чикаго, Даллас, Лос-Анджелес, Нью-Йорк, Сиэтл, Кремниевая долина, Вашингтон (округ Колумбия), Монреаль+, Торонто+|
|**Северная Америка**|Южная Бразилия|Сан-Паулу|
|**Европа**|Северная Европа, Западная Европа|Амстердам, Дублин, Лондон|
|**Азия**|Восточная Азия, Юго-Восточная Азия|Гонконг, Сингапур|
|**Япония**|Западная Япония, Восточная Япония|Осака, Токио|
|**Австралия**|Восточная Австралия, Юго-Восточная Австралия|Мельбурн, Сидней|
|**Индия**|Западная Индия, Центральная Индия, Южная Индия|Ченнаи, Мумбаи|



В таблице ниже содержатся сведения о регионах и геополитических границах для национальных облаков.

|**Геополитический регион**|**Регионы Azure**|**Расположения ExpressRoute**|
|---|---|---|---|
|**Облако правительства США**|Правительство штата Айова, США, Правительство штата Вирджиния, США|Чикаго, Нью-Йорк+, Вашингтон (округ Колумбия)|
|**Облако Китая**|Северный Китай, Восточный Китай|Пекин, Шанхай|


В стандартном номере SKU ExpressRoute подключение между геополитическими регионами не поддерживается. Для поддержки глобальных подключений необходимо включить надстройку ExpressRoute класса "Премиум". Подключение к национальным облачным средам не поддерживается. При необходимости вы можете работать с поставщиками услуг подключения.


## Расположения поставщиков услуг подключения

### Рабочая среда Azure

| **Поставщик услуг** |**Microsoft Azure** | **Office 365 и CRM Online** | **Расположения** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka Networks](http://www.aryaka.com/)** | Поддерживаются | Поддерживаются | Амстердам, Кремниевая долина, Сингапур, Вашингтон, округ Колумбия |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Поддерживаются | Поддерживаются | Амстердам, Даллас, Лондон, Кремниевая долина, Сингапур, Вашингтон (округ Колумбия) |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Поддерживаются | Поддерживаются | Амстердам, Гонконг, Лондон, Кремниевая долина, Сингапур, Токио, Вашингтон (округ Колумбия) |
|**China Telecom Global** | Поддерживаются | Не поддерживается | Гонконг |
|**Cologix** | Скоро | Скоро | Монреаль+, Торонто+ |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Поддерживаются | Поддерживаются | Амстердам, Дублин, Лондон |
| **Comcast** | Поддерживаются | Не поддерживается | Кремниевая долина, Вашингтон, округ Колумбия |
| **CoreSite** | Поддерживаются | Поддерживаются | Лос-Анджелес | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Поддерживаются | Поддерживаются | Амстердам, Атланта, Чикаго, Даллас, Гонконг, Лондон, Лос-Анджелес, Мельбурн, Нью-Йорк, Осака, Сан-Пауло, Сиэтл, Кремниевая долина, Сингапур, Сидней, Токио, Вашингтон (округ Колумбия), Торонто+ |
| **[Internet Initiative Japan Inc. (IIJ)](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** | Поддерживаются | Поддерживаются | Осака, Токио |
| **[InterCloud](https://www.intercloud.com/)** | Поддерживаются | Поддерживаются | Амстердам, Лондон, Сингапур, Вашингтон, округ Колумбия |
| **Internet Solutions - Cloud Connect** | Поддерживаются | Поддерживаются | Амстердам, Лондон |
| **Interxion** | Поддерживаются | Поддерживаются | Амстердам, Лондон |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Поддерживаются | Поддерживаются | Амстердам, Чикаго, Даллас, Лондон, Сиэтл, Кремниевая долина, Вашингтон, округ Колумбия |
| **Megaport** | Поддерживаются | Поддерживаются | Даллас, Лос-Анджелес, Мельбурн, Нью-Йорк, Сиэтл, Сидней, Вашингтон (округ Колумбия) |
| **MTN** | Поддерживаются | Поддерживаются | Лондон |
| **NEXTDC** | Поддерживаются | Поддерживаются | Мельбурн, Сидней |
| **NTT Communications** | Поддерживаются | Поддерживаются | Лондон, Токио |
| **[Orange](http://www.orange-business.com/)** | Поддерживаются | Поддерживаются | Амстердам, Гонконг, Лондон, Кремниевая долина, Сингапур, Вашингтон, округ Колумбия |
| **PCCW Global Limited** | Поддерживаются | Поддерживаются | Гонконг |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** | Поддерживаются | Не поддерживается | Сингапур |
| **Softbank** | Скоро | Скоро | Осака, Токио | 
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Поддерживаются | Поддерживаются | Амстердам, Ченнаи, Гонконг, Лондон, Мумбаи, Сингапур, Вашингтон (округ Колумбия) |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Поддерживаются | Поддерживаются | Амстердам, Лондон |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Поддерживаются | Не поддерживается | Мельбурн, Сидней |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | Поддерживаются | Поддерживаются | Амстердам, Гонконг, Лондон, Кремниевая долина, Сидней, Токио, Вашингтон (округ Колумбия) |
| **Vodafone** | Поддерживаются | Не поддерживается | Лондон | 
| **[Zayo Group](http://www.zayo.com/solutions/industries/connect-to-cloud-data-centers/cloud-connectivity/microsoft-expressroute/)** | Поддерживаются | Поддерживаются | Чикаго, Лос-Анджелес, Нью-Йорк, Кремниевая долина, Вашингтон (округ Колумбия) |

 Знак **+** означает "скоро"

### Национальные облачные среды

#### Облако правительства США

| **Поставщик услуг** |**Microsoft Azure** | **Office 365** | **Расположения** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Поддерживаются | Не поддерживается | Чикаго, Вашингтон (округ Колумбия) |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Поддерживаются | Не поддерживается | Чикаго, Вашингтон (округ Колумбия) |
| **[Level 3 Communications (IPVPN)](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Поддерживаются | Не поддерживается | Чикаго, Вашингтон (округ Колумбия) |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Поддерживаются | Не поддерживается | Чикаго, Вашингтон (округ Колумбия) |

#### Облако Китая

| **Поставщик услуг** |**Microsoft Azure** | **Office 365** | **Расположения** |
|-----------------------|--------------------|----------------|---------------|
| **China Telecom** | Поддерживаются | Не поддерживается | Пекин, Шанхай|
Дополнительные сведения см. на странице [ExpressRoute в Китае](http://www.windowsazure.cn/home/features/expressroute/).

## Подключение через других поставщиков услуг

Вы можете создать подключение, даже если ваш поставщик услуг подключения не указан в предыдущих разделах.

- Узнайте у своего поставщика услуг подключения, подключен ли он к какому-либо Exchange, указанному в таблице выше. Дополнительные сведения об услугах, предлагаемых поставщиками Exchange, см. по ссылкам ниже. Несколько поставщиков услуг подключения уже подключены к серверам Ethernet Exchange.

	- [Equnix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
	- [InterXion](http://www.interxion.com/)
	- [NextDC](http://www.nextdc.com/)
	- [CoreSite](http://www.coresite.com/)
- Обратитесь к своему поставщику услуг подключения, чтобы он расширил вашу сеть, добавив необходимое пиринговое расположение.
	- Убедитесь, что поставщик услуг подключения расширяет границы вашего подключения, сохраняя высокую доступность во избежание влияния единых точек отказа.
- Закажите канал ExpressRoute с Exchange у вашего поставщика услуг подключения для связи с Майкрософт.
	- Для настройки подключения выполните действия, описанные в статье [Создание и изменение канала ExpressRoute с помощью PowerShell](expressroute-howto-circuit-classic.md).

|**Поставщик услуг подключения**|**Exchange**|**Одноранговые расположения**|
|---|---|---|
|**[XO Communications](http://www.xo.com/)**|Equinix|Кремниевая долина|

## Системные интеграторы ExpressRoute

Возможность частного подключения, соответствующего вашим потребностям, будет зависеть от масштаба сети. Чтобы упростить переход на ExpressRoute, вы можете обратиться к одному из системных интеграторов, указанных в таблице ниже.

|**Системный интегратор**|**Континент**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|США||
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**|EMEA|
|**[Project Leadership](http://www.projectleadership.net/azure)** | США |
|**[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | США |

## Дальнейшие действия

- Дополнительную информацию об ExpressRoute см. в статье [Часто задаваемые вопросы об ExpressRoute](expressroute-faqs.md).
- Убедитесь, что выполнены все необходимые условия. См. статью [Предварительные требования для ExpressRoute](expressroute-prerequisites.md).

<!---HONumber=AcomDC_0302_2016-->