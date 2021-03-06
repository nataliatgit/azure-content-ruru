<properties
   pageTitle="Требования к маршрутизации для ExpressRoute | Microsoft Azure"
   description="На этой странице подробно описаны требования по настройке маршрутизации для каналов ExpressRoute и управлению этой маршрутизацией."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/03/2016"
   ms.author="cherylmc"/>


# Требования ExpressRoute к маршрутизации  

Для подключения к облачным службам Майкрософт с помощью ExpressRoute необходима настройка и управление маршрутизацией. Некоторые поставщики услуг подключения предлагают настройку маршрутизации как управляемой службы и управление этой службой. Узнайте у поставщика услуг подключения, предоставляет ли он такую услугу. В противном случае необходимо выполнить требования, описанные ниже.

Описание сеансов маршрутизации, которые необходимо настроить для установки подключения, см. в статье [Каналы и домены маршрутизации](expressroute-circuit-peerings.md).

**Примечание.** Майкрософт не поддерживает применение протоколов избыточности маршрутизаторов (например, HSRP, VRRP) в конфигурациях высокой доступности. Для обеспечения высокой доступности мы используем избыточную пару сеансов BGP на каждый пиринг.

## IP-адреса для пирингов

Для настройки маршрутизации между сетью и концевыми маршрутизаторами Microsoft Enterprise (MSEE) необходимо зарезервировать несколько блоков IP-адресов. В этом разделе приводится список требований и описание правил получения и использования этих IP-адресов.

### IP-адреса для частного пиринга Azure

Для настройки пиринга можно использовать либо частные, либо общедоступные IP-адреса. Диапазон адресов, используемых для настройки маршрутов, не должен пересекаться с диапазонами адресов, используемых для создания виртуальных сетей в Azure.

 - Для маршрутизации интерфейсов необходимо зарезервировать подсеть /29 или две подсети /30.
 - В качестве используемых подсетей можно использовать либо частные, либо общедоступные IP-адреса.
 - Подсети не должны конфликтовать с диапазоном, зарезервированным клиентом для использования в облаке Майкрософт.
 - Если используется подсеть /29 , ее можно разбить на две подсети /30. 
	 - Первая подсеть /30 будет использоваться в качестве основной ссылки, а вторая — в качестве дополнительной.
	 - Для каждой из подсетей /30 необходимо передать в маршрутизатор первый IP-адрес подсети /30. Для настройки сеанса BGP Майкрософт будет использовать второй IP-адрес подсети /30.
	 - [Соглашение об уровне обслуживания по доступности](https://azure.microsoft.com/support/legal/sla/) будет действовать, если настроены оба сеанса BGP.  

#### Пример для частного пиринга

Если вы решите использовать для настройки пиринга подсеть a.b.c.d/29, она будет разделена на две подсети /30. В приведенном ниже примере мы рассмотрим, как используется подсеть a.b.c.d/29.

Подсеть a.b.c.d/29 будет разбита на подсети a.b.c.d/30 и a.b.c.d+4/30 и передана в Майкрософт путем подготовки API. Подсеть a.b.c.d+1 будет использоваться как IP-адрес VRF для основного PE, а Майкрософт будет использовать подсеть a.b.c.d+2 как IP-адрес VRF для основного MSEE. Подсеть a.b.c.d+5 будет использоваться как IP-адрес VRF для дополнительного PE, а Майкрософт будет использовать подсеть a.b.c.d+6 как IP-адрес VRF для дополнительного MSEE.

Допустим, вы выбрали подсеть 192.168.100.128/29 для настройки частного пиринга. Подсеть 192.168.100.128/29 включает адреса с 192.168.100.128 по 192.168.100.135, причем:

- Подсеть 192.168.100.128/30 назначается связи link1, поставщик услуг подключения использует адрес 192.168.100.129, а Майкрософт — адрес 192.168.100.130.
- Подсеть 192.168.100.132/30 назначается связи link2, поставщик услуг подключения использует адрес 192.168.100.133, а Майкрософт — адрес 192.168.100.134.

### IP-адреса для общедоступного пиринга Azure и пиринга Microsoft

Для настройки сеансов BGP используйте принадлежащие вам общедоступные IP-адреса. У Майкрософт должна быть возможность проверить владельца IP-адреса по региональному интернет-реестру или интернет-реестру маршрутизации.

- При настройке сеансов BGP для каждого пиринга в канале ExpressRoute (если их несколько) используйте уникальную подсеть /29 или две подсети /30. 
- Если используется подсеть /29 , ее можно разбить на две подсети /30. 
	- Первая подсеть /30 будет использоваться в качестве основной ссылки, а вторая — в качестве дополнительной.
	- Для каждой из подсетей /30 необходимо передать в маршрутизатор первый IP-адрес подсети /30. Для настройки сеанса BGP Майкрософт будет использовать второй IP-адрес подсети /30.
	- [Соглашение об уровне обслуживания по доступности](https://azure.microsoft.com/support/legal/sla/) будет действовать, если настроены оба сеанса BGP.

Убедитесь, что ваш IP-адрес и число AS зарегистрированы на ваше имя в одном из следующих реестров:

- [ARIN](https://www.arin.net/)
- [APNIC](https://www.apnic.net/)
- [AFRINIC](https://www.afrinic.net/)
- [LACNIC](http://www.lacnic.net/)
- [RIPENCC](https://www.ripe.net/)
- [RADB](http://www.radb.net/)
- [ALTDB](http://altdb.net/)


## Динамический обмен маршрутами

Обмен маршрутами осуществляется по протоколу eBGP. Сеансы EBGP устанавливаются между MSEEs и вашими маршрутизаторами. Проверка подлинности сеансов BGP не требуется. При необходимости можно настроить MD5-хэш. Сведения о настройке сеансов BGP приведены в статьях [Настройка маршрутизации](expressroute-howto-routing-classic.md) и [Рабочие процессы подготовки каналов и состояния канала](expressroute-workflows.md).

## Номера автономных систем (AS)

Майкрософт будет использовать AS 12076 для общедоступного и частного пиринга Azure, а также пиринга Майкрософт. Мы зарезервировали AS 65515 для внутреннего использования. Поддерживаются 16- и 32-битные номера AS. Для частного пиринга Azure можно использовать частные номера AS. Для общедоступного пиринга Azure и пиринга Майкрософт необходимо использовать общедоступные номера AS, зарегистрированные на ваше имя.

Требования к симметрии передачи данных не предъявляются. Прямые и обратные пути могут проходить по разным парам маршрутов. С каждой стороны в различных принадлежащих вам парах каналов должны быть объявлены идентичные маршруты. Метрики маршрута могут не совпадать.

## Статистическая обработка маршрутов и ограничения префиксов

Мы поддерживаем до 4000 префиксов, объявляемых через частный пиринг Azure. При включении надстройки ExpressRoute Premium это число можно увеличить до 10 000 префиксов. Мы принимаем до 200 префиксов на сеанс BGP для общедоступного пиринга Azure и пиринга Майкрософт.

Если количество префиксов превысит лимит, сеанс BGP будет сброшен. Маршруты по умолчанию принимаются только по связи частного пиринга. Поставщик должен отфильтровывать маршрут по умолчанию и частные IP-адреса (RFC 1918) от путей общедоступного пиринга Azure и пиринга Майкрософт.

## Транзитная и межрегиональная маршрутизация

ExpressRoute нельзя настроить как транзитный маршрутизатор. За услугами транзитной маршрутизации данных обращайтесь к поставщику услуг подключения.

## Объявление маршрутов по умолчанию

Маршруты по умолчанию разрешены только для сеансов частного пиринга Azure. В данном случае мы будем направлять весь трафик из связанных виртуальных сетей в вашу сеть. Объявление маршрутов по умолчанию в частный пиринг вызовет блокировку внутреннего пути из Azure. Для направления входящего и исходящего интернет-трафика служб, размещенных в Azure, используйте ресурсы своей корпоративной сети.

 Для подключения к другим услугам и инфраструктурным службам Azure требуется соблюдение одного из следующих условий:

 - общедоступный пиринг Azure включен для направления трафика в общедоступные конечные точки;
 - маршрутизация определяется пользователем, что обеспечивает интернет-подключение к каждой подсети, требующей подключения к Интернету.

**Примечание.** Объявление маршрутов по умолчанию аннулирует действие лицензий Windows и других виртуальных машин. Чтобы решить эту проблему, выполните [данные инструкции](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx).

## Поддержка сообществ BGP (ожидается в ближайшее время)


Этот раздел содержит общие сведения об использовании сообществ BGP с ExpressRoute. Майкрософт объявляет маршруты в путях общедоступного пиринга и пиринга Microsoft с маршрутами, помеченными значениями соответствующего сообщества. Для чего это нужно и что такое значения сообществ, рассмотрим ниже. При этом Майкрософт не учитывает значения сообществ, которыми помечены маршруты, объявленные для Майкрософт.

Подключившись к Майкрософт через ExpressRoute в любом расположении пиринга в геополитической области, вы получите доступ ко всем облачным службам Майкрософт во всех регионах этой геополитической области.

Например, подключившись через ExpressRoute к Майкрософт в Амстердаме, вы получите доступ ко всем облачным службам Майкрософт, размещенным в Северной и Западной Европе.

Подробный список геополитических регионов, связанных с ними регионов Azure и расположений соответствующих пирингов ExpressRoute см. на странице [Партнеры и одноранговые расположения ExpressRoute](expressroute-locations.md).

Вы можете приобрести больше одного канала ExpressRoute на каждый геополитический регион. За счет геоизбыточности наличие нескольких подключений даст вам более высокий уровень доступности. Имея несколько каналов ExpressRoute, вы будете получать тот же набор префиксов, который объявляется из Майкрософт для путей общедоступного пиринга Azure и пиринга Майкрософт. Это означает, что из вашей сети в Майкрософт будет вести сразу несколько путей. В такой ситуации есть вероятность, что ваша сеть будет выбирать маршрутизацию не самым оптимальным образом, а значит, подключение к некоторым службам может оказаться недостаточно надежным.

Майкрософт помечает префиксы, объявляемые через общедоступный пиринг и пиринг Майкрософт, значениями соответствующих сообществ BGP, которые указывают, в каком регионе размещаются эти префиксы. Значения сообществ помогут вам правильно сделать выбор и обеспечить своим клиентам оптимальную маршрутизацию.

| **Геополитический регион** | **Регион Microsoft Azure** | **Значение сообщества BGP** |
|---|---|---|
| **Северная Америка** | | |
| | Восток США | 12076:51004 |
| | Восток США 2 | 12076:51005 |
| | Запад США | 12076:51006 |
| | Северо-центральный регион США | 12076:51007 |
| | Южно-центральный регион США | 12076:51008 |
| | Центральный регион США | 12076:51009 |
| | Центральная Канада | 12076:51020 |
| | Восточная Канада | 12076:51021 |
| **Северная Америка** | | |
| | Южная часть Бразилии | 12076:51014 |
| **Европа** | | |
| | Северная Европа | 12076:51003 |
| | Западная Европа | 12076:51002 |
| | Север Соединенного Королевства | 12076:51022 |
| | Юг Соединенного Королевства 2 | 12076:51023 |
| **Азиатско-Тихоокеанский регион** | | |
| | Восточная Азия | 12076:51010 |
| | Юго-Восточная Азия | 12076:51011 |
| **Япония** | | |
| | Восточная часть Японии | 12076:51012 |
| | Западная часть Японии | 12076:51013 |
| **Австралия** | | | 
| | Восточная часть Австралии | 12076:51015 |
| | Юго-Восточная часть Австралии | 12076:51016 |
| **Индия** | | |
| | Южная Индия | 12076:51019 |
| | Западная Индия | 12076:51018 |
| | Центральная Индия | 12076:51017 |

Все объявляемые Майкрософт маршруты будут помечаться значением соответствующего сообщества.

>[AZURE.IMPORTANT] Глобальные префиксы обозначаются тегами, содержащими значение соответствующего сообщества, и будут объявлены только в том случае, если включена надстройка ExpressRoute «Премиум».


Наряду с вышеизложенным Майкрософт будет также помечать префиксы в зависимости от того, к какой службе они относятся. Это правило действует только для пиринга Майкрософт. В представленной ниже таблице показано сопоставление служб и значений сообществ BGP.

| **Служба** |	**Значение сообщества BGP** |
|---|---|
| **Exchange** | 12076:5010 |
| **SharePoint** | 12076:5020 |
| **Skype для бизнеса** | 12076:5030 |
| **CRM Online** | 12076:5040 |
| **Другие службы Office 365** | 12076:5100 |


### Применение параметров маршрутизации

Майкрософт не учитывает установленные вами значения сообществ BGP. Для удовлетворения требований [соглашений об уровне обслуживания по доступности](https://azure.microsoft.com/support/legal/sla/) необходимо настроить для каждого пиринга отдельную пару сеансов BGP. Кроме того, вы можете настроить свою сеть таким образом, чтобы она предпочитала одну связь другой, используя для этого стандартные методы работы с маршрутами BGP. К каждой связи можно применить разные локальные параметры BGP, сделав одну связь из вашей сети в Майкрософт более предпочтительной, чем другая. Чтобы воздействовать на поток трафика из Майкрософт, добавьте перед объявлениями маршрутов AS-путь.

## Дальнейшие действия

- Настройте подключение ExpressRoute.

	- [Создание канала ExpressRoute для классической модели развертывания](expressroute-howto-circuit-classic.md) или [Создание и изменение канала ExpressRoute с помощью Azure Resource Manager](expressroute-howto-circuit-arm.md).
	- [Настройка маршрутизации для классической модели развертывания](expressroute-howto-routing-classic.md) или [Настройка маршрутизации для модели развертывания Resource Manager](expressroute-howto-routing-arm.md).
	- [Связывание классической виртуальной сети с каналом ExpressRoutet](expressroute-howto-linkvnet-classic.md) или [Связывание виртуальной сети Resource Manager с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md).

<!---HONumber=AcomDC_0309_2016-->