<properties
   pageTitle="Настройка конечных точек службы Service Fabric | Microsoft Azure"
   description="В этой статье поясняется, как описать ресурсы конечной точки в манифесте служб, включая настройку конечных точек HTTPS."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/26/2016"
   ms.author="sumukhs"/>

# Указание ресурсов в манифесте службы

## Обзор

Манифест служб позволяет объявлять и изменять ресурсы, используемые в службе, не меняя скомпилированный код. Azure Service Fabric поддерживает настройку ресурсов конечных точек для службы. Доступ к ресурсам, указанным в манифесте служб, можно контролировать в манифесте приложения с помощью элемента SecurityGroup. Объявление ресурсов позволяет изменять их при развертывании, т. е. службе не нужно внедрять новый механизм настройки.

## Конечные точки

Когда ресурс конечной точки определяется в манифесте служб, Service Fabric назначает порты из зарезервированного диапазона портов приложений, если порт не указан явно (пример — конечная точка *ServiceEndpoint1* ниже). Кроме того, службы также могут запрашивать наличие в ресурсе конкретного порта. Репликам службы, которые выполняются на различных узлах кластера, можно назначить другие номера портов, а реплики этой же службы, выполняющиеся на аналогичном узле, будут использовать тот же порт. Такие порты могут использоваться репликами службы для различных целей, таких как репликация, прослушивание клиентских запросов и т. д.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Дополнительные сведения о создании ссылок на конечные точки из файла параметров пакета конфигурации (settings.xml) см. в статье [Настройка надежных служб с отслеживанием состояния](service-fabric-reliable-services-configuration.md).

## Пример. Указание конечной точки HTTP для службы

В следующем манифесте служб в элементе &lt;Resources&gt; определяется один ресурс конечной точки TCP и два ресурса конечной точки HTTP.

Service Fabric автоматически создает список управления доступом (ACL) для конечных точек HTTP.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## Пример. Указание конечной точки HTTPS для службы

Протокол HTTPS обеспечивает аутентификацию сервера, а также используется для шифрования данных, передаваемых между клиентом сервером. Чтобы включить эти функции в службе Service Fabric, при определении службы в разделе *Ресурсы > Конечные точки > Конечная точка* манифеста служб укажите протокол, как указано ранее для конечной точки *ServiceEndpoint3*.

>[AZURE.NOTE] Протокол службы нельзя изменить при обновлении приложения, так как это будет критическим изменением.


Ниже приведен пример ApplicationManifest, который необходимо задать для HTTPS. (Потребуется предоставить отпечаток для сертификата). EndpointRef является ссылкой на EndpointResource в ServiceManifest, для которого задается протокол HTTPS. Можно добавить несколько элементов Endpointcertificate.

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

<!---HONumber=AcomDC_0211_2016-->