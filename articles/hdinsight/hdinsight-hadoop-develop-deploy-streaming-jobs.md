
<properties
	pageTitle="Разработка программ потоковой передачи Hadoop на C# для HDInsight | Microsoft Azure"
	description="Вы узнаете, как разработать программы потоковой передачи MapReduce на C#, а также о том, как загрузить их на платформу Azure HDInsight."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/28/2016"
	ms.author="jgao"/>



# Разработка программ потоковой передачи Hadoop на C# для HDInsight

Hadoop предоставляет API-интерфейс для MapReduce, позволяющий создавать функции map и reduce на языках, отличных от Java. В этом учебнике рассматривается создание программы для подсчета слов на языке C#, которая подсчитывает количество повторений заданного слова в указанных входных данных. На следующем изображении показано, как платформа MapReduce осуществляет подсчет слов:

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

> [AZURE.NOTE] Шаги, описанные в этой статье, можно применять только к кластерам Azure HDInsight под управлением Windows. Пример потоковой передачи для HDInsight под управлением Linux см. в статье [Разработка программ потоковой передачи Python для HDInsight](hdinsight-hadoop-streaming-python.md).

В этом учебнике описаны следующие процедуры.

- Разработка программ MapReduce потоковой передачи Hadoop на C# 
- Выполнение задания MapReduce в Azure HDInsight
- получить результаты задания MapReduce.

###Предварительные требования

Перед началом работы с этим учебником необходимо выполнить следующие действия:

- Рабочая станция с [Azure PowerShell][powershell-install] и [Microsoft Visual Studio](https://www.visualstudio.com/).
- Получить подписку Azure. Инструкции см. в разделе [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].


##Разработка программы потоковой передачи Hadoop на C&#35; для подсчета слов

Решение для подсчета слов содержит два проекта консольного приложения: приложение сопоставления и приложение сжатия. Приложение сопоставления выполняет потоковую передачу каждого слова на консоль, а приложение сжатия подсчитывает количество слов, переданных в режиме потоковой передачи из документа. Модуль сопоставления и модуль сжатия построчно считывают символы из стандартного входного потока (stdin) и записывают данные в стандартный выходной поток (stdout).

**Создание программы сопоставления**

1. Откройте Visual Studio и создайте консольное приложение на языке C#. Назовите его **WordCountMapper**.
2. В обозревателе решений переименуйте **Program.cs** на **WordCountMapper.cs**. Щелкните **Да**, чтобы подтвердить переименование всех ссылок.
3. Замените код в WordCountMapper.cs на следующий:

        using System;
        using System.IO;

        namespace WordCountMapper
        {
            class WordCountMapper
            {
                static void Main(string[] args)
                {
                    if (args.Length > 0)
                    {
                        Console.SetIn(new StreamReader(args[0]));
                    }

                    string line;
                    string[] words;

                    while ((line = Console.ReadLine()) != null)
                    {
                        words = line.Split(' ');

                        foreach (string word in words)
                            Console.WriteLine(word.ToLower());
                    }
                }
            }
        }

4. Постройте решение и убедитесь в отсутствии ошибок компиляции.

**Создание программы сжатия**

1. Добавьте в решение другое консольное приложение на C# с именем **WordCountReducer**. Его расположение — C:\\Tutorials\\WordCount.
2. В обозревателе решений переименуйте **Program.cs** на **WordCountReducer.cs**. Щелкните **Да**, чтобы подтвердить переименование всех ссылок.
3. Замените код в WordCountReducer.cs на следующий:

        using System;
        using System.IO;

        namespace WordCountReducer
        {
            class WordCountReducer
            {
                static void Main(string[] args)
                {
                    string word, lastWord = null;
                    int count = 0;

                    if (args.Length > 0)
                    {
                        Console.SetIn(new StreamReader(args[0]));
                    }

                    while ((word = Console.ReadLine()) != null)
                    {
                        if (word != lastWord)
                        {
                            if (lastWord != null)
                                Console.WriteLine("{0}[{1}]", lastWord, count);

                            count = 1;
                            lastWord = word;
                        }
                        else
                        {
                            count += 1;
                        }
                    }
                    Console.WriteLine(count);
                }
            }
        }

4. Постройте решение и убедитесь в отсутствии ошибок компиляции.

Будут доступны исполняемые файлы mapper и reducer:

- ..\\WordCountMapper\\bin\\Debug\\WordCountMapper.exe
- ..\\WordCountReducer\\bin\\Debug\\WordCountReducer.exe


##Отправка данных в хранилище BLOB-объектов Azure
Служба Azure HDInsight использует хранилище BLOB-объектов Azure в качестве файловой системы по умолчанию. Можно настроить кластер HDInsight на использование дополнительного хранилища BLOB-объектов для хранения файлов данных. В этом разделе мы создадим учетную запись хранения Azure и передадим файлы данных в хранилище больших двоичных объектов. В качестве файлов данных используются TXT-файлы в каталоге %hadoop\_home%\\share\\doc\\hadoop\\common.


**Создание учетной записи хранения и контейнера**

1. Откройте Azure PowerShell.
2. Задайте переменные, а затем выполните команды:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"  
		$containerName = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

3. Выполните следующие команды, чтобы создать учетную запись хранения и контейнер хранилища больших двоичных объектов в этой учетной записи:

		# Select an Azure subscription
		Select-AzureSubscription $subscriptionName

		# Create a Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location

		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName -Context $destContext

4. Выполните следующие команды, чтобы проверить учетную запись хранения и контейнер:

		Get-AzureStorageAccount -StorageAccountName $storageAccountName
		Get-AzureStorageContainer -Context $destContext

**Передача файлов данных**

1. В окне Azure PowerShell задайте значения для локальной папки и папки назначения:

		$localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
		$destFolder = "WordCount/Input"

	Заметьте, что исходной является папка **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\doc\\hadoop\\common**, а целевой — папка **WordCount/Input**. Исходное расположение — это расположение TXT-файла в эмуляторе HDInsight. Целевое расположение — это структура папок, которая будет отображаться в контейнере BLOB-объектов Azure.

3. Чтобы получить список TXT-файлов в исходной папке, выполните следующие команды:

		# Get a list of the .txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

5. Чтобы скопировать файлы, выполните следующий фрагмент кода:

		# Copy the files from the local workstation to the Blob container
		foreach ($file in $filesTxt){

		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"

		    write-host "Copying $fileName to $blobName"

		    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
		}

6. Выполните следующую команду, чтобы получить список переданных файлов:

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder


**Передача приложений для подсчета слов**

1. Установите следующие переменные в окне Azure PowerShell:

		$mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
		$reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
		$blobFolder = "WordCount/Apps"

	Обратите внимание на то, что целевой папкой является **WordCount/Apps**. Эта же структура папок будет отображаться в контейнере BLOB-объектов Azure.

2. Выполните следующие команды, чтобы скопировать приложения:

		Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
		Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

3. Выполните следующую команду, чтобы получить список переданных файлов:

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

	Должны появиться оба указанных файла программы.


##Выполнение задания MapReduce в Azure HDInsight

В этом разделе рассматривается сценарий Azure PowerShell, который выполняет все возможные задачи, связанные с выполнением задания MapReduce. В список заданий входят:

1. Подготовка кластера HDInsight

	1. Создание учетной записи хранения, которая будет использоваться в качестве файловой системы по умолчанию для кластера HDInsight.
	2. Создание контейнера хранилища BLOB-объектов
	3. Создание кластера HDInsight

2. Отправка задания MapReduce

	1. Создание определения задания MapReduce для потоковой передачи
	2. Отправка задания MapReduce
	3. Ожидание завершения задания
	4. Отображение стандартной ошибки
	5. Отображение стандартного вывода

3. Удаление кластера

	1. Удаление кластера HDInsight
	2. Удаление учетной записи хранения, которая использовалась в качестве файловой системы по умолчанию для кластера HDInsight.


**Запуск скрипта Azure PowerShell**

1. Откройте Блокнот.
2. Скопируйте следующий код и вставьте в Блокнот:

		# ====== STORAGE ACCOUNT AND HDINSIGHT CLUSTER VARIABLES ======
		$subscriptionName = "<AzureSubscriptionName>"
		$stringPrefix = "<StringForPrefix>"     ### Prefix to cluster, Storage account, and container names
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$location = "<MicrosoftDataCenter>"     ### Must match the data storage account location
		$clusterNodes = 1

		$clusterName = $stringPrefix + "hdicluster"

		$storageAccountName_Default = $stringPrefix + "hdistore"
		$containerName_Default =  $stringPrefix + "hdicluster"

		# ====== THE STREAMING MAPREDUCE JOB VARIABLES ======
		$mrMapper = "WordCountMapper.exe"
		$mrReducer = "WordCountReducer.exe"
		$mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
		$mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

		Select-AzureSubscription $subscriptionName

		#====== CREATE A STORAGE ACCOUNT ======
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

		#====== CREATE A BLOB STORAGE CONTAINER ======
		Write-Host "Create a Blob storage container" -ForegroundColor Green
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default

		New-AzureStorageContainer -Name $containerName_Default -Context $destContext

		#====== CREATE AN HDINSIGHT CLUSTER ======
		Write-Host "Create an HDInsight cluster" -ForegroundColor Green
		$storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }

		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data

		Select-AzureSubscription $subscriptionName
		New-AzureHDInsightCluster -Name $clusterName -Location $location -Config $config

		#====== CREATE A STREAMING MAPREDUCE JOB DEFINITION ======
		Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green

		$mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
		$mrJobDef.Files.Add($mrMapperFile)
		$mrJobDef.Files.Add($mrReducerFile)

		#====== RUN A STREAMING MAPREDUCE JOB ======
		Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
		Select-AzureSubscription $subscriptionName
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef
		Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600

		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput

		#====== DELETE THE HDINSIGHT CLUSTER ======
		Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
		Select-AzureSubscription $subscriptionName
		Remove-AzureHDInsightCluster -Name $clusterName

		#====== DELETE THE STORAGE ACCOUNT ======
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. Задайте первые четыре переменных скрипта: Переменная **$stringPrefix** используется для добавления префикса к имени кластера HDInsight, имени учетной записи хранения и имени контейнера хранилища больших двоичных объектов. Так как количество символов в этих именах должно быть больше 3 и меньше 24, следите за тем, чтобы общее количество символов в строке и именах, используемых сценарием, не превышало это ограничение. Для **$stringPrefix** необходимо использовать буквы только в нижнем регистре.

	Переменные **$storageAccountName\_Data** и **$containerName\_Data** — это учетная запись хранения и контейнер, которые вы создали ранее. поэтому им необходимо присвоить имена. Они предназначены для хранения файлов данных и приложений. Переменная **$location** должна соответствовать расположению учетной записи хранения данных.

4. Просмотрите остальные переменные.
5. Сохраните файл скрипта.
6. Откройте Azure PowerShell.
7. Выполните следующую команду, чтобы задать политику выполнения RemoteSigned:

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. При появлении запроса введите имя пользователя и пароль для кластера HDInsight. Пароль должен содержать по крайней мере 10 символов, среди которых несколько обязательных: прописная буква, строчная буква, число и специальный знак. Если вы не хотите, чтобы появлялся запрос на ввод учетных данных, см. статью [Работа с паролями, защищенными строками и учетными данными в Windows PowerShell][powershell-PSCredential].

Пример пакета SDK для HDInsight .NET по отправке заданий потоковой передачи Hadoop см. статье [Отправка заданий Hadoop программным способом][hdinsight-submit-jobs].


##Извлечение выходных данных задания MapReduce
В этом разделе показано, как загружать и отображать выходные данные. Информацию об отображении результатов в Excel см. в статьях [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC][hdinsight-ODBC] и [Подключение Excel к HDInsight с помощью Power Query][hdinsight-power-query].


**Извлечение выходных данных**

1. Откройте окно Azure PowerShell.
2. Задайте значения, а затем выполните команды:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<TheDataStorageAccountName>"
		$containerName = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-00000"

3. Выполните следующие команды для создания объекта контекста службы хранилища Azure:

		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  

4. Выполните следующие команды, чтобы загрузить и отобразить выходные данные:

		Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"



##Дальнейшие действия
В этом учебнике вы узнали, как разработать задание потоковой передачи Hadoop в MapReduce, протестировать приложение в эмуляторе HDInsight, написать сценарий Azure PowerShell для подготовки кластера HDInsight и выполнения задания MapReduce в кластере. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

- [Приступая к работе с Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Приступая к работе с эмулятором HDInsight][hdinsight-get-started-emulator]
- [Разработка программ MapReduce на Java для HDInsight][hdinsight-develop-mapreduce]
- [Использование хранилища больших двоичных объектов Azure с HDInsight][hdinsight-storage]
- [Администрирование HDInsight с использованием Azure PowerShell][hdinsight-admin-powershell]
- [Отправка данных в HDInsight][hdinsight-upload-data]
- [Использование Hive с HDInsight][hdinsight-use-hive]
- [Использование Pig с HDInsight][hdinsight-use-pig]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator.md
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator.md#blobstorage
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[powershell-install]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-hadoop-develop-deploy-streaming-jobs/HDI.WordCountDiagram.gif "Последовательность операций приложения для подсчета слов MapReduce"

<!---HONumber=AcomDC_0218_2016-->