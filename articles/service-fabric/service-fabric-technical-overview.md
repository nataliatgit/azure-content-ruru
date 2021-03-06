<properties
   pageTitle="Технический обзор Service Fabric | Microsoft Azure"
   description="Технический обзор платформы Service Fabric. Описание основных понятий и архитектуры."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="chackdan;subramar"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="mfussell"/>

# Технический обзор платформы Service Fabric

Service Fabric — это платформа распределенных систем, которая дает возможность не только легко упаковывать и развертывать масштабируемые и надежные микрослужбы, но и управлять ими.

## Основные термины
В этом разделе описана терминология, используемая в контексте Service Fabric. Это позволит вам понимать термины, повсеместно используемые в документации.

**Кластер**. Подключенный к сети набор виртуальных машин или физических компьютеров, в котором вы развертываете микрослужбы и управляете ими. Кластеры можно масштабировать до нескольких тысяч машин.

**Узел**. Компьютер или виртуальная машина, которая входит в состав кластера, называется узлом. Каждому узлу назначается имя (строка). Узлы имеют свои характеристики, в частности свойства размещения. Каждый компьютер или виртуальная машина имеет автоматически запускаемую службу Windows, `FabricHost.exe`. Она запускается после загрузки системы и запускает два исполняемых файла: `Fabric.exe` и `FabricGateway.exe`. Эти два файла формируют узел. В целях тестирования на одном компьютере или виртуальной машине можно разместить несколько узлов, запустив несколько экземпляров `Fabric.exe` и `FabricGateway.exe`.

**Тип приложения**. Имя и версия, назначенные набору типов служб. Эта информация указывается в файле `ApplicationManifest.xml`, который хранится в каталоге пакета приложения. Этот каталог затем копируется в хранилище образов кластера Service Fabric. Из этого типа приложения в кластере можно создать именованное приложение.

Дополнительные сведения о модели приложений см. [здесь](service-fabric-application-model.md).

**Пакет приложения**. Каталог на диске, в котором хранится файл типа приложения, `ApplicationManifest.xml`. В этом файле указаны ссылки на пакеты служб, типы которых определяют тип приложения. Файлы в каталоге пакета приложения копируются в хранилище образов кластера Service Fabric. Например, пакет почтового приложения может содержать ссылки на пакет службы очередей, пакет службы внешнего интерфейса и пакет службы базы данных.

**Именованное приложение**. Когда пакет приложения будет скопирован в хранилище образов, вы сможете создать в кластере экземпляр приложения. Для этого нужно указать тип приложения в пакете приложения (имя и версию). Каждому экземпляру приложения этого типа назначается универсальный код ресурса (URI), который выглядит так: `"fabric:/MyNamedApp"`. Из одного типа приложения в кластере можно создать несколько именованных приложений. Именованные приложения можно также создавать из разных типов приложений. Для каждого именованного приложения управление и контроль версий выполняются отдельно.

**Тип службы**. Имя и версия, назначенные пакетам кода, пакетам данных и пакетам конфигураций службы. Эта информация указывается в файле `ServiceManifest.xml`, который хранится в каталоге пакета службы. Затем ссылка на этот каталог добавляется в файл пакета приложения, `ApplicationManifest.xml`. Создав именованное приложение, вы можете создать в кластере именованную службу, используя для этого один из типов служб соответствующего типа приложения. Файл типа службы, `ServiceManifest.xml`, содержит ее описание.

Дополнительные сведения о модели приложений см. [здесь](service-fabric-application-model.md).

Существует два типа служб.

- **Без отслеживания состояния**. Служба без отслеживания состояния используется, когда устойчивое состояние службы хранится во внешней службе хранилища, например в службе хранилища Azure, базе данных SQL Azure или Azure DocumentDB. Этот тип службы также следует использовать, когда у службы нет постоянного хранилища. Примером может быть служба калькулятора: в службу передаются значения, служба выполняет вычисления с использованием этих значений и возвращает результат.

- **С отслеживанием состояния**. Служба с отслеживанием состояния используется, когда нужно, чтобы платформа Service Fabric управляла состоянием службы через модели программирования Reliable Collections или Reliable Actors. При создании именованной службы вы указываете, в скольких секциях должно храниться ее состояние (обеспечение масштабируемости), а также сколько раз ее состояние должно прореплицироваться на узлы (обеспечение надежности). У каждой именованной службы есть одна первичная реплика и несколько вторичных. Состояние именованной службы изменяется путем записи в первичную реплику. После этого Service Fabric реплицирует это состояние во все вторичные реплики, тем самим синхронизируя текущее состояние. Когда происходит сбой первичной реплики, платформа Service Fabric автоматически обнаруживает это, делает одну вторичную реплику первичной и создает новую вторичную реплику.

**Пакет службы**. Каталог на диске, в котором хранится файл типа службы, `ServiceManifest.xml`. В этом файле указаны ссылки на пакеты кода, статических данных и конфигураций для данного типа службы. Ссылки на файлы из каталога пакетов служб указываются в файле `ApplicationManifest.xml` для соответствующего типа приложения. Например, в пакете службы могут быть указаны ссылки на пакеты кода, статических данных и конфигураций, определяющие службу баз данных.

**Именованная служба**. Создав именованное приложение, вы можете создать в кластере экземпляр одной из его служб определенного типа, используя для этого соответствующий тип службы (имя и версию). Каждому экземпляру службы определенного типа назначается код URI, частью которого будет URI именованного приложения. Например, если в именованном приложении MyNamedApp вы создадите именованную службу MyDatabase, ее URI будет выглядеть так: `"fabric:/MyNamedApp/MyDatabase"`. В именованном приложении можно создать несколько именованных служб. У каждой именованной службы может быть своя схема секционирования и свое количество реплик и экземпляров.

**Пакет кода**. Каталог на диске, в котором хранятся исполняемые файлы службы определенного типа (обычно файлы EXE или DLL). Ссылки на файлы из каталога пакетов кода указываются в файле `ServiceManifest.xml` для соответствующего типа службы. После создания именованной службы файлы из пакета кода копируются на узлы, на которых платформа Service Fabric будет запускать именованную службу, после чего начинается выполнение кода. Существует два типа исполняемых файлов для пакетов кода.

- **Гостевые исполняемые файлы**. Исполняемые файлы, выполняющиеся "как есть" в операционной системе сервера виртуальных машин (Windows или Linux). То есть эти файлы никак не связаны с файлами среды выполнения Service Fabric, в связи с чем они не используют ни одну из моделей программирования Service Fabric. Исполняемые файлы этого типа не могут использовать некоторые возможности платформы Service Fabric. В частности, они не могут использовать службу именования для обнаружения конечных точек и не могут передавать показатели нагрузки по каждому экземпляру службы.

- **Исполняемые файлы узла службы**. Это исполняемые файлы, использующие модели программирования Service Fabric через создание связей с файлами среды выполнения Service Fabric. Таким образом части исполняемого кода связываются с платформой Service Fabric, что обеспечивает дополнительные возможности. Например, экземпляр именованной службы может регистрировать конечные точки в службе именования Service Fabric и передавать показатели нагрузки.

**Пакет данных**. Каталог на диске, в котором хранятся статические файлы данных службы определенного типа, предназначенные только для чтения (обычно фотографии, аудио и видео). Ссылки на файлы из каталога пакетов данных указываются в файле `ServiceManifest.xml` для соответствующего типа службы. После создания именованной службы файлы из пакета данных копируются на узлы, на которых платформа Service Fabric будет запускать именованную службу, после чего начинается выполнение кода. После этого код может обращаться к файлам данных.

**Пакет конфигурации**. Каталог на диске, в котором хранятся статические файлы конфигурации службы определенного типа, предназначенные только для чтения (обычно текстовые файлы). Ссылки на файлы из каталога пакетов конфигураций указываются в файле `ServiceManifest.xml` для соответствующего типа службы. После создания именованной службы файлы из пакета конфигурации копируются на узлы, на которых платформа Service Fabric будет запускать именованную службу, после чего начинается выполнение кода. После этого код может обращаться к файлам конфигурации.

**Хранилище образов**. В каждом кластере Service Fabric есть свое хранилище образов. Вам необходимо скопировать содержимое пакета приложения в хранилище образов и затем зарегистрировать тип приложения в этом пакете приложения. Когда тип приложения будет подготовлен, вы сможете создать из него именованное приложение. Удалить регистрацию типа приложения из хранилища образов можно только после удаления всех связанных с этим типом именованных приложений.

Дополнительные сведения о развертывании пакетов приложений в хранилище образов см. в статье [Развертывание приложения](service-fabric-deploy-remove-applications.md).

**Схема секционирования**. При создании именованной службы нужно указать схему секционирования. Службы с большим количеством состояний разделяют данные по секциям, которые распределены между узлами кластера. Это позволяет состоянию именованной службы масштабироваться. В пределах одной секции именованные службы без отслеживания состояния хранят экземпляры, а именованные службы с отслеживанием состояния — реплики. Как правило, именованные службы без отслеживания состояния используют только одну секцию, так как у них нет внутреннего состояния. Экземпляры в секциях обеспечивают доступность службы — если один экземпляр выходит из строя, другие продолжают работать нормально, а Service Fabric создает новый экземпляр. Именованные службы с отслеживанием состояния хранят сведения о своем состоянии в репликах, и у каждой секции есть свой набор реплик. Все сведения о состоянии службы постоянно синхронизируются. Если в реплике возникает сбой, Service Fabric создает новую реплику из существующих.

Дополнительные сведения см. в статье [Секционирование служб Reliable Services в Service Fabric](service-fabric-concepts-partitioning.md).

**Модели программирования**. Существует две модели программирования для .NET Framework, с помощью которых вы можете создавать службы Service Fabric.

- Reliable Services. Интерфейс API для создания служб без отслеживания и с отслеживанием состояния. Служба с отслеживанием состояния хранит сведения о своем состоянии в коллекциях Reliable Collections (например, словарь или очередь). Кроме того, вы можете использовать разнообразные коммуникационные стеки, например веб-API и Windows Communication Foundation (WCF).

- Reliable Actors. Интерфейс API для создания объектов без отслеживания и с отслеживанием состояния с помощью модели программирования с виртуальными акторами. Эту модель целесообразно использовать, если у вас есть множество независимых единиц вычисления или состояния. Так как в этой модели применяется пошаговая потоковая модель, мы не рекомендуем использовать код, который обращается к другим акторам или службам, потому что отдельный актор не может обрабатывать другие входящие запросы, пока не будут обработаны все его исходящие запросы.

Дополнительные сведения см. в статье [Выбор платформы для службы](service-fabric-choose-framework.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия
Дополнительные сведения о платформе Service Fabric см. в следующих статьях.

- [Общие сведения о Service Fabric](service-fabric-overview.md)
- [Разработка приложений с использованием микрослужб](service-fabric-overview-microservices.md)
- [Сценарии приложений](service-fabric-application-scenarios.md)

<!---HONumber=AcomDC_0224_2016-->