<properties
   pageTitle="Настройка устойчивости и восстановления Elasticsearch в Azure"
   description="Вопросы, связанные с устойчивостью и восстановлением Elasticsearch."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Настройка устойчивости и восстановления Elasticsearch в Azure

Этот материал входит в [цикл статей, посвященных вопросам Elasticsearch](guidance-elasticsearch.md).

Одна из ключевых особенностей Elasticsearch — поддержка устойчивости при сбое узлов и/или нарушении связности сети. Репликация — самый очевидный способ повышения устойчивости кластера. Таким образом Elasticsearch обеспечивает доступность нескольких копий любых элементов данных в случае недоступности одного из узлов. Если узел становится временно недоступным, другие узлы, содержащие реплики данных отсутствующего узла, могут обрабатывать отсутствующие данные до разрешения проблемы. Если отсутствующий узел недоступен в течение более длительного периода, его можно заменить новым, а Elasticsearch может восстановить данные на новый узел, используя реплики.

В этой статье представлены общие сведения о вариантах обеспечения устойчивости и восстановления в Elasticsearch при размещении в Azure и описаны некоторые важные аспекты кластера Elasticsearch, которые следует учитывать, чтобы свести к минимуму вероятность потери данных и увеличения времени их восстановления.

Здесь также приведены некоторые примеры тестов, которые позволяют продемонстрировать влияние различных сбоев на кластер Elasticsearch и поведение системы при восстановлении.

Кластер Elasticsearch использует реплики для обеспечения доступности и повышения производительности операций чтения. Реплики следует хранить на виртуальных машинах, отличных от основных сегментов, которые они реплицируют. Таким образом, если на виртуальной машине, где размещен узел данных, произойдет сбой или она станет недоступной, система сможет продолжать работу, используя виртуальные машины с репликами.

## Использование выделенного главного узла

Один узел в кластере Elasticsearch назначается в качестве главного. Этот узел предназначен для выполнения операций управления кластером:

- обнаружение узлов, вышедших из строя, и переключение на реплики;

- перемещение сегментов для распределения рабочей нагрузки узла;

- восстановление сегментов при возобновлении работы узла.

Рекомендуется использовать выделенные главные узлы в критически важных кластерах. 3 выделенных узла должны выполнять роль главного узла. Такая конфигурация позволяет уменьшить объем ресурсоемких операций, которые выполняют эти узлы (они не хранят данные и не обрабатывают запросы) и улучшить стабильность кластера. Только один из этих узлов будет назначен в качестве главного. Остальные будут содержать копию состояния системы и могут выполнять функции назначенного главного узла при его сбое.

## Управление высокой доступностью в Azure. Домены обновления и домены сбоя 

Разные виртуальные машины могут совместно использовать одно физическое оборудование. В центре обработки данных Azure в одной стойке могут размещаться несколько виртуальных машин. Все они используют общий источник питания и сетевой коммутатор. Таким образом, сбой одной виртуальной машины в стойке может повлиять на несколько виртуальных машин. Поэтому в Azure используется концепция доменов сбоя для распределения этого риска. Домен сбоя примерно соответствует группе виртуальных машин в одной стойке. Чтобы убедиться, что сбой одной виртуальной машины в стойке не приведет к сбою узла и узлов, содержащих все реплики этого узла, следует распределить виртуальные машины по доменам сбоя.

Аналогичным образом [контроллер структуры Azure](https://azure.microsoft.com/documentation/videos/fabric-controller-internals-building-and-updating-high-availability-apps/) отключает виртуальные машины для планового обслуживания и обновления операционной системы. Azure выделяет виртуальные машины доменам обновления. При плановом обслуживании в любой момент времени используются виртуальные машины только в одном домене обновления. Виртуальные машины в других доменах обновления продолжают работать, пока не возобновится работа обновляемых виртуальных машин в одном домене обновления. Поэтому по возможности необходимо выделить виртуальные машины, содержащие узлы и реплики узлов, разным доменам обновления.

> [AZURE.NOTE] Дополнительные сведения о доменах сбоя и доменах обновления см. в статье [Управление доступностью виртуальных машин][].

Невозможно явно выделить виртуальную машину конкретному домену обновления или домену сбоя. Этим управляет Azure при создании виртуальных машин. Дополнительные сведения см. в статье [Управление доступностью виртуальных машин][]. Тем не менее виртуальные машины можно создавать в составе группы доступности. Виртуальные машины в одной группе доступности распределяются по доменам обновления и сбоя. При создании виртуальных машин вручную Azure свяжет каждую группу доступности с двумя доменами сбоя и пятью доменами обновления. Виртуальные машины будут выделяться этим доменам сбоя и доменам обновления и циклически распределятся по мере подготовки дополнительных виртуальных машин. Вот как это происходит:

- Первая виртуальная машина, подготовленная в группе доступности, помещается в первый домен сбоя (ДС 0) и первый домен обновления (ДО 0).
- Вторая виртуальная машина, подготовленная в группе доступности, помещается в ДС 1 и ДО 1.
- Третья виртуальная машина, подготовленная в группе доступности, помещается в ДС 0 и ДО 2. Четвертая виртуальная машина, подготовленная в группе доступности, помещается в ДС 1 и ДО 3.
- Пятая виртуальная машина, подготовленная в группе доступности, помещается в ДС 0 и ДО 4.
- Шестая виртуальная машина, подготовленная в группе доступности, помещается в ДС 1 и ДО 0.
- Седьмая виртуальная машина, подготовленная в группе доступности, помещается в ДС 0 и ДО 1.

> [AZURE.IMPORTANT] При создании виртуальных машин с помощью диспетчера ресурсов Azure на каждую группу доступности можно выделить до 3 доменов сбоя и 20 доменов обновления. Именно поэтому следует использовать диспетчер ресурсов Azure.

В общем, нужно размещать все виртуальные машины, выполняющие одну функцию, в одну группу доступности и создавать разные группы доступности для виртуальных машин, выполняющих разные функции. Это означает, что для кластера Elasticsearch следует создать отдельные группы доступности по крайней мере для таких виртуальных машин:

- виртуальных машин, содержащих узлы данных;
- виртуальных машин, содержащих клиентские узлы (в случае их использования);
- виртуальных машин, содержащих главные узлы.

Кроме того, следует убедиться, что каждый узел в кластере содержит информацию о домене обновления и домене сбоя, к которым он принадлежит. Эта информация позволяет убедиться, что Elasticsearch не создает сегменты и их реплики в одних и тех же доменах сбоя и доменах обновления. Таким образом вероятность одновременного сбоя сегмента и его реплик сводится к минимуму. Для узла Elasticsearch можно настроить отражение распределения оборудования кластера, настроив [сведения о выделении сегментов](https://www.elastic.co/guide/en/elasticsearch/reference/current/allocation-awareness.html#allocation-awareness). Например, можно определить пару пользовательских атрибутов узла *faultDomain* и *updateDomain* в файле elasticsearch.yml:

```yaml
node.faultDomain: \${FAULTDOMAIN}
node.updateDomain: \${UPDATEDOMAIN}
```

В этом случае атрибуты задаются при запуске Elasticsearch с помощью значений, содержащихся в переменных среды *\\${FAULTDOMAIN}* и *\\${UPDATEDOMAIN}*. Кроме того, в файл Elasticsearch.yml необходимо добавить следующие данные, чтобы указать, что *faultDomain* и *updateDomain* являются атрибутами с информацией о выделении, и задать набор допустимых значений для этих атрибутов:

```yaml
cluster.routing.allocation.awareness.force.updateDomain.values: 0,1,2,3,4
cluster.routing.allocation.awareness.force.faultDomain.values: 0,1
cluster.routing.allocation.awareness.attributes: updateDomain, faultDomain
```

Информацию о выделении сегментов можно использовать в сочетании с [фильтрацией выделения сегментов](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/shard-allocation-filtering.html#shard-allocation-filtering), чтобы явно указать, в каких узлах могут размещаться сегменты для любого заданного индекса.

Если необходимо использовать большее количество доменов сбоя и доменов обновления в группе доступности, можно создать виртуальные машины в дополнительных группах доступности. Тем не менее следует учитывать, что с целью обслуживания узлы в разных группах доступности могут отключаться одновременно. Убедитесь, что каждый сегмент и по крайней мере одна из его реплик находятся в одной группе доступности.

> [AZURE.NOTE] Сейчас количество виртуальных машин ограничено 100 виртуальными машинами на группу доступности. Дополнительные сведения см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md).

### Архивация и восстановление

Использование реплик не обеспечивает полную защиту от масштабного сбоя (например, при случайном удалении всего кластера). Регулярно выполняйте резервное копирование данных в кластере. Кроме того, вам понадобится проверенная стратегия по восстановлению системы с помощью этих резервных копий.

Для резервного копирования и восстановления индексов используйте [API моментальных снимков и восстановления](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html) Elasticsearch. Моментальные снимки можно сохранять в общей файловой системе. Кроме того, можно использовать подключаемые модули, которые могут записывать моментальные снимки в HDFS ([подключаемый модуль HDFS](https://github.com/elasticsearch/elasticsearch-hadoop/tree/master/repository-hdfs)) или в службу хранилища Azure ([облачный подключаемый модуль Azure](https://github.com/elasticsearch/elasticsearch-cloud-azure#azure-repository)).

При выборе механизма хранения моментальных снимков следует учитывать следующее:

- Можно использовать [хранилище файлов Azure](https://azure.microsoft.com/services/storage/files/) для внедрения общей файловой системы, доступной из всех узлов.

- Подключаемый модуль HDFS следует использовать, только если Elasticsearch используется вместе с Hadoop.

- При использовании подключаемого модуля HDFS необходимо отключить диспетчер безопасности Java, выполняемый в экземпляре Elasticsearch на виртуальной машине Java.

- Подключаемый модуль HDFS поддерживает любую файловую систему, совместимую с HDFS, при условии, что с Elasticsearch используется Hadoop, настроенный соответствующим образом.

  
## Устранение кратковременных сбоев подключения между узлами

Кратковременные сетевые сбои, перезагрузка виртуальных машин в центре обработки данных после планового обслуживания и другие аналогичные события могут привести к временной недоступности узлов. Так как такие события, скорее всего, имеют краткосрочный характер, нагрузка из-за перераспределения сегментов увеличивается несколько раз подряд (при обнаружении сбоя и когда узел становится доступным для главного узла), что может повлиять на производительность. Временную недоступность узлов, из-за которой главный узел перераспределяет ресурсы кластера, можно предотвратить, задав для одного индекса или всех индексов свойство *delayed\_timeout*. В приведенном ниже примере для задержки задано значение 5 минут:

```http
PUT /_all/settings
{
	"settings": {
    "index.unassigned.node_left.delayed_timeout": "5m"
	}
}
```

Дополнительные сведения см. в статье [Delaying allocation when a node leaves](https://www.elastic.co/guide/en/elasticsearch/reference/current/delayed-allocation.html) (Задержка распределения при отключении узла).

В сети, подверженной перерывам в работе, можно также изменить параметры, которые позволяют настроить главный узел таким образом, чтобы он определял, когда другой узел становится недоступным. Эти параметры содержатся в модуле [Zen Discovery](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html#modules-discovery-zen), поставляемом с Elasticsearch. Их можно задать в файле Elasticsearch.yml. Например, параметр *discovery.zen.fd.ping.retries* указывает количество попыток проверки связи главного узла с другим узлом в кластере, пока другой узел не будет определен как такой, в котором произошел сбой. По умолчанию для этого параметра задается значение 3, но его можно изменить следующим образом:

```yaml
discovery.zen.fd.ping_retries: 6
```

## Управление восстановлением

При восстановлении подключения к узлу после сбоя нужно восстановить все сегменты в этом узле, чтобы обновить их. По умолчанию Elasticsearch восстанавливает сегменты в следующем порядке:

- По дате создания индекса в обратном порядке. Сначала восстанавливаются новые индексы, а затем — старые индексы.

- По имени индекса в обратном порядке. Сначала восстанавливаются индексы, имена которых содержат буквы и цифры с большим приоритетом.

Если некоторые индексы гораздо важнее других, но не соответствуют этим критериям, приоритет индексов можно переопределить, задав свойство *index.priority*. Сначала будут восстановлены индексы с более высоким значением этого свойства, а затем — индексы с более низким значением:

```http
PUT low_priority_index
{
	"settings": {
		"index.priority": 1
	}
}

PUT high_priority_index
{
	"settings": {
		"index.priority": 10
	}
}
```

Дополнительные сведения см. в статье [Index Recovery Prioritization](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/recovery-prioritization.html#recovery-prioritization) (Приоритет восстановления индексов).

Можно отслеживать восстановление одного или нескольких индексов с помощью API *\_recovery*:

```http
GET /high_priority_index/_recovery?pretty=true
```

Дополнительные сведения см. в статье [Indices Recovery](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-recovery.html#indices-recovery) (Восстановление индексов).

> [AZURE.NOTE] Кластер с сегментами, требующими восстановления, будет помечен *желтым* цветом, чтобы указать, что в настоящее время доступны не все сегменты. Если доступны все сегменты, цвет кластера изменится на *зеленый*. Если кластер помечен *красным* цветом, это означает, что один или несколько сегментов отсутствуют. Возможно, следует восстановить данные из резервной копии.

## Предотвращение разделения вычислительных мощностей 

Разделение вычислительных мощностей может возникнуть при сбое подключения между узлами. Если главный узел становится недоступным в части кластера, в доступном сегменте сети будет выделен другой узел, который станет главным. При неправильной настройке кластера каждой части кластера могут быть выделены разные главные узлы, что приводит к несогласованности или повреждению данных. Это явление также называется *разделением вычислительных мощностей*.

Вероятность этого события можно уменьшить, настроив свойство модуля обнаружения *minimum\_master\_nodes* в файле elasticsearch.yml. Это свойство определяет, сколько узлов должно быть доступно для выделения главного узла. В следующем примере для этого свойства указано значение 2:

```yaml
discovery.zen.minimum_master_nodes: 2
```

Это значение должно соответствовать самому меньшему количеству узлов, которые могут выполнять роль главного узла. Например, если кластер содержит 3 главных узла, для свойства *minimum\_master\_nodes* нужно указать значение 2. Если кластер содержит 5 главных узлов, для свойства *minimum\_master\_nodes* следует указать значение 3. В идеале число главных узлов должно быть нечетным.

**Примечание**. Разделение вычислительных мощностей может произойти при одновременном запуске нескольких главных узлов в одном кластере. Хотя это происходит редко, разделение вычислительных мощностей можно предотвратить, запуская узлы последовательно и с небольшой задержкой (5 секунд).

## Управление последовательными обновлениями

При обновлении программного обеспечения узлов вручную (например, при переходе на более новую версию или применении исправления) может потребоваться выполнять действия с узлами по отдельности. При этом необходимо перевести их в автономный режим и сохранить доступность оставшейся части кластера. Для этого рекомендуется применить процедуру, описанную ниже.

Убедитесь, что для повторного выделения сегментов указано соответствующее значение задержки. Таким образом выделенный главный узел не будет перераспределять сегменты из отсутствующего узла по оставшейся части кластера. По умолчанию повторное выделение сегментов выполняется через 1 минуту. Однако это значение можно увеличить, если узел может быть недоступен в течение более длительного периода. В следующем примере значение задержки увеличено до 5 минут:

```http
PUT /_all/_settings
{
	"settings": {
		"index.unassigned.node_left.delayed_timeout": "5m"
	}
}
```

> [AZURE.IMPORTANT] Кроме того, можно полностью отключить повторное выделение сегментов, установив для свойства *cluster.routing.allocation.enable* значение *none*. Однако старайтесь не использовать этот метод, если во время пребывания узла в отключенном состоянии могут быть созданы новые индексы, так как это может привести к сбою выделения индекса, после чего он будет помечен красным цветом.

Остановите работу Elasticsearch на узле, для которого нужно выполнить обслуживание. Если Elasticsearch выполняется как служба, его работу можно остановить контролируемым образом с помощью команды операционной системы. В приведенном ниже примере показано, как остановить службу Elasticsearch на одном узле под управлением Ubuntu:

```bash
service elasticsearch stop
```

Кроме того, можно использовать API завершения работы непосредственно на узле:

```http
POST /_cluster/nodes/_local/_shutdown
```

Выполните необходимые операции по обслуживанию на узле, а затем перезапустите его и дождитесь, пока он будет добавлен в кластер.

Повторно включите выделение сегментов:

```http
PUT /_cluster/settings
{
	"transient": {
		"cluster.routing.allocation.enable": "all"
	}
}
```

> [AZURE.NOTE] Если необходимо выполнить обслуживание для нескольких узлов, повторите шаги 2, 3 и 4 для каждого узла, прежде чем включать выделение сегментов.

По возможности остановите индексацию новых данных при выполнении этого процесса. Это поможет свести к минимуму время восстановления при возобновлении работы узлов и их добавлении в кластер.

Учитывайте вероятность автоматического обновления элементов, например виртуальной машины Java (в идеале следует отключить автоматическое обновление этих элементов), особенно при работе с Elasticsearch под управлением Windows. Агент обновления Java может скачать последнюю версию Java автоматически. Однако чтобы обновления вступили в силу, может потребоваться перезапуск Elasticsearch. Это может привести к несогласованной временной потере узлов в зависимости от конфигурации агента обновления Java. Кроме того, в разных экземплярах Elasticsearch в одном кластере могут быть запущены разные версии виртуальных машин Java, что может вызвать проблемы совместимости.

## Тестирование и анализ устойчивости и восстановления Elasticsearch

В этом разделе описываются тесты, выполненные для оценки устойчивости и восстановления кластера Elasticsearch с тремя узлами данных и тремя главными узлами.

Проведены тесты для четырех разных сценариев.

1.  Сбой и перезапуск узла без потери данных. Работа узла данных останавливается, и в течение 5 минут он перезапускается. Elasticsearch настроен таким образом, чтобы в течение этого периода не выполнялось повторное выделение отсутствующих сегментов. Поэтому при перемещении сегментов не выполняются дополнительные операции ввода-вывода. При перезапуске узла в ходе восстановления его сегменты обновляются.

2.  Сбой узла с катастрофической потерей данных. Работа узла данных останавливается, а его данные удаляются для моделирования масштабного сбоя диска. Затем узел перезапускается (через 5 минут) и заменяет исходный узел. В этом случае требуется восстановить отсутствующие данные для этого узла, а также может потребоваться переместить сегменты на других узлах.

3.  Сбой и перезапуск узла без потери данных с повторным выделением сегментов. Работа узла данных останавливается, а его сегменты выделяются другим узлам. Затем узел перезапускается и выполняется повторное выделение для перераспределения ресурсов кластера.

4.  Последовательное обновление. Работа каждого узла в кластере останавливается, и через короткий промежуток времени он перезапускается. Это позволяет смоделировать перезагрузку компьютеров после обновления программного обеспечения. В любой момент времени останавливается работа только одного узла. Повторное выделение сегментов не выполняется, пока узел не работает.

В каждом сценарии при отключении и восстановлении узлов рабочая нагрузка, включающая задачи приема данных, объединение и запросы на фильтрацию, была одинаковой. При каждой операции массовой вставки в рабочей нагрузке передавалось по 1000 документов. Каждая из этих операций выполнялась с одним индексом. При объединении и выполнении запросов на фильтрацию использовался отдельный индекс, содержащий несколько миллионов документов. Это позволило отдельно оценить производительность запросов и операций массовой вставки. Каждый индекс включал в себя пять сегментов и одну реплику.

В следующих разделах приведены результаты этих тестов, а также указаны сведения о снижении производительности при отключении и восстановлении узла, а также возникших ошибках. Результаты представлены в графическом виде. Кроме этого, выделены моменты отключения одного или нескольких узлов и представлена оценка времени, необходимого системе для полного восстановления и достижения уровня производительности, аналогичного уровню до отключения узлов.

> [AZURE.NOTE] Средства и компоненты, использованные для выполнения этих тестов, доступны в Интернете. Их можно адаптировать и использовать для проверки устойчивости и восстановления собственных конфигураций кластера. Дополнительные сведения см. в разделе [Running the Automated Elasticsearch Resiliency Tests][] (Выполнение автоматических тестов устойчивости Elasticsearch).

## Сбой и перезапуск узла без потери данных. Результаты

<!-- TODO; reformat this pdf for display inline -->

Результаты этого теста содержатся в файле [ElasticsearchRecoveryScenario1.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario1.pdf). На графиках представлен профиль производительности рабочей нагрузки и физические ресурсы для каждого узла в кластере. В начальной части графиков показано, что система работает в обычном режиме около 20 минут, после чего узел 0 выключается на 5 минут перед повторным запуском. Затем представлена статистика для следующих 20 минут. Восстановление системы и стабилизация ее работы занимает около 10 минут. Эти видно в показателях скорости транзакций и времени отклика для разных рабочих нагрузок.

Обратите внимание на следующие моменты.

- Во время теста ошибки не обнаружены. Данные не утеряны, и все операции успешно завершены.

- Скорость транзакций для всех трех типов операций (операции массовой вставки, запросы на объединение и запросы на фильтрацию) упала, а среднее время отклика увеличилось, пока узел 0 был отключен.

- Во время восстановления скорость транзакций и время отклика запросов на объединение и фильтрацию постепенно восстановились. Прежде чем снизиться, производительность массовой вставки временно восстановилась. Тем не менее, скорее всего, это произошло из-за объема данных, который вызвал увеличение индекса, используемого при массовой вставке. Скорость транзакций для этой операции уменьшается еще до отключения узла 0.

- На графике загрузки ЦП для узла 0 наблюдается снижение активности на этапе восстановления. Это происходит из-за повышения активности диска и сети, вызванного механизмом восстановления. Узел должен получить все данные, добавленные, пока он был отключен, и обновить свои сегменты.

- Сегменты для индексов неравномерно распределяются по всем узлам. Два индекса содержат по 5 сегментов и 1 реплике. В общем получается 20 сегментов. Таким образом, два узла будут содержать по 6 сегментов, а другие два — по 7 сегментов. Это видно на графике загрузки ЦП в течение первых 20 минут: нагрузка узла 0 меньше, чем у остальных двух. По завершении восстановления происходит переключение, так как нагрузка узла 2 снижается.

    
## Сбой узла с катастрофической потерей данных. Результаты

<!-- TODO; reformat this pdf for display inline -->

Результаты этого теста содержатся в файле [ElasticsearchRecoveryScenario2.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario2.pdf). Как и в первом тесте, в начальной части графиков показано, что система работает в обычном режиме около 20 минут, после чего узел 0 выключается на 5 минут. В течение этого периода перед перезапуском данные Elasticsearch на этом узле удаляются, моделируя катастрофическую потерю данных. Полное восстановление производительности до уровня, наблюдавшегося до проведения теста, занимает 12–15 минут.

Обратите внимание на следующие моменты.

- Во время теста ошибки не обнаружены. Данные не утеряны, и все операции успешно завершены.

- Скорость транзакций для всех трех типов операций (операции массовой вставки, запросы на объединение и запросы на фильтрацию) упала, а среднее время отклика увеличилось, пока узел 0 был отключен. На этом этапе профиль производительности теста похож на профиль производительности в первом сценарии. Это неудивительно, так как до этого этапа сценарии абсолютно одинаковы.

- Во время восстановления скорость транзакций и время отклика восстановились, хотя в течение этого периода эти показатели сильно колебались. Скорее всего, это происходило из-за дополнительных операций, выполняемых узлами в кластере, чтобы предоставить данные для восстановления отсутствующих сегментов. Дополнительные операции четко видны на графиках загрузки ЦП, активности диска и сети.

- На графике загрузки ЦП для узлов 0 и 1 наблюдается ограничение активности на этапе восстановления. Это вызвано повышенной активностью диска и сети в ходе восстановления. В первом сценарии такое поведение проявлял только восстанавливаемый узел. Однако в этом сценарии, скорее всего, большая часть данных, отсутствующих на узле 0, восстанавливается из узла 1.

- По сравнению с первым сценарием для узла 0 наблюдается уменьшение активности ввода-вывода. Причиной может быть эффективное выполнение операций ввода-вывода: копируются данные всего сегмента, а не выполняются небольшие запросы ввода-вывода, необходимые для обновления существующего сегмента.

- При передаче и получении данных на всех трех узлах наблюдаются всплески активности сети. В сценарии 1 такой уровень активности наблюдался только на узле 0. Однако, судя по всему, этот уровень активности поддерживался в течение длительного периода. Опять же, такая разница могла возникнуть из-за эффективной передачи всех данных сегмента в рамках одного запроса, а не нескольких небольших запросов, полученных при восстановлении сегмента.

## Сбой и перезапуск узла с повторным выделением сегментов. Результаты

<!-- TODO; reformat this pdf for display inline -->

Результаты этого теста содержатся в файле [ElasticsearchRecoveryScenario3.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario3.pdf). Как и в первом тесте, в начальной части графиков показано, что система работает в обычном режиме около 20 минут, после чего узел 0 выключается на 5 минут. На этом этапе кластер Elasticsearch пытается повторно создать отсутствующие сегменты и перераспределить их между оставшимися узлами. По истечении 5 минут узел 0 включается и кластеру нужно снова перераспределить сегменты. Производительность восстанавливается до прежнего уровня в течение 12–15 минут.

Обратите внимание на следующие моменты.

- Во время теста ошибки не обнаружены. Данные не утеряны, и все операции успешно завершены.

- По сравнению с двумя предыдущими тестами скорость транзакций для всех трех типов операций (операции массовой вставки, запросы на объединение и запросы на фильтрацию) упала, а среднее время отклика существенно увеличилось, пока узел 0 был отключен. Это вызвано повышением активности кластера при повторном создании недостающих сегментов и перераспределении ресурсов кластера, о чем свидетельствуют увеличенные показатели активности диска и сети для узлов 1 и 2 за этот период.

- В течение этого периода после включения узла 0 показатели скорости транзакций и времени отклика колеблются.

- На графиках загрузки ЦП и активности диска для узла 0 видно, что активность во время восстановления существенно снижена. Это происходит, потому что на этом этапе узел 0 не обрабатывает данные. Спустя примерно 5 минут происходит всплеск активности узла, о чем свидетельствует резкое увеличение активности сети, дисков и ЦП. Скорее всего, это вызвано распределением сегментов между узлами кластера. Затем активность узла 0 стабилизируется.
  
## Последовательное обновление. Результаты

<!-- TODO; reformat this pdf for display inline -->

Результаты этого теста, содержащиеся в файле [ElasticsearchRecoveryScenario4.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario4.pdf), демонстрируют последовательное отключение и возобновление работы каждого узла. Каждый узел выключается на 5 минут до перезапуска, после чего выключается следующий узел.

Обратите внимание на следующие моменты.

- Хотя работа с каждым узлом выполняется циклически, производительность с точки зрения пропускной способности и времени отклика практически такая же.

- Активность диска временно увеличивается для каждого узла при включении. Скорее всего, это происходит из-за отката до изменений, произошедших, пока узел был отключен, в рамках восстановления.

- При отключении узла в оставшихся узлах наблюдаются пики сетевой активности. Они также наблюдаются при перезапуске узла.

- После перезапуска последнего узла показатели системы сильно колеблются. Скорее всего, это происходит из-за того, что в ходе восстановления нужно синхронизировать изменения на каждом узле и обеспечить согласованность всех реплик и соответствующих сегментов. В какой-то момент это приводит к тому, что время ожидания последовательных операций вставки истекает и они завершаются ошибкой. В каждом случае получено такое уведомление об ошибках:

```
Failure -- BulkDataInsertTest17(org.apache.jmeter.protocol.java.sampler.JUnitSampler$AnnotatedTestCase): java.lang.AssertionError: failure in bulk execution:
[1]: index [systwo], type [logs], id [AVEg0JwjRKxX_sVoNrte], message [UnavailableShardsException[[systwo][2] Primary shard is not active or isn't assigned to a known node. Timeout: [1m], request: org.elasticsearch.action.bulk.BulkShardRequest@787cc3cd]]

```

Последующие эксперименты показали, что если указать задержку в несколько минут между перезапуском и отключением каждого узла, эта ошибка устраняется. Поэтому она, вероятнее всего, вызвана конфликтом между процессом восстановления, в ходе которого восстанавливается несколько узлов одновременно, и операциями массовой вставки, при выполнении которых сохраняются тысячи новых документов.


## Сводка

На основе полученных результатов можно выделить такие основные моменты:

- Elasticsearch обеспечивает высокую надежность в отношении наиболее распространенных типов ошибок, которые могут произойти в кластере.

- Elasticsearch может быстро восстанавливаться при катастрофической потере данных на узле в правильно настроенном кластере. Это может произойти, если в Elasticsearch настроено сохранение данных во временное хранилище, а узел повторно подготавливается после перезагрузки. Эти результаты показывают, что даже в этом случае риски, связанные с использованием временного хранилища, скорее всего, значительно превосходят преимущества производительности, которые предоставляет хранилище такого типа.

- В первых трех сценариях при отключении и восстановлении узла в параллельных рабочих нагрузках операций массовой вставки, объединения и запросов на фильтрацию не произошло ошибок.

- Только в сценарии 4 обнаружена возможная потеря данных, которая затронула только новые добавляемые данные. В приложениях, выполняющих прием данных, рекомендуется предотвратить это событие, выполняя неудавшиеся операции вставки повторно, так как весьма вероятно, что такие ошибки будут временными.

- Результаты теста 4 также показывают, что при выполнении планового обслуживания узлов в кластере производительность увеличится, если перезапускать и выключать узлы через несколько минут. В случае внепланового обслуживания (например, повторный запуск узлов в центре обработки данных после обновления операционной системы) степень контроля над способом и временем отключения и перезапуска узлов гораздо меньше. Конфликты, которые возникают в Elasticsearch при попытке восстановления состояния кластера после последовательного отключения узлов, могут привести к истечению времени ожидания и ошибкам.

[Управление доступностью виртуальных машин]: ../articles/virtual-machines/virtual-machines-manage-availability.md
[Running the Automated Elasticsearch Resiliency Tests]: guidance-elasticsearch-running-automated-resilience-tests.md

<!---HONumber=AcomDC_0224_2016-->