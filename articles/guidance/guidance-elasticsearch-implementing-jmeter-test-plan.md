<properties
   pageTitle="Реализация плана тестирования JMeter для Elasticsearch | Microsoft Azure"
   description="Инструкции по выполнению тестов производительности для Elasticsearch с помощью JMeter."
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
   
# Реализация плана тестирования JMeter для Elasticsearch

Этот материал входит в [цикл статей](guidance-elasticsearch.md).

Тесты производительности в Elasticsearch были реализованы с использованием планов тестирования JMeter и кода Java, внедренного в форме теста JUnit для выполнения таких задач, как отправка данных в кластер. Планы тестирования и код JUnit описываются в документах [Настройка производительности приема данных для Elasticsearch в Azure][] и [Tuning Data Aggregation and Query Performance for Elasticsearch on Azure][].

В этом документе представлен ключевой опыт создания и применения таких планов тестирования. Более общие рекомендации по эффективному применению JMeter см. на странице [JMeter Best Practices](http://jmeter.apache.org/usermanual/best-practices.html) на веб-сайте Apache JMeter.

## Реализация плана тестирования JMeter

В следующем списке перечислены моменты, которые необходимо учитывать при создании плана тестирования JMeter:

- Создавайте отдельную группу потоков для каждого теста. Тест может состоять из нескольких этапов и включать логические контроллеры, таймеры, обработчики для предварительной и постобработки, дискретизаторы и прослушиватели.

- Старайтесь не создавать слишком много потоков в группе потоков. Чрезмерное число потоков может привести к сбою JMeter и исключениям, связанным с нехваткой памяти. Лучше добавить несколько подчиненных серверов JMeter, на каждом из которых будет выполняться небольшое число потоков, чем пытаться запускать большое число потоков на одном сервере JMeter.

![](./media/guidance-elasticsearch/jmeter-testing1.png)

- Чтобы оценить производительность кластера, включите в план тестирования подключаемый модуль [сборщика показателей Perfmon](http://jmeter-plugins.org/wiki/PerfMon/) — это прослушиватель JMeter, который входит в число стандартных подключаемых модулей JMeter. Сохраняйте необработанные данные как набор CSV-файлов и обрабатывайте их после завершения теста. Это более эффективно и создает меньше нагрузки на JMeter, чем попытка обрабатывать данные по мере их записи. 

Для импорта данных и формирования ряда графиков для аналитических целей можно использовать такой инструмент, как Excel.

![](./media/guidance-elasticsearch/jmeter-testing2.png)

Рекомендуется фиксировать следующие данные:

- Загрузка ЦП для каждого узла в кластере Elasticsearch.

- Количество байт, прочитанных с диска на каждом узле.

- Если возможно, доля процессорного времени, потраченного на ожидание ввода-вывода на каждом узле. Это не всегда возможно на виртуальных машинах Windows, но для Linux можно создать пользовательскую метрику (метрику EXEC), выполняющую следующую команду оболочки для вызова команды *vmstat* на узле:

```Shell
sh:-c:vmstat 1 5 | awk 'BEGIN { line=0;total=0;}{line=line+1;if(line&gt;1){total=total+\$16;}}END{print total/4}'
```

Поле 16 в выходных данных команды *vmstat* содержит процессорное время, затраченное на ожидание ввода-вывода. Дополнительные сведения о работе этой инструкции см. в статье о [команде vmstat](http://linuxcommand.org/man_pages/vmstat8.html).

- Количество байт, отправленных и полученных по сети на каждом узле.

- Для записи данных о производительности и частоте успешных и неудачных операций используйте отдельные прослушиватели статистического отчета. Записывайте данные об успешных и неудачных операциях в разные файлы.

![](./media/guidance-elasticsearch/jmeter-testing3.png)

- Старайтесь, чтобы каждый тестовый случай JMeter был максимально простым — это позволит вам соотносить производительность и конкретные действия во время теста. Если тестовый случай требует сложной логики, ее можно включить в тест JUnit и использовать для выполнения теста дискретизатора запросов JUnit в JMeter.

- Для выполнения таких HTTP-операций, как GET, POST, PUT и DELETE используйте дискретизатор HTTP-запросов. Например, для поиска в Elasticsearch можно использовать запрос POST, указав параметры запроса в поле *Body Data* (Данные основного текста):

![](./media/guidance-elasticsearch/jmeter-testing4.png)

- В целях воспроизводимости и повторного использования планы тестирования JMeter рекомендуется параметризовать. Впоследствии выполнение планов тестирования можно будет автоматизировать с помощью сценариев.

## Выполнение JUnit

Сложный код можно включить в план тестирования JMeter, создав один или несколько тестов JUnit. Тест JUnit можно написать, используя Java IDE, например Eclipse. В статье [Развертывание дискретизатора JUnit для JMeter при тестировании производительности Elasticsearch][] содержатся сведения о настройке соответствующей среды разработки.

Вот некоторые рекомендации по написанию кода для теста JUnit:

- Для передачи параметров инициализации теста используйте конструктор класса тестов. JMeter может использовать конструктор, который принимает один строковый аргумент. В конструкторе разберите этот аргумент на отдельные элементы, как показано в следующем примере кода:

```Java
private String hostName = "";
private String indexName = "";
private String typeName = "";
private int port = 0;
private String clusterName = "";
private int itemsPerBatch = 0;

/* JUnit test class constructor */
public ElasticsearchLoadTest2(String params) {
	/* params is a string containing a set of comma separated values for:
		hostName
		indexName
		typeName
		port
		clustername
		itemsPerBatch
	*/

    /* Parse the parameter string into an array of string items */
	String delims = "[ ]*,[ ]*"; // comma surrounded by zero or more spaces
	String[] items = params.split(delims);

    /* Note: Parameter validation code omitted */

	/* Use the parameters to populate variables used by the test */
	hostName = items[0];
	indexName = items[1];
	typeName = items[2];
	port = Integer.parseInt(items[3]);
	clusterName = items[4];
	itemsPerBatch = Integer.parseInt(items[5]);

	if(itemsPerBatch == 0)
		itemsPerBatch = 1000;
}
```

- Старайтесь не включать в класс тестов конструктора или настройки дорогостоящие операции или операции ввода-вывода, поскольку они будут выполняться вместе с каждым тестом JUnit, а в каждом тесте производительности, выполняемом из JMeter, один и тот же тест JUnit может выполняться тысячи раз.

- Для запуска дорогостоящих тестовых случаев можно использовать однократную настройку.

- Если для теста требуется много входных параметров, храните сведения о конфигурации теста в отдельном файле конфигурации и передавайте расположение этого файла в конструктор.

- Старайтесь не прописывать пути к файлам в коде теста нагрузки. Это может привести к сбоям из-за различий между операционными системами, например Windows и Linux.

- Используйте утверждения для указания на сбои в методах JUnit — так вы сможете отслеживать их в JMeter и использовать в качестве бизнес-метрик. По возможности возвращайте информацию о причинах сбоев, как показано в выделенной полужирным шрифтом части кода в следующем примере:

```Java
@Test
public void bulkInsertTest() throws IOException {
	...
	BulkResponse bulkResponse = bulkRequest.execute().actionGet();
	assertFalse(
		bulkResponse.buildFailureMessage(), bulkResponse.hasFailures());
		...
}
```

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Настройка производительности приема данных для Elasticsearch в Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Развертывание дискретизатора JUnit для JMeter при тестировании производительности Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Tuning Data Aggregation and Query Performance for Elasticsearch on Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md

<!---HONumber=AcomDC_0224_2016-->