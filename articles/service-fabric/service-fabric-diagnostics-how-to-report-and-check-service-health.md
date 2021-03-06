<properties
   pageTitle="Проверка работоспособности и оповещение о проблемах в Azure Service Fabric | Microsoft Azure"
   description="Узнайте, как отправлять отчеты о работоспособности из кода службы и проверять работоспособность службы с использованием средств наблюдения за работоспособностью Azure Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="toddabel"/>


# Проверка работоспособности службы и оповещение о проблемах
Если при работе служб возникают проблемы, то ваша возможность ответить на все возникшие инциденты и устранить их зависит от того, насколько быстро вы сможете обнаружить проблему. Сообщив о проблемах и сбоях диспетчеру работоспособности Service Fabric из кода службы, вы сможете использовать стандартные средства мониторинга работоспособности, которые Service Fabric предоставляет для проверки работоспособности.

В этой статье будет рассмотрен пример добавления отчета о работоспособности к службе и проверки состояния работоспособности с помощью средств, предоставляемых Service Fabric. Эта статья представляет собой краткое введение в возможности Service Fabric по отслеживанию работоспособности. Для получения дополнительных сведений вы можете прочесть серию подробных статей о работоспособности, начиная со ссылки в конце этого документа.

## Предварительные требования
Для начала работы должно быть установлено следующее: * Visual Studio 2015 * пакет SDK Service Fabric

## Развертывание приложения и проверка его работоспособности
Чтобы развернуть приложение и проверить его работоспособность, выполните следующие действия.

1. Запустите Visual Studio от имени администратора.

2. Создайте проект для службы с отслеживанием состояния.

  ![Создание приложения Service Fabric со службами с отслеживанием состояния](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. Нажмите **F5**, чтобы запустить приложение в режиме отладки. Приложение будет развернуто на локальный кластер.

4. После запуска приложения запустите обозреватель Service Fabric, щелкнув правой кнопкой мыши Диспетчер локального кластера на панели задач и выбрав **Управление локальным кластером** в контекстном меню.

![Запуск обозревателя Service Fabric из панели задач](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)

5. Состояние работоспособности приложения должно отображаться, как показано на следующем рисунке. Приложение должно работать исправно и без ошибок.

  ![Работоспособное приложение в обозревателе Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. Кроме того, вы можете проверить работоспособность с помощью PowerShell. Вы можете проверить работоспособность приложения с помощью команды ```Get-ServiceFabricApplicationHealth```, а службы — с помощью команды ```Get-ServiceFabricServiceHealth```. Отчет о работоспособности для этого же приложения в PowerShell выглядит следующим образом. ![Работоспособное приложение в PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## Добавление пользовательских событий работоспособности в код службы
В шаблонах проектов Service Fabric в Visual Studio приведен пример кода, Следующие шаги показывают, как сообщить о пользовательских событий работоспособности из кода службы. Такие отчеты автоматически появятся в стандартных средствах для наблюдения за работоспособностью, предоставляемых Service Fabric, таких как обозреватель Service Fabric, представление работоспособности портала Azure и PowerShell.

1. Повторно откройте приложение, созданное ранее в Visual Studio, или создайте новое приложение для службы с отслеживанием состояния на основе шаблонов Visual Studio.
2. Откройте файл **Stateful1.cs**. Найдите объявление `var myDictionary` и добавьте следующий код сразу же после объявления `var myDictionary`. Созданный здесь объект `fabricClient` будет использоваться позже для отчета работоспособности.

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    Кроме того, добавьте следующее пространство имен в файл **Stateful1.cs**.

    ```csharp
    using System.Fabric;
    ```

4. Затем найдите вызов `myDictionary.TryGetValueAsync` в методе *RunAsync*. Как видите, этот метод возвращает `result`, который содержит текущее значение счетчика, так как основная логика этого приложения — поддерживать работу счетчика. В реальном приложении, если отсутствие результата означает сбой, необходимо сообщить об этому диспетчеру работоспособности.
5. Чтобы сообщить о событии отсутствия результата, представляющего сбой, добавьте приведенный ниже код после вызова `myDictionary.TryGetValueAsync`. Мы сообщаем о событии как о `StatefulServiceReplicaHealthReport`, так как сообщение приходит от службы с отслеживанием состояния. Идентификаторы секции PartitionId и реплики ReplicaId, передаваемые в событии отчета, помогут определить источник этого отчета при просмотре отчета в одном из средств наблюдения за работоспособностью. Это важно, поскольку развернутая служба с отслеживанием состояния может иметь несколько секций и каждая секция может иметь несколько реплик. Параметр `HealthInformation` содержит сведения о проблеме с работоспособностью, о которой сообщается в отчете. Добавьте следующее пространство имен в файл **Stateful1.cs**.

    ```csharp
    using System.Fabric.Health;
    ```

    Добавьте следующий код после вызова `myDictionary.TryGetValueAsync`.

    ```csharp
    if(!result.HasValue)
    {
        var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));

        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

5. Давайте сымитируем этот сбой и посмотрим, как он будет отображаться в средствах наблюдения за работоспособностью. Чтобы сымитировать сбой, закомментируйте первую строку в коде проверки работоспособности, который был добавлен ранее. После этого код будет выглядеть так, как показано ниже. Теперь отчет о работоспособности будет отправляться каждый раз, когда выполняется RunAsync. После внесения изменений запустите приложение с помощью клавиши **F5**.

    ```csharp
    //if(!result.HasValue)
    {
        var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));

        var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

6. После запуска приложения откройте обозреватель Service Fabric для проверки работоспособности приложения. На этот раз обозреватель Service Fabric покажет, что работоспособность приложения нарушена. Это вызвано ошибкой из кода, который мы добавили выше. ![Неработоспособное приложение в обозревателе Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. При выборе основной реплики в древовидном представлении обозревателя Service Fabric вы увидите, что в ней также отображается нарушение работоспособности. Кроме того, в нем отображаются подробные сведения о работоспособности, добавленные в параметр `HealthInformation` в коде. Эти же отчеты о работоспособности вы можете просмотреть PowerShell, а также на портале Azure. ![Работоспособность реплики в обозревателе Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Этот отчет останется в диспетчере работоспособности до тех пор, пока не будет заменен другим отчетом или пока эта реплика не будет удалена. Так как мы не задали параметр TimeToLive для этого отчета о работоспособности в объекте HealthInformation, срок его действия никогда не истечет. Для запроса и сообщения о работоспособности FabricClient должен иметь права администратора.

## Дальнейшие действия
[Подробный обзор работоспособности в Service Fabric](service-fabric-health-introduction.md)

<!---HONumber=AcomDC_0224_2016-->