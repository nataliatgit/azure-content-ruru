<properties 
	pageTitle="Управление базой данных SQL Azure с помощью PowerShell" 
	description="Управление ресурсами Базы данных SQL с PowerShell" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor="monicar"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2016" 
	ms.author="sstein"/>

# Управление базой данных SQL Azure с помощью PowerShell


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

В этой статье представлены команды PowerShell для выполнения различных задач Базы данных SQL Azure.


Чтобы выполнять командлеты PowerShell, необходимо установить и запустить Azure PowerShell. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).



## Настройка учетных данных

Для запуска командлетов PowerShell по подписке Azure необходимо сначала настроить доступ к учетной записи Azure. Выполните следующую команду; откроется окно входа, в котором необходимо ввести свои учетные данные. Используйте тот же адрес электронной почты и пароль, который вы используете для входа на классический портал Azure.

	Login-AzureRmAccount

После успешного входа на экране будут отображаться некоторые сведения, включая идентификатор, под которым вы вошли в систему, и подписки Azure, к которым у вас есть доступ.


## Выбор подписки Azure

Для выбора подписки вам понадобится идентификатор (**-SubscriptionId**) или имя подписки (**-SubscriptionName**). Идентификатор или имя можно скопировать из предыдущего шага или, если у вас несколько подписок, запустить командлет **Get-AzureSubscription** и скопировать необходимые сведения о подписке из набора результатов.

Выполните следующий командлет с данными о подписке для задания текущей подписки:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Следующие команды будет выполнены для заданной выше подписки.

## Создание группы ресурсов

Создайте группу ресурсов, которая будет содержать сервер. Следующую команду можно изменить, указав любое допустимое расположение.

Список допустимых расположений серверов баз данных SQL Azure можно получить, выполнив следующий командлет:

	$AzureSQLLocations = (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Sql'}).Locations

Если у вас уже есть группа ресурсов, вы можете перейти к следующему шагу или выполнить команду ниже, чтобы создать новую группу ресурсов:

	New-AzureRmResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## Создание сервера 

Чтобы создать новый сервер версии 12, воспользуйтесь командлетом [New-AzureRMSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx). Замените server12 на имя вашего сервера. Это имя должно быть уникальным для серверов Azure SQL Server, и, если оно уже занято, появится сообщение об ошибке. Кроме того, выполнение этой команды может занять несколько минут. После успешного создания сервера будут запрошены сведения о сервере и PowerShell. Команду можно изменить, указав любое допустимое расположение.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

При выполнении этой команды появится окно, запрашивающее **Имя пользователя** и **Пароль**. Это не учетные данные Azure; введите имя пользователя и пароль, которые станут учетными данными администратора нового сервера.

## Создание правила брандмауэра для сервера

Чтобы создать правило брандмауэра для доступа к серверу, воспользуйтесь командой [New-AzureRMSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx). Выполните следующую команду, заменив начальный и конечный IP-адреса значениями для своего клиента.

Если ваш сервер должен предоставлять доступ к другим службам Azure, добавьте параметр **-AllowAllAzureIPs**, который добавит специальное правило брандмауэра и предоставит всему трафику Azure доступ к серверу.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Дополнительные сведения см. в статье [Брандмауэр Базы данных SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).

## Создание базы данных SQL

Для создания базы данных используйте команду [New-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx). Для создания базы данных необходим сервер. В следующем примере создается база данных SQL, с именем TestDB12. Эта база данных создается с уровнем производительности Standard S1.

	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## Изменение уровня производительности базы данных SQL

Для масштабирования базы данных воспользуйтесь командой [Set-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx). В следующем примере база данных с именем TestDB12 масштабируется с текущего уровня производительности на уровень Standard S3.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## Удаление базы данных SQL

Для удаления базы данных SQL воспользуйтесь командой [Remove-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx). В следующем примере удаляется база данных SQL с именем TestDB12.

	Remove-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## Удаление сервера

Сервер также можно удалить командой [Remove-AzureRMSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx). В следующем примере удаляется сервер с именем server12.

	Remove-AzureRmSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"



Если в будущем вы планируете снова создавать такие же или аналогичные SQL-ресурсы Azure, можно выполнить одно из двух действий:

- сохранить блок командлетов как файл скрипта PowerShell (*.ps1);
- сохранить его как Runbook службы автоматизации Azure в разделе "Служба автоматизации" на классическом портале Azure. 

## Дальнейшие действия

Объедините и автоматизируйте команды. Например, замените все содержимое внутри кавычек, включая символы < and >, соответствующими значениями для создания сервера, правила брандмауэра и базы данных:


    New-AzureRmResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    New-AzureRmSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    New-AzureRmSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## Сопутствующая информация

- [Командлеты Базы данных SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx)

<!---HONumber=AcomDC_0224_2016-->