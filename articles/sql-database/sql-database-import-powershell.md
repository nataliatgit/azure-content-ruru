<properties 
    pageTitle="Импорт BACPAC-файла для создания новой базы данных SQL Azure с помощью PowerShell | Microsoft Azure" 
    description="Импорт BACPAC-файла для создания новой базы данных SQL Azure с помощью PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="02/05/2016"
    ms.author="sstein"/>

# Импорт BACPAC-файла для создания новой базы данных SQL Azure с помощью PowerShell

**Отдельная база данных**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

В этой статье приведены указания о том, как создать базу данных SQL Azure, импортировав BACPAC-файл с помощью PowerShell.

BACPAC-файл — это файл с расширением .bacpac, который содержит схему базы данных и данные. Дополнительные сведения см. в разделе «Пакет резервного копирования (BACPAC)» в статье [Приложения уровня данных](https://msdn.microsoft.com/library/ee210546.aspx).

База данных создается на основе BACPAC-файла, импортированного из контейнера больших двоичных объектов хранилища Azure. Если в службе хранилища Azure нет BACPAC-файла, его можно создать, выполнив одно из действий, описанных в статье [Создание и экспорт BACPAC-файла из базы данных SQL Azure](sql-database-export-powershell.md).

> [AZURE.NOTE] База данных SQL Azure автоматически создает и обслуживает резервные копии для каждой пользовательской базы данных, которую можно восстановить. Дополнительные сведения см. в статье [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md).


Чтобы импортировать базу данных SQL, необходимо следующее.

- Подписка Azure. Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.
- BACPAC-файл базы данных, которую вы хотите импортировать. BACPAC-файл должен находиться в контейнере больших двоичных объектов [учетной записи хранения Azure (классика)](storage-create-storage-account.md).


> [AZURE.IMPORTANT] Эта статья содержит команды для Azure PowerShell версии вплоть до 1.0, *но не включая* саму версию 1.0 и более поздние версии. Используемую версию Azure PowerShell можно проверить с помощью команды **Get-Module azure | format-table version**.



## Настройка учетных данных и выбор подписки

Для начала установите доступ к учетной записи Azure. Для этого запустите PowerShell и выполните указанный ниже командлет. На экране входа в систему укажите те же адрес электронной почты и пароль, которые используются для входа на портал Azure.

	Add-AzureAccount

После успешного входа на экране будут отображаться некоторые сведения, включая идентификатор, под которым вы вошли в систему, и подписки Azure, к которым у вас есть доступ.


### Выбор подписки Azure

Для выбора подписки вам нужно знать ее идентификатор. Идентификатор подписки можно скопировать из данных, которые были показаны на предыдущем этапе. Если у вас несколько подписок и вам нужны дополнительные данные, выполните командлет **Get-AzureSubscription** и скопируйте нужные данные из полученных результатов. Получив идентификатор подписки, выполните следующий командлет:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

После успешного выполнения командлета **Select-AzureSubscription** вы вернетесь в командную строку PowerShell. Если у вас несколько подписок, выполните командлет **Get-AzureSubscription** и убедитесь в том, что в выбранной подписке отображается **IsCurrent: True**.


## Настройка переменных для среды

В некоторых переменных приведенные для примера значения необходимо заменить на значения, соответствующие вашей базе данных и учетной записи хранения.

В качестве имени сервера необходимо указать сервер, который уже существует в подписке, выбранной на предыдущей этапе, и на котором вы хотите создать базу данных.

Имя базы данных — это желаемое новой базы данных.

    $ServerName = "servername"
    $DatabaseName = "databasename"


Указанные ниже переменные взяты из учетной записи хранения, где находится BACPAC-файл. Чтобы получить эти значения, перейдите к учетной записи хранения на [портале Azure](https://portal.azure.com). Чтобы найти первичный ключ доступа, последовательно щелкните **Все параметры** и **Ключи** в колонке учетной записи хранения.

Имя большого двоичного объекта — это имя существующего BACPAC-файла, на основе которого вы хотите создать базу данных. Укажите расширение BACPAC.

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"

## Создание указателя на сервер и учетную запись хранения

После выполнения командлета **Get-Credential** откроется окно с запросом имени пользователя и пароля. Введите имя входа и пароль администратора сервера SQL, на котором вы хотите создать базу данных ($ServerName в приведенном выше примере), а не имя пользователя и пароль вашей учетной записи Azure.

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## Импорт базы данных

Эта команда отправляет в службу запрос об импорте базы данных. Операция импорта может занять некоторое время в зависимости от размера базы данных.

    $importRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    

## Отслеживание хода выполнения операции

После выполнения командлета **Start-AzureSqlDatabaseImport** вы можете проверить состояние запроса.

Обычно при проверке состояния сразу после запроса возвращается значение **Ожидание** или **Выполняется** и указывается текущий процент выполнения, так что командлет может выдать результат **Состояние: завершено** не с первого раза.

Для выполнения этой команды потребуется пароль. Введите имя входа и пароль администратора для сервера SQL.


    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
 


## Сценарий импорта базы данных SQL с помощью PowerShell


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "nameofnewdatabase"

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $ImportRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    

## Дальнейшие действия

- [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md)




## Дополнительные ресурсы

- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [Отработка аварийного восстановления](sql-database-disaster-recovery-drills.md)
- [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0211_2016-->