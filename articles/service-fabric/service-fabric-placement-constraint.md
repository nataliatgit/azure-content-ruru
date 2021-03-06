<properties
   pageTitle="Ограничения на размещение при управлении кластером Service Fabric | Microsoft Azure"
   description="Обзор понятия ограничения на размещение в Service Fabric"
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
   ms.date="02/03/2016"
   ms.author="masnider"/>

# Общие сведения об ограничениях на размещение

Azure Service Fabric позволяет разработчикам использовать ограничения на размещение реплик служб на узлах, не удовлетворяющих определенным условиям. Условия выражаются через логическое выражение, в которое при расчете подставляются конкретные значения в соответствии с контекстом конкретной службы.


## Возможности
С помощью ограничений на размещение вы можете:

- используя свойства узлов, настроить размещение определенных типов служб только на определенных типах узлов;

- выбирать ограничения для применения к первичной и вторичной репликам службы или только к первичным репликам.


## Основные понятия
Свойство узла — определяемое пользователем или системой сопоставление строки и значения. На разных узлах могут быть определены разные свойства (пример: NodeName).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия

Дополнительные сведения: [Сценарии приложений](service-fabric-application-scenarios.md).

<!---HONumber=AcomDC_0211_2016-->