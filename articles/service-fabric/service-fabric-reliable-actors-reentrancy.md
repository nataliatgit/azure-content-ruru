<properties
   pageTitle="Повторный вход надежных субъектов | Microsoft Azure"
   description="Общие сведения о повторном входе для надежных субъектов Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/19/2016"
   ms.author="amanbha"/>


# Повторный вход надежных субъектов
Среда выполнения субъектов Fabric по умолчанию разрешает повторный вход на основе логического контекста вызова. Это позволяет субъектам выполнять повторный вход, если они находятся в той же цепочке контекста вызова. Например, субъект A отправляет сообщение субъекту B, который отправляет сообщение субъекту C. В ходе обработки сообщения, если субъект C осуществит вызов субъекта A, сообщение будет означать повторный вход, поэтому будет разрешено. Любые другие сообщения, являющиеся частью другого контекста вызова, будут заблокированы на субъекте A до тех пор, пока он не завершит обработку.

Субъекты, которым необходимо запретить повторный вход на основе логического контекста вызова, могут отключить его путем оформления класса субъекта с `ReentrantAttribute(ReentrancyMode.Disallowed)`.

```csharp
public enum ReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```

В следующем коде показан класс субъекта, который задает `ReentrancyMode.Disallowed` в качестве режима повторного входа. В этом случае, если субъект отправляет сообщение повторного входа другому субъекту, будет выдано исключение типа `FabricException`.

```csharp
[Reentrant(ReentrancyMode.Disallowed)]
class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    ...
}
```

<!---HONumber=AcomDC_0224_2016-->