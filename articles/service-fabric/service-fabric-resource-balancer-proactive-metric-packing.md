<properties
   pageTitle="Упреждающее упаковывание метрик | Microsoft Azure"
   description="Общие сведения об использовании упреждающего упаковывания метрик в балансировщике ресурсов"
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

# Упреждающее упаковывание метрик

Целью типичной конфигурации балансировщика ресурсов в Azure Service Fabric является достижение равномерного использования служб на всех узлах по всем метрикам. Кроме того, балансировщик ресурсов отвечает за поиск места для службы, когда поступают запросы на размещение новых служб. Если свободного места для размещения новых реплик службы (всех реплик всех разделов службы) нет в достаточном объеме, балансировщик ресурсов пытается освободить место путем перемещения существующих рабочих нагрузок. Это совершенно нормальный процесс, но для него может потребоваться время в зависимости от заполненности кластера и степени фрагментации рабочих нагрузок.

Например, если кластер прилично загружен, а клиент хочет добавить новую службу с большой нагрузкой по умолчанию (скажем, максимальную производительность узла по одной или нескольким метрикам), возможно, что для размещения этой службы балансировщику ресурсов придется перенести много реплик в другое место. Кроме того, если службы отслеживают свое состояние и имеют большие размеры, перенос реплик может занять некоторое время, необходимое для копирования данных. Два этих момента могут увеличить время создания новой службы. Обычно службы должны быть устойчивы к нечастым ситуациям с длительным временем создания, но некоторые рабочие нагрузки менее устойчивы к ним и стремятся быть созданными как можно быстрее. Это означает, что в стабильном состоянии балансировщику ресурсов нужно убедиться, что кластер "дефрагментируется", потому что это позволит повысить шансы найти свободное место для новых рабочих нагрузок.

Когда балансировщик ресурсов переходит к этапу балансировки, запускается механизм упреждающего упаковывания метрик (также называемый дефрагментацией). Это делается с целью минимизировать время создания службы путем упаковки рабочих нагрузок на меньшем количестве узлов (во время балансировки рабочие нагрузки, наоборот, распределяются между большим числом узлов). Когда метрика настроена для дефрагментации, балансировщик ресурсов стремится достичь максимального среднего стандартного отклонения (во время балансировки целью является минимальное среднее стандартное отклонение).

Достигнув максимального отклонения, балансировщик ресурсов попытается разместить на некоторых узлах как можно больше служб и сохранить как можно больше узлов пустыми. Кроме того, одно из базовых ограничений для размещения новых служб состоит в том, что реплики не могут находиться в одном домене обновления или домена сбоя. Так как целью является быстрое добавление новых служб, балансировщик ресурсов должен стремиться к минимальному стандартному отклонению распределения нагрузки между доменами обновления и доменами сбоя (сумма нагрузок служб на домен обновления или сбоя). В результате получится такой же объем свободного места в домене обновления или сбоя. Кроме того, при дефрагментации учитываются все другие ограничения в системе, в частности сходство, ограничения на размещение и производительность метрик узла.

## Конфигурация кластера для балансировщика ресурсов
В манифесте кластера указываются следующие значения конфигурации, определяющие общее поведение при упаковке метрик в балансировщике ресурсов.

### DefragmentationMetrics

Метрики дефрагментации, которые балансировщик ресурсов должен учитывать при упреждающем упаковывании (дефрагментации). В этом списке должны быть указаны все настроенные метрики (так же, как и в списках пороговых значений действий и балансировки). Если для метрики задано значение True, она будет считаться дефрагментационной, а если задано значение False (или метрика вообще не указана в этом списке), она не будет учитываться при определении необходимости дефрагментации.

``` xml
<FabricSettings>
  <Section Name="DefragmentationMetrics">
    <Parameter Name="MetricName1" Value="true"/>
    <Parameter Name="MetricName2" Value="false"/>
  </Section>
</FabricSettings>
```

### BalacingThreshholds

Пороговые значения балансировки определяют степень фрагментации кластера по определенным метрикам до того, как балансировщик ресурсов перейдет к этапу балансировки (будет задействована логика упаковывания метрик). Если метрика считается дефрагментационной, пороговым значением балансировки будет минимальное отношение между максимально и минимально используемыми узлами в домене обновления или сбоя (отношение, при котором балансировщик ресурсов не начинает дефрагментацию кластера). Если в каком-либо домене обновления или сбоя данное отношение меньше порогового значения, начинается этап дефрагментации.

На следующем рисунке показаны два примера, в которых пороговое значение балансировки для заданной метрики равно 10.

![Примеры пороговых значений балансировки][Image1]

Сейчас при определении "использования" узла не учитывается его размер (то есть емкость). В расчет принимается только фактическое использование узла для заданной метрики.

Если метрика не указана, значением по умолчанию берется 1. В этом случае дефрагментация будет выполняться до тех пор, пока в кластере не появится хотя бы по одному пустому узлу в каждом домене обновления и сбоя.

Значение 0 означает, что эта метрика не будет учитываться во время этапа балансировки, даже если она учитывается при определении необходимости дефрагментации.

В приведенном ниже примере кода показано, что пороговые значения балансировки настраиваются для каждой метрики отдельно с помощью элемента FabricSettings в манифесте кластера.

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия

Дополнительные сведения см. в статье [Архитектура балансировщика ресурсов](service-fabric-resource-balancer-architecture.md).

[Image1]: media/service-fabric-resource-balancer-proactive-metric-packing/PMP.png

<!---HONumber=AcomDC_1223_2015-->