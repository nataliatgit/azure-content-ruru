<properties
   pageTitle="Тесты на хаос и отработку отказа | Microsoft Azure"
   description="Использование сценариев тестов на хаос и отработку отказа Service Fabric для провоцирования ошибок и проверки надежности служб."
   services="service-fabric"
   documentationCenter=".net"
   authors="anmolah"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/03/2016"
   ms.author="anmola"/>

# Сценарии Testability
Крупные распределенные системы, такие как облачные инфраструктуры, ненадежные по своей сути. Azure Service Fabric предоставляет разработчикам возможность создавать службы, которые работают в ненадежных инфраструктурах. Чтобы создать высококачественные службы, разработчики должны иметь возможность проверить стабильность работы своих служб в таких ненадежных инфраструктурах.

Платформа Service Fabric позволяет разработчикам вызывать сбои для тестирования служб на наличие ошибок. Однако на этом преимущества целевого моделирования ошибок заканчиваются. Для дальнейшего развития тестирования вы можете использовать сценарии тестирования в Service Fabric: хаотический тест и тест отработки отказа. Они моделируют на кластере непрерывные ошибки с чередованием, как нормальные, так и ненормальные, на протяжении долгого периода времени. После настройки коэффициента и типа ошибок сценарии выполняются как клиентские средства через интерфейсы API C# или PowerShell, создавая ошибки на кластере и в службе.

## Хаотический тест
Сценарий хаотического тестирования создает ошибки в масштабах всего кластера Service Fabric. Этот сценарий позволяет всего за несколько часов проработать ошибки, которые обычно наблюдаются в течение многих месяцев или лет. Сочетание ошибок с чередованием с высокой частотой возникновения сбоев позволяет находить проблемы, которые в противном случае были бы упущены. Это обеспечивает значительное улучшение качества кода службы.

### Ошибки, моделируемые в хаотическом тесте
 - Перезапуск узла
 - Перезапуск развернутого пакета кода
 - Удаление реплики
 - Перезапуск реплики
 - Перемещение первичной реплики (необязательно)
 - Перемещение вторичной реплики (необязательно)

Хаотический тест выполняет несколько итераций сбоев и проверок кластера в течение указанного периода времени. Кроме того, вы можете настроить время, затрачиваемое на стабилизацию работы и проверку кластера для успешной его работы. Сценарий завершается ошибкой, если при проверке кластера обнаруживается сбой.

Рассмотрим тест, запланированный на выполнение в течение одного часа и включающий не более трех одновременных сбоев. В ходе теста будут вызваны три ошибки, а затем проверена работоспособность кластера. Во время теста предыдущий шаг будет повторяться, пока кластер не станет неработоспособным или не пройдет один час. Если при любой итерации кластер становится неработоспособным, т. е. его работа не стабилизируется в течение заданного времени, тест завершится ошибкой с исключением. Это исключение указывает, что возникли проблемы, которые необходимо дополнительно изучить.

В своей текущей форме механизм создания ошибок при хаотическом тесте вызывает только безопасные сбои. Это означает, что при отсутствии внешних ошибок исключена вероятность потери кворума или данных.

### Важные параметры конфигурации
 - **TimeToRun**. Общее время выполнения теста до успешного завершения. Тест может завершиться ранее вместо ошибки проверки.
 - **MaxClusterStabilizationTimeout**. Максимальный период ожидания возврата кластера в работоспособное состояние до завершения теста с ошибкой. Проверяется, работоспособны ли кластер и службы, соответствует ли размер набора целевых реплик разделу службы, а также отсутствуют ли реплики InBuild.
 - **MaxConcurrentFaults**. Максимальное количество одновременных ошибок, вызываемых при каждой итерации. Чем больше это число, тем более интенсивно проводится тест, что приведет к более сложным отработкам отказа и переходам. Во время этого теста при отсутствии внешних ошибок исключена вероятность потери кворума или данных, независимо от значения этого параметра.
 - **EnableMoveReplicaFaults**: включает или отключает ошибки, что приводит к перемещению первичных или вторичных реплик. Эти ошибки отключены по умолчанию.
 - **WaitTimeBetweenIterations**: время ожидания между итерациями, то есть после цикла ошибок и соответствующей проверки.

### Выполнение хаотического теста
Пример на языке C#

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## Тест Failover Test

Сценарий тестирования отработки отказа — это версия сценария хаотического тестирования, предназначенная для конкретного раздела службы. В ходе этого теста проверяется влияние отработки отказа на определенный раздел службы (другие службы не затрагиваются). Если ввести сведения о целевом разделе и других параметрах, тест выполняется как клиентское средство, которое использует API C# или PowerShell, чтобы создавать ошибки для раздела службы. В ходе сценария повторяется последовательность моделируемых сбоев и проверка службы, в то время как бизнес-логика обеспечивает рабочую нагрузку. Ошибка при проверке службы указывает на проблему, которую необходимо дополнительно изучить.

### Ошибки, моделируемые во время теста отработки отказа
- Перезапуск развернутого пакета кода в расположении раздела
- Удаление первичной или вторичной реплики либо экземпляра без отслеживания состояния
- Перезапуск первичной или вторичной реплики (в материализованной службе)
- Перемещение первичной реплики
- Перемещение вторичной реплики
- Перезапуск раздела

В ходе теста отработки отказа вызывается выбранная ошибка, а затем выполняется проверка службы для обеспечения ее стабильной работы. Во время теста отработки отказа одновременно вызывается только одна ошибка в отличие от хаотического теста, в ходе которого может инициироваться несколько сбоев. Если после каждого сбоя раздел службы не стабилизируется в течение заданного времени ожидания, тест не пройден. Этот тест вызывает только безопасные ошибки. Это означает, что при отсутствии внешних ошибок исключена вероятность потери кворума или данных.

### Важные параметры конфигурации
 - **PartitionSelector** — объект выбора, указывающий целевой раздел.
 - **TimeToRun** — общее время выполнения теста до завершения.
 - **MaxServiceStabilizationTimeout** — максимальный период ожидания возврата кластера в работоспособное состояние до завершения теста с ошибкой. Проверяется, работоспособна ли служба, соответствует ли размер набора целевых реплик всем разделам, а также отсутствуют ли реплики InBuild.
 - **WaitTimeBetweenFaults** — время ожидания между каждым циклом сбоя и проверки.

### Выполнение теста отработки отказа
Пример на языке C#

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario chaosScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


PowerShell

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```

<!---HONumber=AcomDC_0211_2016-->