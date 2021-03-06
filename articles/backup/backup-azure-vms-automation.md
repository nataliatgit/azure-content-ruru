<properties
	pageTitle="Развертывание резервной копии виртуальной машины Azure с помощью PowerShell и управление ею | Microsoft Azure"
	description="Узнайте о том, как развернуть службу архивации Azure и управлять ею с помощью PowerShell"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/28/2016" ms.author="aashishr";"trinadhk" />


# Развертывание резервной копии виртуальной машины Azure с помощью PowerShell и управление ею
В этой статье объясняется, как использовать Azure PowerShell для резервного копирования и восстановления виртуальных машин Azure IaaS.

## Основные понятия

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

См. статью [Общие сведения о резервном копировании Azure IaaS](backup-azure-vms-introduction.md) в документации службы архивации Azure.

> [AZURE.WARNING] Перед началом работы убедитесь, что вам известны [предварительные требования](backup-azure-vms-prepare.md) для работы со службой архивации Azure и [ограничения](backup-azure-vms-prepare.md#limitations), применяемые к текущему решению для резервного копирования виртуальных машин.

Чтобы эффективно использовать PowerShell, необходимо понимать иерархию объектов и знать, откуда следует начинать резервное копирование.

![Иерархия объектов](./media/backup-azure-vms-automation/object-hierarchy.png)

Две самые важные процедуры включают в себя обеспечение защиты виртуальной машины и восстановление данных из точки восстановления. Эта статья поможет вам приобрести опыт в работе с командлетами PowerShell, чтобы обеспечить выполнение этих двух сценариев.


## Настройка и регистрация
Чтобы начать работу, сделайте следующее:

1. [Скачайте последнюю версию PowerShell](https://github.com/Azure/azure-powershell/releases) (минимальная требуемая версия: 1.0.0).

2. Выведите доступные командлеты PowerShell для службы архивации Azure, введя следующую команду.

```
PS C:\> Get-Command *azurermbackup*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmBackupItem                           1.0.1      AzureRM.Backup
Cmdlet          Disable-AzureRmBackupProtection                    1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupContainerReregistration        1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupProtection                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupContainer                         1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupItem                              1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJob                               1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJobDetails                        1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupRecoveryPoint                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVaultCredentials                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupRetentionPolicyObject             1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Register-AzureRmBackupContainer                    1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupProtectionPolicy               1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupVault                          1.0.1      AzureRM.Backup
Cmdlet          Restore-AzureRmBackupItem                          1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Stop-AzureRmBackupJob                              1.0.1      AzureRM.Backup
Cmdlet          Unregister-AzureRmBackupContainer                  1.0.1      AzureRM.Backup
Cmdlet          Wait-AzureRmBackupJob                              1.0.1      AzureRM.Backup
```

С помощью PowerShell можно автоматизировать указанные ниже задачи по настройке и регистрации.

- создать хранилище архивации;
- Регистрация виртуальной машины в службе архивации Azure

### создать хранилище архивации;

> [AZURE.WARNING] Для клиентов, использующих службу архивации Azure впервые, необходимо зарегистрировать поставщика службы архивации для использования с вашей подпиской. Для этого выполните следующую команду: Register-AzureRMResourceProvider -ProviderNamespace "Microsoft.Backup"

Вы можете создать новое хранилище архивации с помощью командлета **New-AzureRMBackupVault**. Хранилище архивов представляет собой ресурс ARM, поэтому вам потребуется разместить его в группе ресурсов. В консоли Azure PowerShell с повышенными привилегиями выполните следующие команды:

```
PS C:\> New-AzureRMResourceGroup –Name “test-rg” –Location “West US”
PS C:\> $backupvault = New-AzureRMBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

Чтобы получить список всех хранилищ службы архивации в данной подписке, используйте командлет **Get-AzureRMBackupVault**.

> [AZURE.NOTE] Объект хранилища службы архивации удобно хранить в переменной. Объект хранилища используется в качестве входных данных для многих командлетов службы архивации Azure.


### Регистрация виртуальных машин
Чтобы настроить резервное копирование с помощью службы архивации Azure, сначала зарегистрируйте свой компьютер или виртуальную машину в хранилище службы архивации Azure. Командлет **Register-AzureRMBackupContainer** принимает входные данные виртуальной машины Azure IaaS и регистрирует ее в указанном хранилище. Операция регистрации связывает виртуальную машину Azure с хранилищем службы архивации и отслеживает виртуальную машину на протяжении всего цикла резервного копирования.

Во время регистрации виртуальной машины в службе архивации Azure создается объект контейнера верхнего уровня. Контейнер обычно содержит несколько элементов, которые можно архивировать, но контейнер для виртуальной машины содержит только один элемент для резервного копирования.

```
PS C:\> $registerjob = Register-AzureRMBackupContainer -Vault $backupvault -Name "testvm" -ServiceName "testvm"
```

## Виртуальные машины службы архивации Azure

### Создание политики защиты
Чтобы начать резервное копирование виртуальных машин, создавать новую политику защиты не обязательно. Хранилище поставляется с "политикой по умолчанию", которую можно использовать для быстрого включения защиты. Позднее в эту политику можно внести сведения о правах. Список доступных в хранилище политик можно получить с помощью командлета **Get-AzureRMBackupProtectionPolicy**.

```
PS C:\> Get-AzureRMBackupProtectionPolicy -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DefaultPolicy             AzureVM            Daily              26-Aug-15 12:30:00 AM
```

> [AZURE.NOTE] Часовой пояс поля BackupTime в PowerShell — UTC. Однако при отображении времени резервного копирования на портале Azure часовой пояс выравнивается с локальной системой вместо со смещением от UTC.

Политика резервного копирования связана по крайней мере с одной политикой хранения. Политика хранения определяет продолжительность хранения точки восстановления в службе архивации Azure. Командлет **New-AzureRMBackupRetentionPolicy** создает объекты PowerShell, которые содержат сведения о политике хранения. Эти объекты политики хранения используются в качестве входных данных для командлета *New-AzureRMBackupProtectionPolicy* или используются непосредственно с командлетом *Enable-AzureRMBackupProtection*.

Политика резервного копирования определяет время и частоту резервного копирования элемента. Командлет **New-AzureRMBackupProtectionPolicy** создает объект PowerShell, который содержит сведения о политике резервного копирования. Политика архивации используется в качестве входных данных для командлета *Enable-AzureRMBackupProtection*.

```
PS C:\> $Daily = New-AzureRMBackupRetentionPolicyObject -DailyRetention -Retention 30
PS C:\> $newpolicy = New-AzureRMBackupProtectionPolicy -Name DailyBackup01 -Type AzureVM -Daily -BackupTime ([datetime]"3:30 PM") -RetentionPolicy ($Daily) -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DailyBackup01             AzureVM            Daily              01-Sep-15 3:30:00 PM
```

### Включить защиту
Защита включается для двух объектов: элемента и политики, оба объекта должны принадлежать одному хранилищу. После того как политика сопоставится с элементом, рабочий процесс резервного копирования будет срабатывать по определенному расписанию.

```
PS C:\> Get-AzureRMBackupContainer -Type AzureVM -Status Registered -Vault $backupvault | Get-AzureRMBackupItem | Enable-AzureRMBackupProtection -Policy $newpolicy
```

### Начальное резервное копирование
Расписание резервного копирования предполагает создание полной начальной копии элемента и добавочное копирование последующих резервных копий. Если вы хотите, чтобы начальная архивация была выполнена в определенное время или даже немедленно, используйте командлет **Backup-AzureRMBackupItem**.

```
PS C:\> $container = Get-AzureRMBackupContainer -Vault $backupvault -type AzureVM -name "testvm"
PS C:\> $backupjob = Get-AzureRMBackupItem -Container $container | Backup-AzureRMBackupItem
PS C:\> $backupjob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

> [AZURE.NOTE] Часовой пояс для полей StartTime и EndTime, отображаемый в PowerShell, — UTC. Однако при отображении подобной информации на портале Azure часовой пояс выравнивается с часами локальной системы.

### Наблюдение за выполнением задания резервного копирования
Большинство длительных операций службы архивации Azure моделируются в виде задания. Это упрощает отслеживание хода выполнения, при этом вам не нужно все время держать открытым портал Azure.

Чтобы получить последнее состояние выполняющегося задания, используйте командлет **Get-AzureRMBackupJob**.

```
PS C:\> $joblist = Get-AzureRMBackupJob -Vault $backupvault -Status InProgress
PS C:\> $joblist[0]

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

Вместо опроса этих заданий о ходе их выполнения, который требует выполнения дополнительного кода, проще использовать командлет **Wait-AzureRMBackupJob**. Если командлет используется в сценарии, он приостанавливает выполнение сценария до завершения задания или до достижения конкретного значения времени ожидания.

```
PS C:\> Wait-AzureRMBackupJob -Job $joblist[0] -Timeout 43200
```


## Восстановление виртуальной машины Azure

Чтобы восстановить данные резервной копии, необходимо определить архивный элемент и точку восстановления, которая содержит данные на определенный момент времени. Эти сведения предоставляются командлету AzureRMBackupItem, чтобы инициировать восстановление данных из хранилища в учетную запись клиента.

### Выбор виртуальной машины.

Чтобы получить объект PowerShell, определяющий правильный архивный элемент, необходимо начать с контейнера в хранилище и пройти постепенно вниз по иерархии объектов. Чтобы выбрать контейнер, который представляет виртуальную машину, используйте командлет **Get-AzureRMBackupContainer** и передайте найденный контейнер в командлет **Get-AzureRMBackupItem**.

```
PS C:\> $backupitem = Get-AzureRMBackupContainer -Vault $backupvault -Type AzureVM -name "testvm" | Get-AzureRMBackupItem
```

### Выбор точки восстановления

Теперь можно получить список всех точек восстановления для архивного элемента с помощью командлета **Get-AzureRMBackupRecoveryPoint** и выбрать точку восстановления. Обычно пользователи выбирают самую последнюю точку *AppConsistent* в списке.

```
PS C:\> $rp =  Get-AzureRMBackupRecoveryPoint -Item $backupitem
PS C:\> $rp

RecoveryPointId    RecoveryPointType  RecoveryPointTime      ContainerName
---------------    -----------------  -----------------      -------------
15273496567119     AppConsistent      01-Sep-15 12:27:38 PM  iaasvmcontainer;testvm;testv...
```

Переменная ```$rp``` — массив точек восстановления для выбранного архивного элемента, отсортированный по времени в обратном порядке, — последняя точка восстановления имеет индекс 0. Используйте стандартное индексирование массива PowerShell для выбора точки восстановления. Например: ```$rp[0]``` выбирает последнюю точку восстановления.

### Восстановление дисков

Операции восстановления, выполняемые на портале Azure и с помощью Azure PowerShell, существенно отличаются. При использовании PowerShell операция восстановления прекращается на восстановлении дисков и сведений о конфигурации из точки восстановления. Она не создает виртуальную машину.

> [AZURE.WARNING] Командлет Restore-AzureRMBackupItem не создает виртуальную машину. Он только восстанавливает диски в указанную учетную запись хранения. На портале Azure операция восстановления выполняется иначе.

```
PS C:\> $restorejob = Restore-AzureRMBackupItem -StorageAccountName "DestAccount" -RecoveryPoint $rp[0]
PS C:\> $restorejob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Restore         InProgress      01-Sep-15 1:14:01 PM   01-Jan-01 12:00:00 AM
```

Когда задание восстановления будет выполнено, вы можете получить сведения об операции восстановления с помощью командлета **Get-AzureRMBackupJobDetails**. Свойство *ErrorDetails* будет содержать сведения, необходимые для повторного создания виртуальной машины.

```
PS C:\> $restorejob = Get-AzureRMBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRMBackupJobDetails -Job $restorejob
```

### Сборка виртуальной машины

Вы можете собрать виртуальную машину из восстановленных дисков с помощью старых командлетов PowerShell диспетчера служб Azure, новых шаблонов диспетчера ресурсов Azure или даже с помощью портала Azure. В кратком примере мы покажем, как это сделать с помощью командлетов диспетчера служб Azure.

```
 $properties  = $details.Properties

 $storageAccountName = $properties["Target Storage Account Name"]
 $containerName = $properties["Config Blob Container Name"]
 $blobName = $properties["Config Blob Name"]

 $keys = Get-AzureStorageKey -StorageAccountName $storageAccountName
 $storageAccountKey = $keys.Primary
 $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey


 $destination_path = "C:\Users\admin\Desktop\vmconfig.xml"
 Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path -Context $storageContext


$obj = [xml](((Get-Content -Path $destination_path -Encoding UniCode)).TrimEnd([char]0x00))
 $pvr = $obj.PersistentVMRole
 $os = $pvr.OSVirtualHardDisk
 $dds = $pvr.DataVirtualHardDisks
 $osDisk = Add-AzureDisk -MediaLocation $os.MediaLink -OS $os.OS -DiskName "panbhaosdisk"
 $vm = New-AzureVMConfig -Name $pvr.RoleName -InstanceSize $pvr.RoleSize -DiskName $osDisk.DiskName

 if (!($dds -eq $null))
 {
	 foreach($d in $dds.DataVirtualHardDisk)
 	 {
		 $lun = 0;
		 if(!($d.Lun -eq $null))
		 {
	 		 $lun = $d.Lun
		 }
		 $name = "panbhadataDisk" + $lun
     Add-AzureDisk -DiskName $name -MediaLocation $d.MediaLink
     $vm | Add-AzureDataDisk -Import -DiskName $name -LUN $lun
	}
}

New-AzureVM -ServiceName "panbhasample" -Location "SouthEast Asia" -VM $vm
```

Дополнительные сведения о сборке виртуальной машины из восстановленных дисков см. в документации о следующих командлетах:

- [Add-AzureDisk](https://msdn.microsoft.com/library/azure/dn495252.aspx)
- [New-AzureVMConfig](https://msdn.microsoft.com/library/azure/dn495159.aspx)
- [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)

## Примеры кода

### 1\. Получение состояния выполнения подзадач

Чтобы отслеживать состояние выполнения отдельных подзадач, можно использовать командлет **Get-AzureRMBackupJobDetails**.

```
PS C:\> $details = Get-AzureRMBackupJobDetails -JobId $backupjob.InstanceId -Vault $backupvault
PS C:\> $details.SubTasks

Name                                                        Status
----                                                        ------
Take Snapshot                                               Completed
Transfer data to Backup vault                               InProgress
```

### 2\. Создание ежедневного или еженедельного отчета для задач резервного копирования

Администраторы обычно хотят знать, какие задачи резервного копирования запускались за последние 24 часа и каково состояние этих заданий резервного копирования. Кроме того, объем передаваемых данных позволяет администраторам оценить ежемесячное использование данных. Следующий сценарий извлекает необработанные данные из службы архивации Azure и отображает сведения в консоли PowerShell.

```
param(  [Parameter(Mandatory=$True,Position=1)]
        [string]$backupvaultname,

        [Parameter(Mandatory=$False,Position=2)]
        [int]$numberofdays = 7)


#Initialize variables
$DAILYBACKUPSTATS = @()
$backupvault = Get-AzureRMBackupVault -Name $backupvaultname
$enddate = ([datetime]::Today).AddDays(1)
$startdate = ([datetime]::Today)

for( $i = 1; $i -le $numberofdays; $i++ )
{
    # We query one day at a time because pulling 7 days of data might be too much
    $dailyjoblist = Get-AzureRMBackupJob -Vault $backupvault -From $startdate -To $enddate -Type AzureVM -Operation Backup
    Write-Progress -Activity "Getting job information for the last $numberofdays days" -Status "Day -$i" -PercentComplete ([int]([decimal]$i*100/$numberofdays))

    foreach( $job in $dailyjoblist )
    {
        #Extract the information for the reports
        $newstatsobj = New-Object System.Object
        $newstatsobj | Add-Member -type NoteProperty -name Date -value $startdate
        $newstatsobj | Add-Member -type NoteProperty -name VMName -value $job.WorkloadName
        $newstatsobj | Add-Member -type NoteProperty -name Duration -value $job.Duration
        $newstatsobj | Add-Member -type NoteProperty -name Status -value $job.Status

        $details = Get-AzureRMBackupJobDetails -Job $job
        $newstatsobj | Add-Member -type NoteProperty -name BackupSize -value $details.Properties["Backup Size"]
        $DAILYBACKUPSTATS += $newstatsobj
    }

    $enddate = $enddate.AddDays(-1)
    $startdate = $startdate.AddDays(-1)
}

$DAILYBACKUPSTATS | Out-GridView
```

Если вы хотите добавить в выходные данные этого отчета возможности построения графиков, обратитесь к статье [Построение графиков с помощью PowerShell](http://blogs.technet.com/b/richard_macdonald/archive/2009/04/28/3231887.aspx) в блоге TechNet.

<!---HONumber=AcomDC_0211_2016-->