<properties
   pageTitle="Архитектура балансировщика ресурсов | Microsoft Azure"
   description="Обзор архитектуры балансировщика ресурсов Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="masnider"/>

# Обзор архитектуры балансировщика ресурсов

![Архитектура балансировщика ресурсов][Image1]

Балансировщик ресурсов в Azure Service Fabric состоит из одной централизованной службы балансировки ресурсов и компонента, который присутствует на каждом узле. По своему характеру это похоже на диспетчер отработки отказов Service Fabric и локальный узел Service Fabric соответственно.

Локальный агент отвечает за сбор и буферизацию отчетов о нагрузке из служб, работающих на локальном узле, за отправку отчетов в службу балансировщика ресурсов и за передачу сведений об отказах и других событиях в диспетчер отработки отказов и балансировщик нагрузки (1 и 2 на рисунке выше). Балансировщик ресурсов и диспетчер отработки отказов взаимодействуют друг с другом для реагирования на события нагрузки и другие события в системе. Эти события могут включать в себя сбои реплик или узлов, отчеты о нагрузке, а также создаваемые и удаляемые службы и приложения.

Например, в случае отказа реплики диспетчер отработки отказов просит балансировщик ресурсов указать другое расположение, исходя из данных о нагрузке, полученных с других узлов. Аналогичным образом при получении запроса на размещение новой службы диспетчер отработки отказов просит балансировщик ресурсов порекомендовать, где можно разместить эту службу. Во всех этих случаях балансировщик ресурсов в качестве ответа вносит изменения в конфигурации различных служб (3). Затем эти изменения реализуются диспетчером отработки отказов. В примере выше диспетчер отработки отказов создает на узле новую реплику, что позволяет достичь оптимального баланса (4).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия

Компоненты балансировки ресурсов:

- [Описание кластера](service-fabric-resource-balancer-cluster-description.md)
- [Описание служб](service-fabric-resource-balancer-service-description.md)
- [Передача данных о динамической нагрузке](service-fabric-resource-balancer-dynamic-load-reporting.md)
- [Упреждающее упаковывание метрик](service-fabric-resource-balancer-proactive-metric-packing.md)
- [Процент свободного места на узле](service-fabric-resource-balancer-node-buffer-percentage.md)

[Image1]: media/service-fabric-resource-balancer-architecture/Service-Fabric-Resource-Balancer-Architecture.png

<!---HONumber=AcomDC_1223_2015-->