<properties
	pageTitle="Связанные службы вычислений | Microsoft Azure"
	description="Сведения о средах вычислений, которые можно использовать в конвейерах фабрики данных Azure для преобразования и обработки данных."
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

# Связанные службы вычислений

В этой статье описываются различные среды вычислений, которые можно использовать для обработки и преобразования данных. Здесь содержатся также сведения о различных конфигурациях (конфигурациях по запросу и ваших собственных), которые поддерживаются фабрикой данных при настройке связанных служб, связывающих эти среды вычислений с фабрикой данных Azure.

## Среда вычислений по запросу

В конфигурации такого типа среда вычислений полностью управляется службой фабрики данных Azure. Среда автоматически создается службой фабрики данных перед отправкой задания для обработки данных и удаляется после его выполнения. Вы можете создать связанную службу для среды вычислений по запросу, настроить ее и управлять детализированными параметрами выполнения задания, управления кластером и параметрами действий начальной загрузки.

> [AZURE.NOTE] Конфигурации по запросу в настоящее время поддерживаются только для кластеров Azure HDInsight.

## Связанная служба Azure HDInsight по запросу
Для обработки данных служба фабрики данных Azure автоматически создает кластер HDInsight под управлением Windows/Linux по запросу. Кластер создается в том же регионе, что и учетная запись хранения (свойство linkedServiceName в JSON), связанная с кластером.

Обратите внимание на следующие **важные** моменты, касающиеся связанной службы HDInsight по запросу.

- Вы не увидите кластер HDInsight по запросу, созданный в подписке Azure, так как служба фабрики данных Azure управляет кластером HDInsight по запросу от вашего имени.
- Журналы заданий, которые выполняются в кластере HDInsight по запросу, копируются в учетную запись хранения, связанную с кластером HDInsight. Доступ к этим журналам можно получить на классическом портале Azure в колонке **Подробности о выполнении операции**. Дополнительные сведения см. в статье [Мониторинг конвейеров и управление ими](data-factory-monitor-manage-pipelines.md).
- Вы будете оплачивать только время, когда кластер HDInsight работает и выполняет задания.

> [AZURE.IMPORTANT] Подготовка к работе кластера Azure HDInsight по запросу обычно занимает более **15 минут**.

### Пример
Представленный ниже код JSON определяет связанную службу HDInsight по запросу. При обработке среза данных фабрика данных автоматически создает кластер HDInsight **под управлением Windows**. Обратите внимание на то, что в примере кода JSON свойство **osType** не задано, поскольку имеет значение **Windows** по умолчанию.

	{
	  "name": "HDInsightOnDemandLinkedService",
	  "properties": {
	    "type": "HDInsightOnDemand",
	    "typeProperties": {
	      "version": "3.2",
	      "clusterSize": 1,
	      "timeToLive": "00:30:00",
	      "linkedServiceName": "StorageLinkedService"
	    }
	  }
	}


Представленный ниже код JSON определяет связанную службу HDInsight по запросу под управлением Linux. При обработке среза данных служба фабрики данных автоматически создает кластер HDInsight **под управлением Linux**. Необходимо указать значения для свойств **sshUserName** и **sshPassword**.


	{
	    "name": "HDInsightOnDemandLinkedService",
	    "properties": {
	        "hubName": "getstarteddf0121_hub",
	        "type": "HDInsightOnDemand",
	        "typeProperties": {
	            "version": "3.2",
	            "clusterSize": 4,
	            "timeToLive": "00:05:00",
	            "osType": "linux",
	            "sshPassword": "MyPassword!",
	            "sshUserName": "myuser",
	            "linkedServiceName": "StorageLinkedService",
	        }
	    }
	}

> [AZURE.IMPORTANT] 
Кластер HDInsight создает **контейнер по умолчанию** в хранилище BLOB-объектов, указанном в коде JSON (**linkedServiceName**). При удалении кластера HDInsight этот контейнер не удаляется. Это сделано специально. В случае связанной службы HDInsight по запросу кластер HDInsight создается для обработки каждого среза данных (если не используется динамический кластер **timeToLive**) и удаляется по завершении обработки.
> 
> По мере обработки новых срезов количество контейнеров в хранилище BLOB-объектов будет увеличиваться. Если эти контейнеры не используются для устранения неполадок с заданиями, удалите их — это позволит сократить расходы на хранение. Подобные контейнеры имеют имена вида "adf**имя\_вашей\_фабрики\_данных**-**имя\_связанной\_службы**-отметка\_даты\_и\_времени". Для удаления контейнеров из хранилища BLOB-объектов Azure пользуйтесь такими средствами, как [обозреватель хранилищ Майкрософт](http://storageexplorer.com/).

### Свойства

Свойство | Описание | Обязательно
-------- | ----------- | --------
type | Свойству type необходимо присвоить значение **HDInsightOnDemand**. | Да
clusterSize | Размер кластера по запросу. Укажите, сколько узлов должно быть в этом кластере по запросу. | Да
timetolive | <p>Допустимое время простоя кластера HDInsight по запросу. Указывает, как долго кластер HDInsight по запросу будет оставаться активным после выполнения действия, если в кластере нет других активных заданий.</p><p>Например, если выполнение действия занимает 6 минут, а значение свойства timetolive равно 5 минутам, кластер остается активным в течение 5 минут по истечении 6-минутного выполнения действия. Если в течение этих 6 минут выполняется другое действие, оно обрабатывается в том же кластере.</p><p>Создание кластера HDInsight по запросу является ресурсоемкой и довольно длительной операцией, поэтому при необходимости этот параметр следует использовать для повышения производительности фабрики данных путем повторного использования кластера HDInsight по запросу.</p><p>Если значение timetolive равно 0, кластер удаляется сразу после выполнения действия. С другой стороны, если задать большое значение, кластер может простаивать без необходимости, что приведет к большим затратам. Поэтому необходимо установить соответствующее значение в соответствии со своими потребностями.</p><p>Если значение свойства timetolive задано правильно, один и тот же экземпляр кластера HDInsight по запросу могут совместно использовать несколько конвейеров.</p> | Да
версия | Версия кластера HDInsight. Значение по умолчанию равно 3.1 для кластера Windows и 3.2 для кластера Linux. | Нет
linkedServiceName (имя связанной службы) | Хранилище больших двоичных объектов, которое кластер по запросу должен использовать для хранения и обработки данных. | Да
additionalLinkedServiceNames | Указывает дополнительные учетные записи хранения для связанной службы HDInsight, чтобы служба фабрики данных могла регистрировать их от вашего имени. | Нет
osType | Тип операционной системы. Допустимые значения: Windows (по умолчанию) и Linux. | Нет
hcatalogLinkedServiceName | Имя связанной службы SQL Azure, указывающие на базу данных HCatalog. Будет создан кластер HDInsight по требованию с использованием базы данных SQL Azure как в качестве метахранилища. | Нет
sshUser | Пользователь SSH для кластера HDInsight под управлением Linux | Да (только для Linux)
sshPassword | Пароль SSH для кластера HDInsight под управлением Linux | Да (только для Linux)


#### Пример кода JSON additionalLinkedServiceNames

    "additionalLinkedServiceNames": [
        "otherLinkedServiceName1",
		"otherLinkedServiceName2"
  	]
 
### Дополнительные свойства

Для детализированной настройки кластера HDInsight по запросу можно также указать следующие свойства.

Свойство | Описание | Обязательно
:-------- | :----------- | :--------
coreConfiguration | Задает параметры конфигурации ядра (как в файле core-site.xml) для создаваемого кластера HDInsight. | Нет
hBaseConfiguration | Задает основные параметры конфигурации HBase (hbase-site.xml) для кластера HDInsight. | Нет
hdfsConfiguration | Задает основные параметры конфигурации HDFS (hdfs-site.xml) для кластера HDInsight. | Нет
hiveConfiguration | Задает основные параметры конфигурации Hive (hive-site.xml) для кластера HDInsight. | Нет
mapReduceConfiguration | Задает параметры конфигурации MapReduce (mapred-site.xml) для кластера HDInsight. | Нет
oozieConfiguration | Задает параметры конфигурации Oozie (oozie-site.xml) для кластера HDInsight. | Нет
stormConfiguration | Задает параметры конфигурации Storm (storm-site.xml) для кластера HDInsight. | Нет
yarnConfiguration | Задает параметры конфигурации Yarn (yarn-site.xml) для кластера HDInsight. | Нет

#### Пример: конфигурация кластера HDInsight по запросу с дополнительными свойствами

	{
	  "name": " HDInsightOnDemandLinkedService",
	  "properties": {
	    "type": "HDInsightOnDemand",
	    "typeProperties": {
	      "clusterSize": 16,
	      "timeToLive": "01:30:00",
	      "version": "3.2",
	      "linkedServiceName": "adfods1",
	      "coreConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "hiveConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "mapReduceConfiguration": {
	        "mapreduce.reduce.java.opts": "-Xmx4000m",
	        "mapreduce.map.java.opts": "-Xmx4000m",
	        "mapreduce.map.memory.mb": "5000",
	        "mapreduce.reduce.memory.mb": "5000",
	        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
	      },
	      "yarnConfiguration": {
	        "yarn.app.mapreduce.am.resource.mb": "5000",
	        "mapreduce.map.memory.mb": "5000"
	      },
	      "additionalLinkedServiceNames": [
	        "datafeeds",
	        "adobedatafeed"
	      ]
	    }
	  }
	}

### Размеры узлов
Вы можете указать размеры головных узлов, узлов данных и узлов zookeeper, используя следующие свойства.

Свойство | Описание | Обязательное
:-------- | :----------- | :--------
headNodeSize | Указывает размер головного узла. По умолчанию используется значение "Большой". Дополнительные сведения см. в разделе **Указание размеров узлов** ниже. | Нет
dataNodeSize | Задает размер узла данных. По умолчанию используется значение "Большой". | Нет
zookeeperNodeSize | Задает размер узла Zoo Keeper. Значение по умолчанию: "Малый". | Нет
 
#### Указание размеров узлов
Сведения о строковых значениях, необходимых для задания указанных выше свойств, см. в статье [Размеры виртуальных машин](../virtual-machines/virtual-machines-size-specs.md#size-tables). Значения должны соответствовать указанным в статье **командлетам и API**. Как указано в статье, узел данных большого размера (по умолчанию) имеет 7 ГБ памяти, которых может быть недостаточно для вашего сценария.

Если вы хотите создать головные узлы и рабочие узлы размера D4, необходимо указать **Standard\_D4** в качестве значения для свойств headNodeSize и dataNodeSize.

	"headNodeSize": "Standard_D4",	
	"dataNodeSize": "Standard_D4",

Если для этих свойств указано неверное значение, может возникнуть следующая **ошибка:** Не удалось создать кластер. Исключение: не удается завершить операцию создания кластера. Сбой операции, код ошибки "400". Оставшееся состояние кластера: "Ошибка". Сообщение: "PreClusterCreationValidationFailure". При появлении этой ошибки убедитесь, что вы используете имя **командлета или API** из таблицы в указанной выше статье.



## Использование собственной среды вычислений

В конфигурации такого типа вы можете зарегистрировать уже существующую среду вычислений как связанную службу в фабрике данных. Вы будете управлять средой вычислений, а служба фабрики данных — использовать ее для выполнения действий.

Такая конфигурация поддерживается в следующих средах вычислений:

- Azure HDInsight
- Пакетная служба Azure
- Машинное обучение Azure.

## Связанная служба Azure HDInsight

Чтобы зарегистрировать собственный кластер HDInsight в фабрике данных, вы можете создать связанную службу Azure HDInsight.

### Пример

	{
	  "name": "HDInsightLinkedService",
	  "properties": {
	    "type": "HDInsight",
	    "typeProperties": {
	      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
	      "userName": "admin",
	      "password": "<password>",
	      "location": "WestUS",
	      "linkedServiceName": "MyHDInsightStoragelinkedService"
	    }
	  }
	}

### Свойства

Свойство | Описание | Обязательно
-------- | ----------- | --------
type | Свойству type необходимо присвоить значение **HDInsight**. | Да
clusterUri | Универсальный код ресурса (URI) кластера HDInsight. | Да
Имя пользователя | Укажите имя пользователя, которое будет использоваться для подключения к существующему кластеру HDInsight. | Да
пароль | Укажите пароль для учетной записи пользователя. | Да
location | Укажите расположение кластера HDInsight (например, WestUS). | Да
linkedServiceName (имя связанной службы) | Имя связанной службы для хранения больших двоичных объектов, которая используется в данном кластере HDInsight. | Да

## Связанная пакетная служба Azure

Чтобы зарегистрировать пакетный пул виртуальных машин (ВМ) в фабрике данных, можно создать связанную пакетную службу Azure. Пользовательские действия .NET можно выполнять с помощью пакетной службы Azure или службы Azure HDInsight.

Если вы еще не знакомы с пакетной службой Azure, см. следующие статьи.


- [Основные сведения о пакетной службе Azure](../batch/batch-technical-overview.md) — общие сведения о пакетной службе Azure.
- Статья о командлете [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) со сведениями о создании учетной записи пакетной службы Azure или статья о [классическом портале Azure](../batch/batch-account-create-portal.md) со сведениями о создании учетной записи пакетной службы Azure с помощью классического портала Azure. Подробные инструкции по использованию этого командлета см. в статье [Использование Azure PowerShell для управления учетной записью пакетной службы Azure](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
- Статья о командлете [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) со сведениями о создании пула пакетной службы Azure.

### Пример

	{
	  "name": "AzureBatchLinkedService",
	  "properties": {
	    "type": "AzureBatch",
	    "typeProperties": {
	      "accountName": "<Azure Batch account name>",
	      "accessKey": "<Azure Batch account key>",
	      "poolName": "<Azure Batch pool name>",
	      "linkedServiceName": "<Specify associated storage linked service reference here>"
	    }
	  }
	}

Добавьте текст **.<имя\_региона>** к имени учетной записи пакетной службы для свойства **accountName**. Пример:

			"accountName": "mybatchaccount.eastus"

Другой вариант — указать конечную точку batchUri, как показано ниже.

			"accountName": "adfteam",
			"batchUri": "https://eastus.batch.azure.com",

### Свойства

Свойство | Описание | Обязательно
-------- | ----------- | --------
type | Свойству type необходимо присвоить значение **AzureBatch**. | Да
accountName | Имя учетной записи пакетной службы Azure | Да
accessKey | Ключ доступа к учетной записи пакетной службы Azure. | Да
poolName | Имя пула виртуальных машин. | Да
linkedServiceName (имя связанной службы) | Имя связанной службы хранилища Azure, которая ассоциируется с этой связанной пакетной службой Azure. Эта связанная служба используется для промежуточных файлов, необходимых для выполнения действий и хранения журналов выполнения действий. | Да


## Связанная служба машинного обучения Azure

Создайте связанную службу Машинного обучения Azure, чтобы зарегистрировать конечную точку пакетной оценки показателей машинного обучения оценки в фабрике данных.

### Пример

	{
	  "name": "AzureMLLinkedService",
	  "properties": {
	    "type": "AzureML",
	    "typeProperties": {
	      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
	      "apiKey": "<apikey>"
	    }
	  }
	}

### Свойства

Свойство | Описание | Обязательно
-------- | ----------- | --------
Тип | Свойству type необходимо присвоить значение **AzureML**. | Да
mlEndpoint | URL-адрес пакетной оценки. | Да
apiKey | API модели опубликованной рабочей области. | Да


## Связанная служба аналитики озера данных Azure
Можно создать связанную службу **аналитики озера данных Azure**, чтобы связать службу вычислений аналитики озера данных Azure с фабрикой данных Azure, прежде чем использовать [действие U-SQL аналитики озера данных](data-factory-usql-activity.md) в конвейере.

В следующем примере представлено определение JSON для связанной службы аналитики озера данных Azure.

	{
	    "name": "AzureDataLakeAnalyticsLinkedService",
	    "properties": {
	        "type": "AzureDataLakeAnalytics",
	        "typeProperties": {
	            "accountName": "adftestaccount",
	            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
	            "authorization": "<authcode>",
				"sessionId": "<session ID>",
	            "subscriptionId": "<subscription id>",
	            "resourceGroupName": "<resource group name>"
	        }
	    }
	}


В следующей таблице приведены описания свойств из определения JSON.

Свойство | Описание | Обязательно
-------- | ----------- | --------
Тип | Свойству type необходимо присвоить значение **AzureDataLakeAnalytics**. | Да
accountName | Имя учетной записи аналитики озера данных Azure. | Да
dataLakeAnalyticsUri | Универсальный код ресурса (URI) аналитики озера данных Azure. | Нет
authorization | Код авторизации извлекается автоматически после нажатия кнопки **Авторизовать** в редакторе фабрики данных и выполнения входа с авторизацией OAuth. | Да
subscriptionId | Идентификатор подписки Azure | Нет (если не указан, используется подписка фабрики данных).
имя\_группы\_ресурсов | Имя группы ресурсов Azure | Нет (если не указано, используется группа ресурсов фабрики данных).
sessionid | Идентификатор сеанса из сеанса авторизации OAuth. Каждый идентификатор сеанса является уникальным и используется только один раз. Это свойство создается автоматически в редакторе фабрики данных. | Да

Срок действия кода авторизации, созданного с помощью кнопки **Авторизовать**, через некоторое время истекает. Сроки действия для различных типов учетных записей пользователей см. в следующей таблице. При истечении **срока действия маркера** проверки подлинности может появиться следующее сообщение об ошибке: "Ошибка операции с учетными данными: invalid\_grant - AADSTS70002: ошибка при проверке учетных данных". AADSTS70008: срок действия предоставленных прав доступа истек или они были отозваны. Идентификатор отслеживания: d18629e8-af88-43c5-88e3-d8419eb1fca1 Идентификатор корреляции: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Временная отметка: 2015-12-15 21:09:31Z".

| Тип пользователя | Срок действия |
| :-------- | :----------- | 
| Пользователи, которые НЕ управляются Azure Active Directory (@hotmail.com, @live.com и т. д.) | 12 часов |
| Пользователи, которые управляются Azure Active Directory (AAD) | | Через 14 дней после выполнения последнего среза, если срезы, основанные на связанной службе на основе OAuth, не выполнялись в течение 14 дней с момента последнего среза. <p>90 дней, если срез, основанный на связанной службе на основе OAuth, выполняется по крайней мере раз в 14 дней.</p> |

 
Чтобы избежать этой ошибки или исправить ее, вам потребуется повторно авторизоваться с помощью кнопки **Авторизовать** и повторно развернуть связанную службу, когда **срок действия маркера истечет**. Значения свойств sessionId и authorization также можно задавать программно с помощью кода, приведенного в следующем разделе.

### Программное создание значений свойств sessionId и authorization 
Следующий код создает значения свойств**sessionId** и **authorization**.

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Подробные сведения о классах фабрики данных, используемых в коде, см. в разделах [Класс AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Класс AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) и [Класс AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Для использования класса WindowsFormsWebAuthenticationDialog необходимо добавить ссылку на Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll.
 

## Связанная служба SQL Azure

Связанная служба SQL Azure создается и применяется к [действию хранимой процедуры](data-factory-stored-proc-activity.md) для вызова хранимой процедуры из конвейера фабрики данных. Дополнительную информацию об этой связанной службе см. в статье [Соединитель SQL Azure](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).

<!---HONumber=AcomDC_0224_2016-->