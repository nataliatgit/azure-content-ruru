<properties 
	pageTitle="Перемещение данных из локальной системы HDFS | Фабрика данных Azure" 
	description="Узнайте, как перемещать данные из локальной системы HDFS с помощью фабрики данных Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/19/2016" 
	ms.author="spelluru"/>

# Перемещение данных из локальной системы HDFS с помощью фабрики данных Azure
В этой статье описано использование действия копирования в фабрике данных Azure для перемещения данных из локальной системы HDFS в другое хранилище данных. Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

Сейчас фабрика данных поддерживает перемещение данных из локальной системы HDFS в другие хранилища данных, но не наоборот.


## Включение соединения
Служба фабрики данных поддерживает подключение к локальной системе HDFS с помощью шлюза управления данными. В статье [Перемещение данных между локальными и облачными ресурсами](data-factory-move-data-between-onprem-and-cloud.md) приведены сведения о шлюзе управления данными и пошаговые инструкции по его настройке. Шлюз используется для подключения к HDFS, даже если он размещен на виртуальных машинах Azure IaaS.

Шлюз можно установить на тот же локальный компьютер или виртуальную машину Azure, где находится HDFS, однако мы рекомендуем устанавливать шлюз на отдельный компьютер или отдельную виртуальную машину Azure IaaS — это позволит уменьшить число конфликтов и повысит производительность. При установке шлюза на отдельный компьютер у компьютера должен быть доступ к машине с HDFS.

## Пример копирования данных из локальной системы HDFS в хранилище BLOB-объектов Azure

Из этого примера вы узнаете, как копировать данные из локальной файловой системы HDFS в хранилище BLOB-объектов Azure. Однако данные можно **напрямую** копировать в любой указанный [здесь](data-factory-data-movement-activities.md#supported-data-stores) приемник. Это делается с помощью действия копирования в фабрике данных Azure.
 
Образец состоит из следующих сущностей фабрики данных.

1.	Связанная служба типа [OnPremisesHdfs](#hdfs-linked-service-properties).
2.	Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Входной [набор данных](data-factory-create-datasets.md) типа [FileShare](#hdfs-dataset-type-properties).
4.	Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	[Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [FileSystemSource](#hdfs-copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

В примере данные из результата выполнения запроса к локальной системе HDFS каждый час копируются в BLOB-объект. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

Сначала настройте шлюз управления данными. Инструкции, как это сделать, см. в статье [Перемещение данных между локальными источниками и облаком при помощи шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md).

**Связанная служба HDFS**. В этом примере используется проверка подлинности Windows. Сведения о различных типах проверки подлинности, которые можно использовать, см. в разделе [Свойства связанной службы HDFS](#hdfs-linked-service-properties).

	{
	    "name": "HDFSLinkedService",
	    "properties":
	    {
	        "type": "Hdfs",
	        "typeProperties":
	        {
	            "authenticationType": "Windows",
	            "userName": "Administrator",
	            "password": "password",
	            "url" : "http://<machine>:50070/webhdfs/v1/",
	            "gatewayName": "mygateway"
	        }
	    }
	}

**Связанная служба хранения Azure**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Входной набор данных HDFS**. Этот набор данных ссылается на папку в системе HDFS DataTransfer/UnitTest/. Конвейер копирует все файлы из этой папки в место назначения.

Если для параметра external задать значение true и указать политику externalData (необязательно), фабрика данных будет считать эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.
	
	{
	    "name": "InputDataset",
	    "properties": {
	        "type": "FileShare",
	        "linkedServiceName": "HDFSLinkedService",
	        "typeProperties": {
	            "folderPath": "DataTransfer/UnitTest/"
	        },
	        "external": true,
	        "availability": {
	            "frequency": "Hour",
	            "interval":  1
	        }
	    }
	}




**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

	{
	    "name": "OutputDataset",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	            "format": {
	                "type": "TextFormat",
	                "rowDelimiter": "\n",
	                "columnDelimiter": "\t"
	            },
	            "partitionedBy": [
	                {
	                    "name": "Year",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "yyyy"
	                    }
	                },
	                {
	                    "name": "Month",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%M"
	                    }
	                },
	                {
	                    "name": "Day",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%d"
	                    }
	                },
	                {
	                    "name": "Hour",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%H"
	                    }
	                }
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}



**Конвейер с действием копирования**

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **FileSystemSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, указанный для свойства **query**, выбирает для копирования данные за последний час.
	
	{
	    "name": "pipeline",
	    "properties":
	    {
	        "activities":
	        [
	            {
	                "name": "HdfsToBlobCopy",
	                "inputs": [ {"name": "InputDataset"} ],
	                "outputs": [ {"name": "OutputDataset"} ],
	                "type": "Copy",
	                "typeProperties":
	                {
	                    "source":
	                    {
	                        "type": "FileSystemSource"
	                    },
	                    "sink":
	                    {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy":
	                {
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1,
	                    "timeout": "00:05:00"
	                }
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## Свойства связанной службы HDFS

В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе HDFS.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- | 
| type | Для свойства type необходимо задать значение **Hdfs**. | Да | 
| URL-адрес | URL-адрес в HDFS | Да |
| encryptedCredential | Командлет [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) выводит учетные данные доступа. | Нет |
| userName | Имя пользователя для проверки подлинности Windows. | Да (для проверки подлинности Windows)
| пароль | Пароль для проверки подлинности Windows. | Да (для проверки подлинности Windows)
| authenticationType | Windows или анонимная. | Да |
| gatewayName | Имя шлюза, который следует использовать службе фабрики данных для подключения к локальной системе HDFS. | Да |   

Дополнительные сведения о настройке учетных данных для локальной файловой системы HDFS см. в разделе [Настройка учетных данных и безопасность](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security).

### Использовать анонимную проверку подлинности

	{
	    "name": "hdfs",
	    "properties":
	    {
	        "type": "Hdfs",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "userName": "hadoop",
	            "url" : "http://<machine>:50070/webhdfs/v1/",
	            "gatewayName": "mygateway"
	        }
	    }
	}


### Использовать проверку подлинности Windows
	
	{
	    "name": "hdfs",
	    "properties":
	    {
	        "type": "Hdfs",
	        "typeProperties":
	        {
	            "authenticationType": "Windows",
	            "userName": "Administrator",
	            "password": "password",
	            "url" : "http://<machine>:50070/webhdfs/v1/",
	            "gatewayName": "mygateway"
	        }
	    }
	}
 


## Свойства типа "Набор данных HDFS"

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, BLOB-объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** во всех типах наборов данных разный. В нем содержится информация о расположении данных в хранилище данных. Раздел typeProperties набора данных с типом **FileShare** (который включает набор данных HDFS) имеет следующие свойства.

Свойство | Описание | Обязательно
-------- | ----------- | --------
folderPath | Путь к папке, например myfolder.<p>Чтобы указать специальные символы в строке, используйте escape-символ \\. Например: для пути folder\\subfolder укажите folder\\\subfolder, а для пути d:\\samplefolder укажите d:\\\samplefolder.</p><p>Чтобы получить пути с учетом даты и времени начала и окончания среза, используйте **partitionBy**.</p> | Да
fileName | Укажите имя файла в папке **folderPath**, если таблица должна ссылаться на определенный файл в папке. Если этому свойству не присвоить значение, таблица будет указывать на все файлы в папке.<p>Если свойство fileName не указано для выходного набора данных, имя созданного файла будет иметь следующий формат: </p><p>Data.<Guid>.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.</p> | Нет
partitionedBy | Чтобы указать для временного ряда данных динамические путь к папке и имя файла, используйте свойство partitionedBy. Например, путь к папке (folderPath) каждый час будет другим. | Нет
fileFilter | Укажите фильтр для выбора подмножества файлов из folderPath. Фильтр дает возможность выбирать только некоторые файлы, а не все. <p>Допустимые значения: * (несколько символов) и ? (один символ).</p><p>Пример 1: "fileFilter": "*.log"</p>Пример 2: "fileFilter": 2014-1-?.txt"</p><p>**Примечание**. Свойство fileFilter применяется ко входному набору данных FileShare.</p> | Нет
| compression | Укажите тип и уровень сжатия данных. Поддерживаемые типы: **GZip**, **Deflate** и **BZip2**. Поддерживаемые уровни: **Optimal** и **Fastest**. Обратите внимание, что в данный момент параметры сжатия для данных в формате **AvroFormat** не поддерживаются. Дополнительные сведения см. в разделе [Поддержка сжатия](#compression-support). | Нет |
| format | Поддерживаются два типа форматов: **TextFormat** и **AvroFormat**. Свойству type в разделе format необходимо присвоить одно из этих значений. Если для свойства Format указано значение TextFormat, для формата можно указать дополнительные свойства. Дополнительную информацию см. в разделе [Определение TextFormat](#specifying-textformat) ниже. | Нет


> [AZURE.NOTE] Свойства filename и fileFilter нельзя использовать одновременно.


### Использование свойства partionedBy

Как сказано выше, для временных рядов данных путь к папке и имя файла можно указывать динамически. Это делается с помощью свойства partitionedBy. Вы можете это сделать при помощи макроса фабрики данных и системных переменных SliceStart и SliceEnd, которые определяют логический период имеющегося среза данных.

Дополнительные сведения о наборах данных временных рядов, планировании и срезах см. в статьях [Наборы данных](data-factory-create-datasets.md), [Планирование и исполнение с использованием фабрики данных](data-factory-scheduling-and-execution.md) и [Основные сведения о конвейерах и действиях](data-factory-create-pipelines.md).

#### Пример 1

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy": 
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

В примере выше {Slice} заменяется значением SliceStart (системная переменная фабрики данных) в формате YYYYMMDDHH. SliceStart указывает время начала среза. Значение folderPath отличается для каждого среза. Например: wikidatagateway/wikisampledataout/2014100103 или wikidatagateway/wikisampledataout/2014100104.

#### Пример 2

	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	"fileName": "{Hour}.csv",
	"partitionedBy": 
	 [
	    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
	    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
	    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
	],

В примере выше год, месяц, день и время SliceStart извлекаются в отдельные переменные, используемые в свойствах folderPath и fileName.

### Определение TextFormat

Если для свойства format задано значение **TextFormat**, в разделе **Format** вы можете указать следующие **необязательные** свойства.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| columnDelimiter | Символ, используемый в качестве разделителя столбцов в файле. В настоящее время допускается только один символ. Этот тег является необязательным. Значение по умолчанию — запятая (,). | Нет |
| rowDelimiter | Символ, используемый в качестве необработанного разделителя в файле. В настоящее время допускается только один символ. Этот тег является необязательным. По умолчанию используется одно из следующих значений: [\\r\\n, \\r, \\n]. | Нет |
| escapeChar | <p>Специальный символ, используемый для экранирования разделителя столбцов в содержимом. Этот тег является необязательным. Значение по умолчанию отсутствует. Для этого свойства следует указывать не более одного символа.</p><p>Например, если в качестве разделителя столбцов используется запятая и в тексте встречается этот символ (например: Hello, world), назначьте $ escape-символом и используйте в исходном коде строку Hello$, world.</p><p>Обратите внимание, что для таблицы нельзя одновременно указывать свойства escapeChar и quoteChar.</p> | Нет | 
| quoteChar | <p>Специальный символ, который используется для заключения строкового значения в кавычки. Разделители столбцов и строк внутри кавычек будут рассматриваться как часть строкового значения. Этот тег является необязательным. Значение по умолчанию отсутствует. Для этого свойства следует указывать не более одного символа.</p><p>Например, если в качестве разделителя столбцов используется запятая (,) и нужно, чтобы этот символ встречался в тексте (например, <Hello  world>), назначьте в качестве символа кавычек прямые кавычки (") и используйте в исходном коде строку <"Hello, world">. Это свойство применимо ко входным и выходным таблицам.</p><p>Обратите внимание, что для таблицы невозможно одновременно указывать свойства escapeChar и quoteChar.</p> | Нет |
| nullValue | <p>Символы, используемые для обозначения нулевого значения в содержимом файла большого двоичного объекта. Этот тег является необязательным. Значение по умолчанию — \\N.</p><p>Например, значение NaN (на основе приведенного выше примера) в большом двоичном объекте будет преобразовано в нулевое значение при копировании, например на сервер SQL Server.</p> | Нет |
| encodingName | Имя кодировки. Список допустимых имен кодировок см. в описании свойства [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Например: windows-1250 или shift\_jis. По умолчанию используется значение UTF-8. | Нет | 

#### Примеры
В следующем примере показаны некоторые свойства формата для TextFormat.

	"typeProperties":
	{
	    "folderPath": "mycontainer/myfolder",
	    "fileName": "myblobname"
	    "format":
	    {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "quoteChar": """,
	        "NullValue": "NaN"
	    }
	},

Чтобы использовать escapeChar вместо quoteChar, вместо строки с quoteChar укажите следующее:

	"escapeChar": "$",

### Определение AvroFormat
Если для свойства format выбрано значение AvroFormat, вам не нужно указывать какие-либо свойства в подразделе Format раздела typeProperties. Пример:

	"format":
	{
	    "type": "AvroFormat",
	}

Сведения об использовании формата Avro в таблице Hive см. в [учебнике по Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## Свойства типа "Действие копирования HDFS"

Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Такие свойства, как имя, описание, входные и выходные таблицы, различные политики и т. д., доступны для всех типов действий.

То, какие свойства будут доступны в разделе typeProperties, зависит от типа действия, а в случае с действием копирования — еще и от типов источников и приемников.

В случае действия копирования, когда источник относится к типу **FileSystemSource**, в разделе typeProperties доступны указанные ниже свойства.

**FileSystemSource** поддерживает следующие свойства:

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. | True, False (по умолчанию)| Нет |

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

<!---HONumber=AcomDC_0224_2016-->