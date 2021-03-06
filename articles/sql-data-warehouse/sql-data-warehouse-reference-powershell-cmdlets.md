<properties
   pageTitle="Использование командлетов PowerShell и интерфейсов REST API при работе с хранилищем данных SQL"
   description="Приостановка и перезапуск хранилища данных SQL с помощью командлетов PowerShell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/22/2016"
   ms.author="barbkess;mausher;sonyama"/>

# Использование командлетов PowerShell и интерфейсов REST API при работе с хранилищем данных SQL

Хранилищем данных SQL вы можете управлять с помощью командлетов Azure PowerShell или интерфейсов REST API.

Команды, заданные для **базы данных SQL Azure**, используются также для **хранилища данных SQL**. Текущий список см. в статье [Командлеты SQL Azure](https://msdn.microsoft.com/library/mt574084.aspx). Командлеты **Suspend-AzureRmSqlDatabase** и **Resume-AzureRmSqlDatabase** (ниже) — это дополнения, предназначенные для хранилища данных SQL.

Точно так же интерфейсы REST API для **базы данных SQL Azure** можно использовать для экземпляров **хранилища данных SQL**. Текущий список см. в статье [Операции для баз данных Azure SQL](https://msdn.microsoft.com/library/azure/dn505719.aspx).

## Получение и запуск командлетов Azure PowerShell

1. Чтобы загрузить модуль Azure PowerShell, запустите [установщик веб-платформы Майкрософт](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). 
2. Чтобы запустить модуль, введите в окне запуска **Windows PowerShell**.
3. Если ваша учетная запись не добавлена на компьютер, запустите приведенный ниже командлет. (Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell]()).

	```
	Login-AzureRmAccount
	```

3. Выберите подписку на базу данных, которую нужно приостановить или возобновить. В данном случае выбирается подписка с именем "MySubscription".

	```
	Select-AzureRmSubscription -SubscriptionName "MySubscription"
	```

## Suspend-AzureRmSqlDatabase

Справку по командам см. в разделе [Suspend-AzureRmSQLDatabase](https://msdn.microsoft.com/library/mt619337.aspx).

### Пример 1. Приостановка базы данных по имени на сервере

Данный пример приостанавливает базу данных с именем Database02, размещенную на сервере с именем Server01. Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

```
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
```

### Пример 2. Приостановка объекта базы данных

В этом примере извлекается база данных с именем Database02 с сервера Server01, который находится в группе ресурсов с именем ResourceGroup1. Полученный объект передается командлету **Suspend-AzureRmSqlDatabase**. В результате база данных приостанавливается. Последняя команда отображает результаты.

```
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## Resume-AzureSqlDatabase

Справку по командам см. в разделе [Resume-AzureRmSQLDatabase](https://msdn.microsoft.com/library/mt619347.aspx).

### Пример 1. Возобновление базы данных по имени на сервере

Данный пример возобновляет работу базы данных с именем Database02, размещенную на сервере с именем Server01. Сервер находится в группе ресурсов с именем ResourceGroup1.

```
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" -DatabaseName "Database02"
```

### Пример 2. Возобновление объекта базы данных

В этом примере извлекается база данных с именем Database02 с сервера Server01, который находится в группе ресурсов с именем ResourceGroup1. Объект передается командлету **Resume-AzureRmSqlDatabase**.

```
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

## Get-AzureRmSqlDatabaseRestorePoints

Этот командлет выводит точки восстановления с резервных копий для базы данных хранилища Azure SQL. Точки восстановления используются для восстановления базы данных. Далее перечислены свойства для возвращенного объекта.

Свойство|Описание
---|---
RestorePointType|ДИСКРЕТНЫЕ И НЕПРЕРЫВНЫЕ ТОЧКИ ВОССТАНОВЛЕНИЯ. Дискретные точки восстановления — это возможные точки во времени, до которых можно восстановить базу данных хранилища Azure SQL. Непрерывные точки восстановления — это самые ранние возможные точки во времени, до которых можно восстановить базу данных Azure SQL. Базу данных можно восстановить до любой точки во времени после самой ранней точки.
EarliestRestoreDate|Самое раннее время восстановления (заполняется, если свойство restorePointType непрерывно)
RestorePointCreationDate |Время создания моментального снимка резервной копии (заполняется, если свойство restorePointType дискретно)

### Пример 1. Получение точки восстановления базы данных по имени на сервере
В этом примере извлекаются точки восстановления для базы данных с именем Database02 с сервера с именем Server01, который находится в группе ресурсов с именем ResourceGroup1.

```	
$restorePoints = Get-AzureRmSqlDatabaseRestorePoints –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
$restorePoints
```


### Пример 2. Возобновление объекта базы данных

В этом примере извлекается база данных с именем Database02 с сервера с именем Server01, который находится в группе ресурсов с именем ResourceGroup1. Объект базы данных передается командлету **Get-AzureRmSqlDatabase**, и в результате мы получаем точки восстановления базы данных. Последняя команда выводит результаты на печать.

```
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
$restorePoints = $database | Get-AzureRmSqlDatabaseRestorePoints
$retorePoints
```


> [AZURE.NOTE] Обратите внимание, что если вашим сервером является foo.database.windows.net, в командлетах PowerShell в качестве -ServerName используйте значение "foo".


## Дальнейшие действия
Дополнительные справочные сведения см. в обзоре [Общие справочные сведения о хранилище данных SQL][].

<!--Image references-->

<!--Article references-->
[Общие справочные сведения о хранилище данных SQL]: sql-data-warehouse-overview-reference.md
[How to install and configure Azure PowerShell]: ../articles/powershell-install-configure.md

<!--MSDN references-->


<!--Other Web references-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!---HONumber=AcomDC_0224_2016-->