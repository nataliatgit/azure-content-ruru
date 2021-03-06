<properties 
	pageTitle="Перемещение данных в большой двоичный объект Azure и из него | Фабрика данных Azure" 
	description="Узнайте, как переместить данные в хранилище больших двоичных объектов Azure и из него с помощью фабрики данных Azure." 
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
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# Перемещение данных в большой двоичный объект Azure и из него с помощью фабрики данных Azure
В этой статье рассказывается, как с помощью действия копирования в фабрике данных Azure можно перемещать данные в BLOB-объект Azure из другого хранилища данных и обратно. Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

В следующих примерах показано, как копировать данные в базу данных SQL Azure и хранилище BLOB-объектов Azure и обратно. Однако данные можно **напрямую** копировать из любых источников в любой указанный [здесь](data-factory-data-movement-activities.md#supported-data-stores) приемник. Это делается с помощью действия копирования в фабрике данных Azure.
 

## Пример копирования данных из BLOB-объекта Azure в SQL Azure
В примере ниже показано следующее.

1.	Связанная служба типа [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2.	Связанная служба типа [AzureStorage](#azure-storage-linked-service-properties).
3.	Входной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](#azure-blob-dataset-type-properties).
4.	Выходной [набор данных](data-factory-create-datasets.md) типа [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
4.	[Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [BlobSource](#azure-blob-copy-activity-type-properties) и [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties).

В этом примере данные, относящиеся к одному временному ряду, каждый час копируются из BLOB-объекта Azure в таблицу в базе данных SQL Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба SQL Azure**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Связанная служба хранилища Azure**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

Фабрика данных Azure поддерживает два типа связанных служб хранилища Azure: **AzureStorage** и **AzureStorageSas**. Для первой необходимо указать строку подключения, содержащую ключ учетной записи, а для второй — URI подписанного URL-адреса. Дополнительные сведения см. в разделе [Связанные службы](#linked-services).

**Входной набор данных BLOB-объекта Azure**

Данные берутся из нового BLOB-объекта каждый час (frequency: hour, interval: 1). Путь к папке с BLOB-объектом и имя файла вычисляются динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц и день начала, а в имени файла — час начала. Когда для параметра external задано значение true, служба фабрики данных считает эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
	      "fileName": "{Hour}.csv",
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
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Выходной набор данных SQL Azure**

В этом примере данные копируются в таблицу с именем MyOutputTable в базе данных SQL Azure. Таблицу в базе данных SQL Azure нужно создать с тем количеством столбцов, которое должно быть в CSV-файле BLOB-объекта. Новые строки добавляются в таблицу каждый час.

	{
	  "name": "AzureSqlOutput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Конвейер с действием копирования**

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **BlobSource**, а для типа **sink** — значение **SqlSink**.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQL",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}

## Пример копирования данных из SQL Azure в BLOB-объект Azure
В примере ниже показано следующее.

1.	Связанная служба типа [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2.	Связанная служба типа [AzureStorage](#azure-storage-linked-service-properties).
3.	Входной [набор данных](data-factory-create-datasets.md) типа [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
4.	Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](#azure-blob-dataset-type-properties).
4.	[Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [SqlSource](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties) и [BlobSink](#azure-blob-copy-activity-type-properties).


В этом примере данные, относящиеся к одному временному ряду, каждый час копируются из таблицы в базе данных SQL Azure в BLOB-объект. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба SQL Azure**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Связанная служба хранилища Azure**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

Фабрика данных Azure поддерживает два типа связанных служб хранилища Azure: **AzureStorage** и **AzureStorageSas**. Для первой необходимо указать строку подключения, содержащую ключ учетной записи, а для второй — URI подписанного URL-адреса. Дополнительные сведения см. в разделе [Связанные службы](#linked-services).


**Входной набор данных SQL Azure**

В примере предполагается, что таблица MyTable уже создана в SQL Azure и содержит столбец с именем timestampcolumn для данных временных рядов.

Если для параметра external задать значение true и указать политику externalData, фабрика данных будет считать эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}


**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

	
	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Конвейер с действием копирования**

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **SqlSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, указанный для свойства **SqlReaderQuery**, выбирает для копирования данные за последний час.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline for copy activity",
	    	"activities":[  
	      		{
	        		"name": "AzureSQLtoBlob",
		    	    "description": "copy activity",
		    	    "type": "Copy",
		    	    "inputs": [
		    	      {
		    	        "name": "AzureSQLInput"
		    	      }
		    	    ],
		    	    "outputs": [
		    	      {
		    	        "name": "AzureBlobOutput"
		    	      }
		    	    ],
		    	    "typeProperties": {
		    	    	"source": {
		            		"type": "SqlSource",
			            	"SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
		          		},
		          		"sink": {
		            		"type": "BlobSink"
		          		}
		        	},
		       		"scheduler": {
		          		"frequency": "Hour",
		          		"interval": 1
		        	},
		        	"policy": {
		          		"concurrency": 1,
		          		"executionPriorityOrder": "OldestFirst",
		          		"retry": 0,
		          		"timeout": "01:00:00"
		        	}
		      	}
		     ]
		}
	}

## Связанные службы
Для связи хранилища BLOB-объектов Azure с фабрикой данных Azure можно использовать два типа связанных служб: **AzureStorage** и **AzureStorageSas**. Связанная служба хранилища Azure предоставляет фабрике данных глобальный доступ к хранилищу Azure, а связанная служба SAS хранилища Azure — ограниченный или привязанный ко времени доступ к хранилищу Azure. Других различий между этими связанными службами нет. Выберите связанную службу, соответствующую вашим задачам. Более подробно эти службы описаны в следующих разделах.

[AZURE.INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## Свойства типа "Набор данных большого двоичного объекта Azure"

Полный список разделов и свойств JSON, используемых для определения наборов данных, см. в статье [Создание наборов данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, BLOB-объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** во всех типах наборов данных разный. В нем содержится информация о расположении данных в хранилище данных, формате и т. д. Раздел typeProperties набора данных с типом **AzureTable** имеет следующие свойства.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- | 
| folderPath | Путь контейнеру и папке в хранилище BLOB-объектов. Пример: myblobcontainer\\myblobfolder\\ | Да |
| fileName | <p>Имя большого двоичного объекта. Свойство fileName является необязательным и в нем учитывается регистр знаков.</p><p>Если указать имя файла, действие (включая копирование) будет выполняться в отношении определенного большого двоичного объекта.</p><p>Если свойство fileName не указано, копирование будет включать все большие двоичные объекты в свойстве folderPath для входного набора данных.</p><p>Если свойство fileName не указано для выходного набора данных, имя созданного файла будет иметь следующий формат: Data.<Guid>.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).</p> | Нет |
| partitionedBy | Необязательное свойство. Его можно использовать, чтобы указать динамические путь к папке и имя файла для временного ряда данных. Например, путь к папке (значение folderPath) каждый час может быть другим. Подробные сведения и примеры см. ниже в разделе [Использование свойства partitionedBy](#Leveraging-partitionedBy-property). | Нет
| format | Поддерживаются два типа форматов: **TextFormat** и **AvroFormat**. Свойству type в разделе format необходимо присвоить одно из этих значений. Если для свойства Format указано значение TextFormat, для формата можно указать дополнительные свойства. Дополнительную информацию см. в разделе [Определение TextFormat](#specifying-textformat) ниже. | Нет
| compression | Укажите тип и уровень сжатия данных. Поддерживаемые типы: **GZip**, **Deflate** и **BZip2**. Поддерживаемые уровни: **Optimal** и **Fastest**. Обратите внимание, что в данный момент параметры сжатия для данных в формате **AvroFormat** не поддерживаются. Дополнительные сведения см. в разделе [Поддержка сжатия](#compression-support). | Нет |

### Использование свойства partitionedBy
Как упоминалось выше, динамические значения folderPath и fileName для данных временных рядов можно задать в разделе **partitionedBy**, а также с помощью макросов фабрики данных и системных переменных SliceStart и SliceEnd, которые указывают время начала и окончания среза данных.

Дополнительные сведения о системных переменных и функциях фабрики данных, которые можно использовать в разделе partitionedBy см. в статьях [Системные переменные фабрики данных](data-factory-scheduling-and-execution.md#data-factory-system-variables) и [Справочник по функциям фабрики данных](data-factory-scheduling-and-execution.md#data-factory-functions-reference).

Дополнительные сведения о наборах данных временных рядов, планировании и срезах см. в статьях [Создание наборов данных](data-factory-create-datasets.md) и [Планирование и исполнение](data-factory-scheduling-and-execution.md).

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


## Свойства типа "Действие копирования большого двоичного объекта Azure"  
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Такие свойства, как имя, описание, входные и выходные таблицы, различные политики и т. д., доступны для всех типов действий.

То, какие свойства будут доступны в разделе typeProperties, зависит от типа действия, а в случае с действием копирования — еще и от типов источников и приемников.

Для **BlobSource** в разделе **typeProperties** могут быть указаны следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- | 
| treatEmptyAsNull | Указывает, следует ли интерпретировать null или пустую строку как значение null. <p>Обратите внимание, что если задано свойство **quoteChar**, пустые строки в кавычках могут рассматриваться как значения NULL с этим свойством.</p> | TRUE (по умолчанию) <br/>FALSE | Нет |
| skipHeaderLineCount | Указывает, сколько строк необходимо пропустить. Применяется, только когда для входного набора данных используется **TextFormat**. | Целое число от 0 до макс. | Нет | 
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. | True (значение по умолчанию), False | Нет | 


Для **BlobSink** в разделе **typeProperties** могут быть указаны следующие свойства:

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| blobWriterAddHeader | Указывает, нужно ли добавлять заголовок для определений столбцов. | TRUE<br/>FALSE (по умолчанию) | Нет |
| copyBehavior | Это свойство определяет поведение функции копирования, когда в качестве источника используется BlobSource или FileSystem. | <p>Для свойства copyBehavior можно использовать три значения. </p><ul><li>**PreserveHierarchy:** сохраняет иерархии файлов в папку назначения, т. е. относительный путь исходного файла к исходной папке идентичен относительному пути целевого файла к папке назначения.</li><li>**FlattenHierarchy:** все файлы из исходной папки будут на первом уровне целевой папки. Целевые файлы будут иметь автоматически созданные имена. </li><li>**MergeFiles: (по умолчанию)** объединяет все файлы из исходной папки в один файл. Если указано имя файла или большого двоичного объекта, именем объединенного файла будет указанное имя; в противном случае имя файла будет автоматически сформировано.</li></ul> | Нет |

### Примеры recursive и copyBehavior
В данном разделе описываются результаты выполнения операции копирования при использовании различных сочетаний значений recursive и copyBehavior.

recursive | copyBehavior | Результаты выполнения операции
--------- | ------------ | --------
Да | preserveHierarchy | <p>Для исходной папки «Папка1» со структурой</p> <p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5</p>целевая папка «Папка1» будет иметь такую же структуру, как исходная папка<p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5</p>.  
Да | flattenHierarchy | <p>Для исходной папки «Папка1» со структурой</p> <p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5</p>целевая папка «Папка1» будет иметь структуру<p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла «Файл1»<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла «Файл2»<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла «Файл3»<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла «Файл4»<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла «Файл5»</p>
Да | mergeFiles | <p>Для исходной папки «Папка1» со структурой</p> <p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5</p>целевая папка «Папка1» будет иметь структуру <p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов «Файл1», «Файл2», «Файл3», «Файл4» и «Файл5» будет объединено в один файл с автоматически созданным именем файла.</p>
нет | preserveHierarchy | <p>Для исходной папки "Папка1" со структурой</p> <p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5</p>целевая папка "Папка1" будет иметь структуру<p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/></p><p>"ВложеннаяПапка1" с файлами "Файл3", "Файл4" и "Файл5" не будут включены в эту папку.</p>.
нет | flattenHierarchy | <p>Для исходной папки "Папка1" со структурой</p> <p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5</p>целевая папка "Папка1" будет иметь структуру<p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла "Файл2"<br/></p><p>Папка "ВложеннаяПапка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку.</p>
нет | mergeFiles | <p>Для исходной папки «Папка1» со структурой</p> <p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5</p>целевая папка «Папка1» будет иметь структуру<p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов «Файл1» и «Файл2» будет объединено в один файл с автоматически созданным именем для файла «Файл1»</p><p>«ВложеннаяПапка1» с файлами «Файл3», «Файл4» и «Файл5» не будет включена в эту папку.</p>.

  


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=AcomDC_0302_2016-->