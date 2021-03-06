<properties 
	pageTitle="Создание кластера Spark в HDInsight и использование запросов Spark SQL из Zeppelin и Jupyter для интерактивного анализа | Azure" 
	description="Пошаговые инструкции по быстрому созданию кластера Apache Spark в HDInsight и последующему использованию запросов Spark SQL из записных книжек Zeppelin и Jupyter для выполнения интерактивных запросов." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/22/2015" 
	ms.author="nitinme"/>


# Быстрый запуск. Создание кластера Apache Spark в HDInsight и выполнение интерактивных запросов с помощью SQL Spark (Windows)

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Создание Apache Spark в HDInsight и выполнение интерактивных запросов с помощью Spark SQL](hdinsight-apache-spark-jupyter-spark-sql.md)

Узнайте, как создать кластер Apache Spark в HDInsight с помощью параметра "Быстрое создание", а затем использовать записные веб-книжки [Zeppelin](https://zeppelin.incubator.apache.org) и [Jupyter](https://jupyter.org) для выполнения интерактивных запросов Spark SQL в кластере Spark.


   ![Приступая к работе с Apache Spark в HDInsight](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.getstartedflow.png "Руководство по началу работы с Apache Spark в HDInsight. Описание шагов: создание учетной записи хранения; создание кластера; выполнение инструкций Spark SQL")

**Предварительные требования:**

Прежде чем приступать к изучению этого учебника, необходимо оформить подписку Azure. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a name="storage"></a>Создание учетной записи хранения Azure

При создании кластера HDInsight в HDInsight нужно указать учетную запись хранения Azure. Определенный контейнер хранилища больших двоичных объектов из этой учетной записи назначается в качестве файловой системы по умолчанию. По умолчанию создание кластера HDInsight выполняется в том же центре обработки данных, в котором находится указанная учетная запись хранения. Дополнительную информацию см. в статье [Использование хранилища больших двоичных объектов Azure с HDInsight][hdinsight-storage].


**Создание учетной записи хранения Azure**

1. Войдите на [портал Azure][azure-management-portal].
2. Щелкните **СОЗДАТЬ** в левом нижнем углу и введите значения, как показано на рисунке.

	![Портал Azure, на котором можно использовать функцию быстрого создания для настройки новой учетной записи хранения](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.storageaccount.quickcreate.png "Портал Azure, на котором можно использовать функцию быстрого создания для настройки новой учетной записи хранения")

>[AZURE.NOTE]  Создавайте учетную запись хранения в том расположении, в котором поддерживается кластер,

Выберите новую учетную запись хранения в списке и щелкните **УПРАВЛЕНИЕ КЛЮЧАМИ ДОСТУПА** в нижней части страницы. Запишите значения в полях **ПЕРВИЧНЫЙ КЛЮЧ ДОСТУПА** (или **ВТОРИЧНЫЙ КЛЮЧ ДОСТУПА** —любой из ключей будет работать). Они потребуются позже в этом учебнике. Дополнительные сведения см. в статье [Создание учетной записи хранения][azure-create-storageaccount].
	
##Создание кластера HDInsight Spark

В этом разделе представлены сведения о создании кластера HDInsight версии 3.2 на базе Spark версии 1.3.1. Дополнительную информацию о различных версиях HDInsight и их соглашениях об уровне обслуживания см. в статье [Версии компонентов НDInsight](hdinsight-component-versioning.md).

>[AZURE.NOTE] В этой статье описаны действия по созданию кластера Apache Spark в HDInsight с использованием базовых параметров конфигурации. Сведения о других параметрах конфигурации кластера (например, относящихся к использованию дополнительного хранилища, виртуальной сети Azure или метахранилища для Hive) см. в статье [Создание кластеров HDInsight с использованием настраиваемых параметров](hdinsight-apache-spark-provision-clusters.md).


**Создание кластера Spark**

1. Войдите на [портал Azure][azure-management-portal]. 

2. Щелкните **СОЗДАТЬ** в левом нижнем углу и введите значения, как показано на рисунке.

	![Создать кластер Spark в HDInsight](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.quickcreatecluster.png "Создать кластер Spark в HDInsight")


##<a name="zeppelin"></a>Выполнение интерактивных запросов Spark SQL с помощью записной книжки Zeppelin

После создания кластера вы можете воспользоваться веб-записной книжкой Zeppelin для выполнения интерактивных запросов Spark SQL в кластер Spark HDInsight. В этом разделе мы используем файл с демонстрационными данными (hvac.csv), который по умолчанию доступен в кластере, для выполнения некоторых интерактивных запросов Spark SQL.

>[AZURE.NOTE] Записная книжка, которую вы создадите, следуя приведенным ниже инструкциям, также по умолчанию доступна в кластере. После запуска Zeppelin эту записную книжку вы сможете найти по имени **Zeppelin HVAC tutorial**.

1. В левой панели [портала][azure-management-portal] щелкните **HDInsight** и затем щелкните по созданному кластеру Spark. На странице кластера Spark в нижней панели щелкните **Zeppelin Notebook**. При появлении запроса введите учетные данные администратора для кластера.

	> [AZURE.NOTE] Также можно открыть Zeppelin Notebook для своего кластера, открыв следующий URL-адрес в браузере. Замените __CLUSTERNAME__ именем кластера:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Создайте новую записную книжку. На панели заголовка щелкните элемент **Notebook**, а затем — **Создать новую заметку**.

	![Создание новой записной книжки Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.createnewnote.png "Создание новой записной книжки Zeppelin")

	На той же странице под заголовком **Notebook** отобразится новая записная книжка с именем, начинающимся с **Заметка XXXXXXXXX**. Щелкните новую записную книжку.

3. На веб-странице для новой записной книжки щелкните заголовок и при необходимости переименуйте записную книжку. Нажмите клавишу ВВОД, чтобы сохранить изменения. Кроме того, убедитесь, что в правом верхнем углу в заголовке записной книжки отображается состояние **Подключено**.

	![Состояния записной книжки Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.newnote.connected.png "Состояния записной книжки Zeppelin")

4. Загрузите демонстрационные данные во временную таблицу. При создании кластера Spark в HDInsight файл с демонстрационными данными **hvac.csv** копируется в связанную учетную запись хранения по следующему пути: **\\HdiSamples\\SensorSampleData\\hvac**.

	В пустой абзац, созданный по умолчанию в новой записной книжке, вставьте следующий фрагмент кода.

		// Create an RDD using the default Spark context, sc
		val hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		// Define a schema
		case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
		
		// Map the values in the .csv file to the schema
		val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
    		s => Hvac(s(0), 
            		s(1),
            		s(2).toInt,
            		s(3).toInt,
            		s(6)
        	)
		).toDF()
		
		// Register as a temporary table called "hvac"
		hvac.registerTempTable("hvac")
		
	Нажмите клавиши **SHIFT + ВВОД** или кнопку **Воспроизвести** для абзаца, чтобы выполнить фрагмент кода. Состояние, которое отображается в правом верхнем углу абзаца, должно изменяться в следующей последовательности: READY (ГОТОВО), PENDING (ОЖИДАЕТ), RUNNING (ВЫПОЛНЯЕТСЯ) и FINISHED (ЗАВЕРШЕНО). Выходные данные отображаются в нижней части того же абзаца. Снимок экрана выглядит следующим образом:

	![Создание временной таблицы из необработанных данных](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.loaddataintotable.png "Создание временной таблицы из необработанных данных")

	Можно указать заголовок для каждого абзаца. В правом верхнем углу экрана щелкните значок **Параметры**, а затем щелкните элемент **Показать заголовок**.

5. Теперь вы можете выполнить инструкции Spark SQL для таблицы **hvac**. Вставьте следующий запрос в новый абзац. Запрос извлекает идентификатор здания и разницу между целевой и фактической температурами для каждого здания в указанный день. Нажмите **SHIFT + ВВОД**.

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date 
		from hvac
		where date = "6/1/13" 

	Инструкция **% sql** в начале сообщает записной книжке, что необходимо использовать интерпретатор Spark SQL. Просмотреть определенные интерпретаторы вы можете на вкладке **Интерпретатор** в заголовке записной книжки.

	Выходные данные показаны на снимке экрана ниже.

	![Выполнение инструкции Spark SQL с помощью записной книжки](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.sparksqlquery1.png "Выполнение инструкции Spark SQL с помощью записной книжки")

	 Используйте параметры отображения (выделены красным прямоугольником) для переключения между различными представлениями одних и тех же выходных данных. Щелкните элемент **Параметры**, чтобы определить ключи и значения в выходных данных. На снимке экрана выше в параметр **buildingID** используется в качестве ключа, а среднее значение **temp\_diff** — как значение.

	
6. Можно также запустить инструкции Spark SQL с помощью переменных в запросе. В следующем фрагменте кода показано, как определить переменную (**Temp**) в запросе с возможными значениями, с которыми необходимо выполнить запрос. При первом выполнении запроса раскрывающийся список автоматически заполняется значениями, указанными для переменной.

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}" 

	Вставьте этот фрагмент кода в новый абзац и нажмите клавиши **SHIFT + ВВОД**. Выходные данные показаны на снимке экрана ниже.

	![Выполнение инструкции Spark SQL с помощью записной книжки](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.sparksqlquery2.png "Выполнение инструкции Spark SQL с помощью записной книжки")

	Для последующих запросов можно выбрать новое значение из раскрывающегося списка и повторно выполнить запрос. Щелкните **Settings** (Параметры), чтобы определить ключ и значения в выходных данных. На снимке экрана выше параметр **buildingID** используется в качестве ключа, среднее значение **temp\_diff** используется как значение, а **targettemp** — как группа.

7. Перезапустите интерпретатор Spark SQL, чтобы выйти из приложения. Перейдите на вкладку **Интерпретатор** в верхней части окна, а затем для интерпретатора Spark нажмите кнопку **Перезапустить**.

	![Перезапуск интерпретатора Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.zeppelin.restart.interpreter.png "Перезапуск интерпретатора Zeppelin")

##<a name="jupyter"></a>Выполнение запросов Spark SQL с помощью записной книжки Jupyter

В этом разделе записная книжка Jupyter используется для выполнения запросов Spark SQL к кластеру Spark.

>[AZURE.NOTE] Записная книжка, которую вы создадите, следуя приведенным ниже инструкциям, также по умолчанию доступна в кластере. После запуска Jupyter эту записную книжку вы сможете найти по имени **HVACTutorial.ipynb**.

1. В левой панели [портала][azure-management-portal] щелкните **HDInsight** и затем щелкните по созданному кластеру Spark. На странице кластера Spark в нижней панели щелкните **Zeppelin Notebook**. При появлении запроса введите учетные данные администратора для кластера.

	> [AZURE.NOTE] Также можно открыть Jupyter Notebook для своего кластера, открыв следующий URL-адрес в браузере. Замените __CLUSTERNAME__ именем кластера.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Создайте новую записную книжку. Щелкните **Создать**, а затем выберите пункт **Python2**.

	![Создание новой записной книжки Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.jupyter.createnotebook.png "Создание новой записной книжки Jupyter")

3. Будет создана и открыта записная книжка с именем Untitled.pynb. Щелкните имя записной книжки в верхней части страницы сверху и введите понятное имя.

	![Указание имени для записной книжки](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.jupyter.notebook.name.png "Указание имени для записной книжки")

4. Импортируйте необходимые модули и создайте контексты Spark и SQL. Вставьте следующий фрагмент кода в пустую ячейку и нажмите **SHIFT + ВВОД**.

		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *

		# Create Spark and SQL contexts
		sc = SparkContext('spark://headnodehost:7077', 'pyspark')
		sqlContext = SQLContext(sc)

	При каждом запуске задания в Jupyter в заголовке окна веб-браузера будет отображаться состояние **(Занято)**, а также заголовок записной книжки. Кроме того, рядом с надписью **Python 2** в верхнем правом углу окна будет показан закрашенный кружок. После завершения задания этот значок изменится на кружок без заливки.

	 ![Состояние задания записной книжки Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.jupyter.job.status.png "Состояние задания записной книжки Jupyter")

4. Загрузите демонстрационные данные во временную таблицу. При создании кластера Spark в HDInsight файл с демонстрационными данными **hvac.csv** копируется в связанную учетную запись хранения по следующему пути: **\\HdiSamples\\SensorSampleData\\hvac**.

	Вставьте следующий фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**. Этот фрагмент кода регистрирует данные во временной таблице, которая называется **hvac**.


		# Load the data
		hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerAsTable("hvac")
		
		# Run queries against the table and display the data
		data = sqlContext.sql("select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"")
		data.show()

5. После успешного выполнения задания выводятся следующие данные:

		buildingID temp_diff date  
		4          8         6/1/13
		3          2         6/1/13
		7          -10       6/1/13
		12         3         6/1/13
		7          9         6/1/13
		7          5         6/1/13
		3          11        6/1/13
		8          -7        6/1/13
		17         14        6/1/13
		16         -3        6/1/13
		8          -8        6/1/13
		1          -1        6/1/13
		12         11        6/1/13
		3          14        6/1/13
		6          -4        6/1/13
		1          4         6/1/13
		19         4         6/1/13
		19         12        6/1/13
		9          -9        6/1/13
		15         -10       6/1/13

6. Перезапустите ядро, чтобы выйти из приложения. В меню в верхней части страницы щелкните элемент **Ядро**, выберите команду **Перезапустить** и снова нажмите кнопку **Перезапустить** в окне подтверждения действия.

	![Перезапуск ядра Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.jupyter.restart.kernel.png "Перезапуск ядра Jupyter")


##<a name="seealso"></a>См. также:


* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview-v1.md)
* [Создание Spark в кластере HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools-v1.md)
* [Создание приложений машинного обучения с помощью Spark в HDInsight](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [Использование Spark в HDInsight для создания приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager-v1.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0218_2016-->