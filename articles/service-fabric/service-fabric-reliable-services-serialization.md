<properties
   pageTitle="Сериализация служб Reliable Services | Microsoft Azure"
   description="Основная документация по сериализации служб Reliable Services платформы Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson,tyadam"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/18/2015"
   ms.author="mcoskun"/>

# Сериализация в службах Reliable Services
Диспетчер надежных состояний может содержать несколько надежных объектов. Некоторые из этих надежных объектов могут быть универсальными структурами данных, такими как готовые надежный словарь и надежная очередь. Поскольку это надежные универсальные структуры данных, содержащиеся в них универсальные объекты должны быть сериализованы, чтобы поддерживать репликацию и сохранение на диске.

Диспетчер надежных состояний поддерживает три вида сериализаторов: * настраиваемые сериализаторы, * встроенные сериализаторы (для ограниченного числа типов), * сериализаторы DataContractSerilizer.

Когда надежному объекту требуется выполнить сериализацию объекта, он запрашивает сериализатор для данного типа в диспетчере надежных состояний. Диспетчер надежных состояний сначала проверяет, существует ли настраиваемый сериализатор, зарегистрированный для этого типа входных данных. Если его нет, диспетчер проверяет, может ли один из встроенных сериализаторов сериализовать этот тип данных. Диспетчер надежных состояний включает встроенные сериализаторы для следующих типов данных: guid, bool, byte, sbyte, char, decimal, double, float, int, uint, long, ulong, short, ushort и string. В противном случае он возвращает DataContractSerializer.

В этой статье рассматривается использование настраиваемой сериализации.

## Когда следует использовать настраиваемую сериализацию
Существует два распространенных сценария для использования настраиваемой сериализации: * производительность и * шифрование.

Для типов данных, которые не относятся к встроенным типам, значительного улучшения производительности можно добиться, используя настраиваемые встроенные сериализаторы вместо DataContractSerializer. Одна из причин — то, что настраиваемым сериализаторам не требуется выполнять сериализацию сведений о типе. Service Fabric гарантирует, что сериализатору состояния для данного типа будет предоставляться только этот тип данных для сериализации и десериализации.

Сериализованные данные, созданные сериализаторами, реплицируются и сохраняются на диске. Для конфиденциальных данных может быть желательно гарантировать шифрование данных на диске и в сети.

## Использование настраиваемой сериализации
Чтобы использовать настраиваемый сериализатор для данного типа данных, нам потребуется: * построить настраиваемый сериализатор состояния; * зарегистрировать настраиваемый сериализатор состояния в службах Reliable Services.

### Реализация настраиваемого сериализатора
Настраиваемые сериализаторы должны реализовать интерфейс IStateSerializer<T>. Двумя основными методами в этом интерфейсе являются: * T Read(BinaryReader binaryReader); * void Write(T value, BinaryWriter binaryWriter).

Первый используется объектом ReliableObject для чтения сериализованного объекта из потока с помощью модуля чтения двоичных данных BinaryReader. Второй используется для обратной операции: записи объекта в поток с помощью модуля записи двоичных данных BinaryWriter.

Ниже приведен пример пользовательского класса и сериализатора для него.

```C#
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }
    public short District { get; set; }
    public int Customer { get; set; }
    public long Order { get; set; }
```

```C#
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
    void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
    {
        writer.Write(value.Warehouse);
        writer.Write(value.District);
        writer.Write(value.Customer);
        writer.Write(value.Order);
    }

    OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
    {
        OrderKey value = new OrderKey();
        value.Warehouse = reader.ReadByte();
        value.District = reader.ReadInt16();
        value.Customer = reader.ReadInt32();
        value.Order = reader.ReadInt64();

        return value;
    }

    void IStateSerializer<OrderKey>.Write(OrderKey currentValue, OrderKey newValue, BinaryWriter writer)
    {
        ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
    }

    OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
    {
        return ((IStateSerializer<OrderKey>)this).Read(reader);
    }
}
```
>[AZURE.NOTE]В приведенном выше примере реализация перегрузок Read и Write просто вызывает соответствующие им перегрузки. Это происходит потому, что следующие два метода будут использоваться для компонента, который еще не доступен.

### Регистрация настраиваемого сериализатора
Чтобы зарегистрировать настраиваемый сериализатор, нам сначала потребуется метод, который может регистрировать все настраиваемые сериализаторы. Этот метод не должен принимать аргументы и должен быть способен возвращать задачу. В этом методе интерфейс IReliableStateManager.TryAddStateSerializer<T> должен использоваться для регистрации всех настраиваемых сериализаторов для служб Reliable Services.

```C#
protected Task InitializeStateSerializers()
{
    this.StateManager.TryAddStateSerializer(new OrderKeySerializer());
    return Task.FromResult(false);
}
```

Следующим шагом является регистрация приведенного выше метода как делегата, который будет вызываться диспетчером надежных состояний, когда потребуется зарегистрировать все настраиваемые сериализаторы состояния. Этот метод вызывается только в начале реплики Reliable Services до запуска локального восстановления, поскольку сериализаторам может потребоваться считать сериализованные данные с диска. После регистрации сериализатора соответствующий тип из всех надежных объектов будет использовать данный сериализатор для сериализации и десериализации своих объектов.

```C#
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(
        new ReliableStateManagerConfiguration(
            onInitializeStateSerializersEvent : this.InitializeStateSerializers));
}
```
### Управление версиями
Service Fabric ожидает, что сериализаторы поддерживают бесконечную прямую и обратную совместимость. Прямая и обратная совместимость для типов данных, использующих встроенные сериализаторы, обеспечивается Service Fabric. В типы, использующие DataContractSerializer или настраиваемый сериализатор, пользователь ни в коем случае не должен вносить критические изменения. Сведения об управлении версиями DataContract см. в разделе [Управление версиями контракта данных](https://msdn.microsoft.com/library/ms731138.aspx). Если требуется внести критическое изменение, состояние должно быть перемещено из экземпляра службы со старой версией данных в службу с новой версией данных на уровне приложения.

### Когда используется сериализатор
 * Операции записи для надежных объектов приводят к их сериализации, репликации и сохранению в журнале.
 * После того как в журнал будет записано достаточное число операций, последние данные из памяти сериализуются и записываются в контрольные точки на диске.
 * Для повторного создания реплики файлы, записанные на диск, и недавние данные из журнала отправляются напрямую в побайтовом режиме (т. е. данные не сериализуются повторно) из первичной реплики. Эти данные десериализуются в объекты C# на вторичной реплике.
 * Во время восстановления файлы контрольных точек и недавние данные журнала десериализуются.
 * Во время резервного копирования файлы контрольных точек и недавние данные журнала копируются в побайтовом режиме.
 * Во время восстановления ранее архивированные файлы контрольных точек и данные журнала копируются обратно на свое место и десериализуются.

## Дальнейшие действия
 * [Дополнительные возможности использования модели программирования надежных служб](service-fabric-reliable-services-advanced-usage.md)

<!---HONumber=AcomDC_1203_2015-->