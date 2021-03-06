<properties 
	pageTitle="Отправка файлов в учетную запись служб мультимедиа с помощью .NET" 
	description="Узнайте, как включить мультимедийное содержимое в службы мультимедиа, создав и отправив ресурс." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="02/03/2016"  
	ms.author="juliako"/>



#Отправка файлов в учетную запись служб мультимедиа с помощью .NET

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

В службах мультимедиа цифровые файлы отправляются (или принимаются) в актив. Сущность **Asset** может содержать видео, аудио, изображения, коллекции эскизов, текстовые каналы и файлы скрытых субтитров (и метаданные этих файлов). После отправки этих файлов содержимое сохраняется в безопасном расположении в облаке для дальнейшей обработки и потоковой передачи.

Файлы в ресурсе называются **файлами ресурса**. Экземпляр **AssetFile** и фактический файл мультимедиа — это два разных объекта. Экземпляр AssetFile содержит метаданные о файле мультимедиа, а сам файл мультимедиа — фактическое мультимедийное содержимое.

При создании ресурсов можно указать следующие параметры шифрования.

- **None** — шифрование не используется. Это значение по умолчанию. Обратите внимание, что при использовании этого параметра содержимое не защищено при передаче или в хранилище. Используйте этот параметр, если MP4-файл планируется доставить с помощью поэтапного скачивания. 
- **CommonEncryption** — используйте этот параметр при отправке содержимого, которое уже зашифровано и защищено с помощью стандартного шифрования или PlayReady DRM (например, Smooth Streaming с защитой PlayReady DRM).
- **EnvelopeEncrypted** — используйте этот параметр при отправке HLS с шифрованием AES. Обратите внимание, что файлы должны быть закодированы и зашифрованы с помощью Transform Manager.
- **StorageEncrypted** — шифрует незашифрованное содержимое локально с помощью 256-разрядного алгоритма шифрования AES-256, а затем отправляет его в службу хранилища Azure, где оно хранится в зашифрованном виде. Активы, защищенные с помощью шифрования хранилища, автоматически расшифровываются и помещаются в систему зашифрованных файлов до кодирования и при необходимости повторно кодируются до отправки в виде нового выходного актива. Основная причина использования шифрования хранилища — если нужно защитить входные файлы мультимедиа высокого качества с помощью стойкого шифрования при хранении на диске.

	Службы мультимедиа обеспечивают шифрование ресурсов на диске в хранилище, а не по сети, как технология управления цифровыми правами (DRM).

	Если ресурс зашифрован в хранилище, необходимо настроить политику доставки ресурсов. Дополнительную информацию см. в разделе [Настройка политики доставки ресурсов](media-services-dotnet-configure-asset-delivery-policy.md).

Если для ресурса задано шифрование с использованием параметра **CommonEncrypted** или **EnvelopeEncypted**, этот ресурс необходимо связать с ключом содержимого **ContentKey**. Дополнительную информацию см. в разделе [Как создать ContentKey](media-services-dotnet-create-contentkey.md).

Если для ресурса задано шифрование с использованием параметра **StorageEncrypted**, пакет SDK служб мультимедиа для .NET создаст для ресурса зашифрованный в хранилище ключ содержимого (**StorateEncrypted** **ContentKey**).

>[AZURE.NOTE]При создании URL-адресов для потоковой передачи содержимого службы мультимедиа используют значение свойства IAssetFile.Name (например, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.). Поэтому кодирование с помощью знака процента не допускается. Значение свойства **Name** не может содержать следующие [символы, зарезервированные для кодирования с помощью знака процента](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#". Кроме того, может использоваться только один символ "." для расширения имени файла.

В этом разделе показано, как использовать пакет SDK служб мультимедиа для .NET, а также расширения пакета SDK служб мультимедиа для .NET для передачи файлов в ресурс служб мультимедиа.

 
## Передача одного файла с помощью пакета SDK служб мультимедиа для .NET 

В следующем примере кода пакет SDK для .NET используется для выполнения таких задач:

- Создает пустой актив.
- Создает экземпляр AssetFile, который нужно связать с активом.
- Создает экземпляр AccessPolicy, определяющий разрешения и длительность доступа к активам.
- Создает экземпляр локатора, который предоставляет доступ к активам.
- Передает один файл мультимедиа в службы мультимедиа. 

		
		static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
		{
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions); 

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            var policy = _context.AccessPolicies.Create(
                                    assetName,
                                    TimeSpan.FromDays(30),
                                    AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            locator.Delete();
            policy.Delete();

            return inputAsset;
		}

##Передача нескольких файлов с помощью пакета SDK служб мультимедиа для .NET 

В следующем примере кода показано, как создать актив и отправить несколько файлов.

Код делает следующее:
	
- 	Создает пустой ресурс, используя метод CreateEmptyAsset, определенный на предыдущем шаге.
 	
- 	Создает экземпляр **AccessPolicy**, определяющий разрешения и длительность доступа к ресурсу.
 	
- 	Создает экземпляр **Locator**, который предоставляет доступ к ресурсу.
 	
- 	Создает экземпляр **BlobTransferClient**. Этот тип представляет клиент, работающий с большим двоичным объектом Azure. В этом примере используется клиент для отслеживания хода выполнения передачи.
 	
- 	Перечисляет файлы в указанном каталоге и создает экземпляр **AssetFile** для каждого файла.
 	
- 	Передает файлы в службы мультимедиа с помощью метода **UploadAsync**.
 	
>[AZURE.NOTE] Используйте метод UploadAsync, гарантирующий, что вызовы не будут блокироваться, а файлы будут загружаться в параллельном режиме.
 	
 	
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AccestFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }
	
	static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
	{
	    if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
	    {
	        Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
	    }
	}



При передаче большого количества ресурсов необходимо учитывать следующее.

- Создайте новый объект **CloudMediaContext** в каждом потоке. Класс **CloudMediaContext** не является потокобезопасным.
 
- Замените для NumberOfConcurrentTransfers значение по умолчанию (2) более высоким значением, например 5. Задание этого свойства влияет на все экземпляры **CloudMediaContext**.
 
- Оставьте для ParallelTransferThreadCount значение по умолчанию (10).
 
##<a id="ingest_in_bulk"></a>Массовый прием ресурсов с помощью пакета SDK служб мультимедиа для .NET 

Передача больших файлов ресурсов может оказаться узким местом при создании ресурса. Массовый прием ресурсов предполагает отделение создания ресурса от процесса передачи. Чтобы использовать массовый прием ресурсов, создайте манифест (IngestManifest), описывающий ресурс и связанные с ним файлы. Затем воспользуйтесь методом передачи по своему усмотрению, чтобы передать связанные файлы в контейнер больших двоичных объектов манифеста. Службы мультимедиа Microsoft Azure отслеживают контейнер больших двоичных объектов, связанный с манифестом. После загрузки файла в контейнер больших двоичных объектов службы мультимедиа Microsoft Azure завершает создание ресурса на основе конфигурации ресурса в манифесте (IngestManifestAsset).


Для создания новой сущности IngestManifest вызовите метод Create, предоставляемый коллекцией IngestManifests в классе CloudMediaContext. Этот метод создаст новую сущность IngestManifest с указанным именем манифеста.

	IIngestManifest manifest = context.IngestManifests.Create(name);

Создайте ресурсы, которые будут связаны с массовой сущностью IngestManifest. Настройте необходимые параметры шифрования ресурса для массового приема.

	// Create the assets that will be associated with this bulk ingest manifest
	IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
	IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);

Сущность IngestManifestAsset связывает ресурс с массовой сущностью IngestManifest для массового приема. Она также связывает сущности AssetFile, из которых состоит каждый ресурс. Чтобы создать IngestManifestAsset, используйте метод Create в контексте сервера.

В следующем примере показано добавление двух новых сущностей IngestManifestAsset, связывающих два созданных ранее ресурса с манифестом массового приема. Каждая сущность IngestManifestAsset связывает также набор файлов, которые передаются для каждого ресурса во время массового приема.

	string filename1 = _singleInputMp4Path;
	string filename2 = _primaryFilePath;
	string filename3 = _singleInputFilePath;
	
	IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
	IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
	
При этом можно использовать любое высокоскоростное клиентское приложение, которое может передавать файлы ресурсов по универсальному коду ресурса (URI) контейнера хранилища больших двоичных объектов, предоставляемому свойством **IIngestManifest.BlobStorageUriForUpload** сущности IngestManifest. [Приложение Aspera On Demand for Azure](https://datamarket.azure.com/application/2cdbc511-cb12-4715-9871-c7e7fbbb82a6) — одна из наиболее примечательных служб высокоскоростной передачи. Кроме того, можно написать код для передачи файлов ресурсов, как показано в следующем примере кода.
	
	static void UploadBlobFile(string destBlobURI, string filename)
	{
	    Task copytask = new Task(() =>
	    {
	        var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
	        CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
	        CloudBlobContainer blobContainer = blobClient.GetContainerReference(destBlobURI);
	
	        string[] splitfilename = filename.Split('\\');
	        var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);
	
	        using (var stream = System.IO.File.OpenRead(filename))
	            blob.UploadFromStream(stream);
	
	        lock (consoleWriteLock)
	        {
	            Console.WriteLine("Upload for {0} completed.", filename);
	        }
	    });
	
	    copytask.Start();
	}

В следующем примере показан код для передачи файлов ресурса для образца, используемого в этом разделе.
	
	UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
	UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
	UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
	

Ход выполнения массового приема для всех ресурсов, связанных с **IngestManifest**, можно определить с помощью опроса по свойству Statistics сущности **IngestManifest**. Чтобы получать обновленную информацию о ходе выполнения, необходимо использовать новый класс **CloudMediaContext** при каждом опросе по свойству Statistics.

В следующем примере демонстрируется опрос сущности IngestManifest по ее идентификатору **Id**.
	
	static void MonitorBulkManifest(string manifestID)
	{
	   bool bContinue = true;
	   while (bContinue)
	   {
	      CloudMediaContext context = GetContext();
	      IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();
	
	      if (manifest != null)
	      {
	         lock(consoleWriteLock)
	         {
	            Console.WriteLine("\nWaiting on all file uploads.");
	            Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
	            Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
	            Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);
	
	            if (manifest.Statistics.PendingFilesCount == 0)
	            {
	               Console.WriteLine("Completed\n");
	               bContinue = false;
	            }
	         }
	
	         if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
	            Thread.Sleep(60000);
	      }
	      else // Manifest is null
	         bContinue = false;
	   }
	}
	


##Передача файлов с помощью расширений пакета SDK для .NET 

В приведенном ниже примере показано, как передать один файл с помощью расширений пакета SDK для .NET. В этом случае используется метод **CreateFromFile**, но доступна также и асинхронная версия (**CreateFromFileAsync**). Метод **CreateFromFile** позволяет указать имя файла, параметр шифрования и обратный вызов, чтобы сообщать о ходе передачи файла.


	static public IAsset UploadFile(string fileName, AssetCreationOptions options)
	{
	    IAsset inputAsset = _context.Assets.CreateFromFile(
	        fileName,
	        options,
	        (af, p) =>
	        {
	            Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	        });
	
	    Console.WriteLine("Asset {0} created.", inputAsset.Id);
	
	    return inputAsset;
	}

В следующем примере вызывается функция UploadFile, а в качестве параметра создания ресурса указывается шифрование хранилища.


	var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);


##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##Дальнейшие действия
После загрузки актива в службы мультимедиа перейдите к статье [Получение процессора мультимедиа][].

[Получение процессора мультимедиа]: media-services-get-media-processor.md
 

<!---HONumber=AcomDC_0211_2016-->