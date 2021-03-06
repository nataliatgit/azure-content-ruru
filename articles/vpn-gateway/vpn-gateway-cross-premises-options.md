<properties 
   pageTitle="О безопасных распределенных подключениях для виртуальных сетей | Microsoft Azure"
   description="Узнайте о типах безопасных распределенных подключений для виртуальных сетей, включая подключения типа «сеть-сеть», «точка-сеть» и подключения ExpressRoute."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/01/2015"
   ms.author="cherylmc" />

# О безопасных распределенных подключениях для виртуальных сетей

Если требуется безопасное подключение локальных сайтов к виртуальной сети, доступны три варианта: «сеть-сеть», «точка-сеть» и ExpressRoute.

При выборе варианта учитываются различные факторы, в том числе:


- Какая пропускная способность требуется для решения?
- Вы хотите обмениваться данными через общедоступный Интернет по безопасному VPN-подключению или через частное подключение?
- У вас есть общедоступный IP-адрес, который можно использовать?
- Вы планируете использовать VPN-устройство? Если да, является ли оно совместимым?
- Вы подключаете лишь несколько компьютеров или требуется постоянное подключения для сайта?
- Какой тип VPN-шлюза необходим для решения, которое вы хотите создать?

Таблица ниже поможет вам подобрать наилучший вариант подключения для вашего решения.


| - | **Точка — сайт** | **Сайт — сайт** | **ExpressRoute** |
|------------------------------|----------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Поддерживаемые службы Azure** | Облачные службы и виртуальные машины | Облачные службы и виртуальные машины | [Список служб](../expressroute/expressroute-faqs.md#supported-services) |
| **Типичные пропускные способности** | Обычно < 100 Мбит/с (совокупная) | Обычно < 100 Мбит/с (совокупная) | 50 Мбит/с, 100 Мбит/с, 200 Мбит/с, 500 Мбит/с, 1 Гбит/с, 2 Гбит/с, 5 Гбит/с, 10 Гбит/с |
| **Поддерживаемые протоколы** | SSTP (Secure Sockets Tunneling Protocol) | IPsec | Прямое подключение через виртуальные сети, технологии виртуальных частных сетей NSP (MPLS, VPLS) |
| **Маршрутизация** | На основе маршрутов (динамическая) | Мы поддерживаем маршрутизацию на основе политик (статическая маршрутизация) и на основе маршрутов (VPN с динамической маршрутизацией) | BGP |
| **Устойчивость подключения** | Активное-пассивное | Активное-пассивное | Активное-активное |
| **Типичный случай использования** | Создание прототипов, сценарии разработки, тестирования и лабораторного использования для облачных служб и виртуальных машин | Сценарии разработки, тестирования и лабораторного использования, а также маломасштабные рабочие нагрузки для облачных служб и виртуальных машин | Доступ ко всем службам Azure (проверенный список), критически важные рабочие нагрузки и рабочие нагрузки корпоративного уровня, архивация, большие данные, Azure в качестве сайта аварийного восстановления |
| **СОГЛАШЕНИЕ ОБ УРОВНЕ ОБСЛУЖИВАНИЯ** | [СОГЛАШЕНИЕ ОБ УРОВНЕ ОБСЛУЖИВАНИЯ](https://azure.microsoft.com/support/legal/sla/) | [СОГЛАШЕНИЕ ОБ УРОВНЕ ОБСЛУЖИВАНИЯ](https://azure.microsoft.com/support/legal/sla/) | [СОГЛАШЕНИЕ ОБ УРОВНЕ ОБСЛУЖИВАНИЯ](https://azure.microsoft.com/support/legal/sla/) |
| **Цены** | [Цены](https://azure.microsoft.com/pricing/details/vpn-gateway/) | [Цены](https://azure.microsoft.com/pricing/details/vpn-gateway/) | [Цены](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Техническая документация** | [Документация по VPN-шлюзам](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [Документация по VPN-шлюзам](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [Документация по ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) |
| ****Вопросы и ответы ** | [VPN-шлюз: вопросы и ответы](vpn-gateway-vpn-faq.md) | [VPN-шлюз: вопросы и ответы](vpn-gateway-vpn-faq.md) | [Вопросы и ответы по ExpressRoute](../expressroute/expressroute-faqs.md) |


## Подключения типа "сеть-сеть"

VPN типа «сеть-сеть» позволяет создать безопасное подключение между локальным сайтом и виртуальной сетью. Для создания подключения типа «сеть-сеть» VPN-устройство, расположенное в локальной сети, настраивается для безопасного подключения к VPN-шлюзу Azure. После создания подключения ресурсы локальной сети и ресурсы, расположенные в виртуальной сети, могут безопасно взаимодействовать напрямую. Для подключений типа «сеть-сеть» не требуется устанавливать отдельное подключение каждого клиентского компьютера в локальной сети, чтобы предоставить ему доступ к ресурсам в виртуальной сети.

**Используйте подключение типа «сеть-сеть» в следующих случаях:**

- Вы хотите создать гибридное решение.
- Вам необходимо создать подключение между локальным расположением и виртуальной сетью без настройки на стороне клиента.
- Вам требуется постоянное подключение. 

**Требования**

- У локального VPN-устройства должен быть IPv4-адрес с доступом к Интернету. Он не может находиться за NAT.
- VPN-устройство должно быть совместимым. См. статью [О VPN-устройствах](http://go.microsoft.com/fwlink/p/?LinkID=615099). 
- Используемое VPN-устройство должно быть совместимо с типом шлюза, который необходим для решения. См. раздел [Сведения о VPN-шлюзах](vpn-gateway-about-vpngateways.md).
- SKU шлюза также будет влиять на совокупную пропускную способность. См. статью [SKU шлюзов](vpn-gateway-about-vpngateways.md#gateway-skus) для получения дополнительной информации. 

Информацию о настройке VPN-подключения типа "сеть-сеть" к шлюзу с помощью классического портала и классической модели развертывания см. в статье [Настройка виртуальной сети с VPN-подключением "сеть-сеть"](vpn-gateway-site-to-site-create.md). Информацию о настройке VPN-подключения типа «сеть-сеть» с помощью модели развертывания диспетчера ресурсов см. в статье [Создание виртуальной сети с VPN-подключением типа «сеть-сеть» с помощью классического портала Azure](vpn-gateway-create-site-to-site-rm-powershell.md).


## Подключения "точка-сеть"

VPN типа «точка-сеть» также позволяет создать безопасное подключение к виртуальной сети. В конфигурации «точка-сеть» подключение настраивается отдельно на каждом клиентском компьютере, который необходимо подключить к виртуальной сети. Для подключений типа «точка-сеть» не требуются VPN-устройства. Этот тип подключения использует VPN-клиент, который необходимо установить на каждом клиентском компьютере. VPN-подключение устанавливается вручную, с локального клиентского компьютера.

Конфигурации «точка-сеть» и «сеть-сеть» могут существовать одновременно, но, в отличие от подключений типа «сеть-сеть», подключения типа «точка-сеть» нельзя настроить одновременно с подключением ExpressRoute в той же виртуальной сети.

**Используйте подключение типа «точка-сеть» в следующих случаях:**

- Вы хотите настроить лишь несколько клиентов для подключения к виртуальной сети.

- Вы хотите подключиться к виртуальной сети из удаленного расположения. Например, подключиться из кафе или места проведения конференции.

- У вас есть подключение типа «сеть-сеть», но некоторым клиентам необходимо подключаться из удаленного расположения.

- У вас нет доступа к VPN-устройству, которое соответствует минимальным требованиям для подключения типа «сеть-сеть».

- У вашего VPN-устройства нет IPv4-адреса с доступом к Интернету.

Дополнительные сведения о настройке подключения типа «точка-сеть» см. в статье [Настройка VPN-подключения типа «точка-сеть» к виртуальной сети](vpn-gateway-point-to-site-create.md).

## Подключения ExpressRoute

Azure ExpressRoute позволяет создавать частные подключения между центрами обработки данных Azure и инфраструктурой вашей локальной среды или среды для совместного размещения. Подключения ExpressRoute не осуществляются через общедоступный Интернет и обеспечивают повышенный уровень безопасности, надежности и быстродействия с низким уровнем задержки по сравнению с типичными подключениями через Интернет.

В некоторых случаях использование подключений ExpressRoute для передачи данных между локальными сетями и Azure также дает возможность значительно сократить затраты. С помощью ExpressRoute вы можете устанавливать подключения к Azure в расположении ExpressRoute (помещении поставщика Exchange) или подключаться напрямую из имеющейся глобальной сети (например, MPLS VPN), предоставленной поставщиком сетевых услуг.

Дополнительные сведения об ExpressRoute см. в [техническом обзоре ExpressRoute](../expressroute/expressroute-introduction.md).


## Дальнейшие действия

Дополнительную информацию см. в статьях [Часто задаваемые вопросы об ExpressRoute](../expressroute/expressroute-faqs.md) и [Часто задаваемые вопросы о VPN-шлюзе](vpn-gateway-vpn-faq.md).

<!---HONumber=AcomDC_0302_2016-->