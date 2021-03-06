<properties 
	pageTitle="Смоделированная гибридная облачная среда для тестирования | Microsoft Azure" 
	description="Создание смоделированной гибридной облачной среды для ИТ-специалистов или тестирования разработки с использованием двух виртуальных сетей Azure и подключения тип VNet-to-VNet." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2016" 
	ms.author="josephd"/>

# Создание имитации гибридной облачной среды для тестирования

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md).

В этом разделе описываются шаги по созданию смоделированной гибридной облачной среды с помощью Microsoft Azure, в которой можно выполнять тестирование с использованием двух виртуальных сетей Azure. Эту конфигурацию можно использовать как альтернативу [настройке гибридной облачной среды для тестирования](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md), если у вас нет прямого подключения к Интернету и свободного общедоступного IP-адреса. Это конфигурация, которая получается в результате.

![](./media/virtual-machines-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)

Они позволяет смоделировать гибридную облачную рабочую среду. В ее состав входит:

- смоделированная и упрощенная локальная сеть, размещенная в виртуальной сети Azure (виртуальная сеть TestLab).
- смоделированная виртуальная сеть с подключением между организациями, размещенная в Azure (TestVNET).
- подключение типа «VNet-to-VNet» между двумя виртуальными сетями.
- дополнительный контроллер домена в виртуальной сети TestVNET.

Это основа и общая начальная точка, на базе которой можно:

- разрабатывать и тестировать приложения в смоделированной гибридной облачной среде.
- создавать конфигурации тестов компьютеров в виртуальной сети TestLab и TestVNET для имитации гибридных облачных рабочих нагрузок ИТ-среды.

Существует четыре основных этапа настройки данной тестовой среды гибридного облака:

1.	настройка виртуальной сети TestLab.
2.	создание виртуальной сети между организациями.
3.	создание подключения типа «VNet-to-VNet».
4.	Настройка DC2. 

Если у вас еще нет подписки Azure, вы можете зарегистрироваться для получения бесплатной пробной версии на веб-сайте [Try Azure](https://azure.microsoft.com/pricing/free-trial/). При наличии подписки MSDN см. [Преимущества Azure для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Виртуальные машины и шлюзы виртуальных сетей в Azure создают текущие расходы во время своей работы. На эти затраты выставляется счет при использовании бесплатной пробной версии, подписки MSDN или платной подписки. Шлюз Azure VPN реализован как набор двух виртуальных машин Azure. Чтобы сократить расходы, создайте тестовую среду и как можно быстрее выполните необходимые тестирования и демонстрации.

## Этап 1. Настройка виртуальной сети TestLab

Используйте указания в статье [Тестовая среда с базовой конфигурацией](virtual-machines-base-configuration-test-environment-resource-manager.md), чтобы настроить в виртуальной сети Azure с именем TestLab компьютеры DC1, APP1 и CLIENT1.

Затем запустите командную строку Azure PowerShell.

> [AZURE.NOTE] Следующая команда задает использование Azure PowerShell 1.0 и более поздней версии. Дополнительные сведения см. в статье об Azure PowerShell 1.0.

Войдите в свою учетную запись.

	Login-AzureRMAccount

Получите имя своей подписки, выполнив указанную ниже команду.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Настройте свою подписку Azure. Используйте ту же подписку, которая использовалась для построения базовой конфигурации. Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Добавьте подсеть шлюза в виртуальную сеть TestLab базовой конфигурации, которая будет использоваться для размещения шлюза Azure.

	$rgName="<name of your resource group that you used for your TestLab virtual network>"
	$locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
	$vnet=Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name TestLab
	Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 10.255.255.248/29 -VirtualNetwork $vnet
	Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

Запросите общедоступный IP-адрес для назначения шлюзу для виртуальной сети TestLab.

	$gwpip=New-AzureRmPublicIpAddress -Name TestLab_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

Создайте шлюз.

	$vnet=Get-AzureRmVirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
	$gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name TestLab_GWConfig -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 
	New-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Имейте в виду, что процесс создания может занять 20 и более минут.

На портале Azure на локальном компьютере подключитесь к DC1, используя учетные данные CORP\\User1. Для настройки домена CORP таким образом, чтобы компьютеры и пользователи использовали свой локальный контроллер домена для проверки подлинности, выполните следующие команды из командной строки Windows PowerShell с правами администратора.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
	New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

Это текущая конфигурация.

![](./media/virtual-machines-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_1.png)
 
## Этап 2. Создание виртуальной сети TestVNET

Сначала создайте виртуальную сеть TestVNET и защитите ее с помощью группы безопасности сети.

	$rgName="<name of your resource group that you used for your TestLab virtual network>"
	$locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
	$locShortName="<Azure location name from $locName in all lowercase letters with spaces removed. Example:  westus>"
	$testSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix 192.168.0.0/24
	$gatewaySubnet=New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 192.168.255.248/29
	New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix 192.168.0.0/16 -Subnet $testSubnet,$gatewaySubnet –DNSServer 10.0.0.4
	$rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
	New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
	$vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name TestVNET
	$nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
	Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet" -AddressPrefix 192.168.0.0/24 -NetworkSecurityGroup $nsg

Затем запросите общедоступный IP-адрес для назначения шлюзу виртуальной сети TestVNET и создайте шлюз.

	$gwpip=New-AzureRmPublicIpAddress -Name TestVNET_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$vnet=Get-AzureRmVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
	$gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name "TestVNET_GWConfig" -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
	New-AzureRmVirtualNetworkGateway -Name "TestVNET_GW" -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Это текущая конфигурация.

![](./media/virtual-machines-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_2.png)
 
##Этап 3. Создание подключения между виртуальными сетями

Сначала получите случайный, надежно зашифрованный 32-значный общий ключ у администратора сети или администратора безопасности. Или см. статью [Создание случайной строки для общего ключа IPsec](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) со сведениями о получении общего ключа.

Затем создайте VPN-подключение типа "сеть-сеть", используя следующие команды. Это может занять некоторое время.

	$sharedKey="<pre-shared key value>"
	$gwTestLab=Get-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName
	$gwTestVNET=Get-AzureRmVirtualNetworkGateway -Name TestVNET_GW -ResourceGroupName $rgName
	New-AzureRmVirtualNetworkGatewayConnection -Name TestLab_to_TestVNET -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestLab -VirtualNetworkGateway2 $gwTestVNET -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey
	New-AzureRmVirtualNetworkGatewayConnection -Name TestVNET_to_TestLab -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestVNET -VirtualNetworkGateway2 $gwTestLab -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey

Подключение должно установиться через несколько минут. Обратите внимание, что на данный момент шлюзы и подключения, создаваемые с помощью диспетчера ресурсов Azure, не отображаются на портале Azure.

Это текущая конфигурация.

![](./media/virtual-machines-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_3.png)
 
## Этап 4. Настройка DC2

Во-первых, необходимо создать виртуальную машину Azure для DC2. Выполните следующие команды в командной строке Azure PowerShell на локальном компьютере.

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name for the base configuration>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
	$pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.0.4
	$vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Затем выполните вход в новую виртуальную машину DC2 с портала Azure.

Далее настройте правила брандмауэра Windows, чтобы разрешить трафик для тестирования базовых параметров подключения. Из командной строки Windows PowerShell с правами администратора на DC2 выполните следующие команды.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

В результате выполнения команды ping должны возвратиться четыре успешных ответа с IP-адреса 10.0.0.4. Это проверка трафика через подключение типа «Vnet-to-Vnet».

Далее следует добавить дополнительный диск данных как новый том с буквой диска F:.

1.	В левой области диспетчера серверов щелкните **Файловые службы и службы хранилища**, а затем щелкните **Диски**.
2.	В области содержимого в группе **Диски** щелкните **диск 2** (при этом для параметра **Раздел** должно быть задано значение **Неизвестный**).
3.	Щелкните **Задачи**, а затем **Новый том**.
4.	На странице «Перед началом работы» мастера создания томов щелкните **Далее**.
5.	На странице «Выбор сервера и диска» щелкните **Диск 2**, а затем щелкните **Далее**. При появлении запроса нажмите кнопку **OK**.
6.	На странице «Выбор размера тома» щелкните **Далее**.
7.	На странице «Назначение букве диска или папке» щелкните **Далее**.
8.	На странице «Выбор параметров файловой системы» щелкните **Далее**.
9.	На странице «Подтверждение выбора» щелкните **Создать**.
10.	После завершения нажмите кнопку **Закрыть**.

Затем настройте DC2 в качестве реплики контроллера домена для домена corp.contoso.com. Выполните следующие команды из командной строки Windows PowerShell на DC2.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Следует иметь в виду, что вам будет предложено ввести пароль CORP\\User1 и пароль режима восстановления служб каталогов (DSRM), а также перезапустить DC2.

Отметим, что виртуальная сеть TestVNET имеет собственный DNS-сервер(DC2). Необходимо настроить виртуальную сеть TestVNET для использования этого DNS-сервера.

1.	На левой панели портала Azure щелкните значок виртуальных сетей, а затем выберите **TestVNET**.
2.	На вкладке **Параметры** щелкните **DNS-серверы**.
3.	В поле **Основной DNS-сервер** введите **192.168.0.4**, чтобы заменить 10.0.0.4.
4.	Щелкните **Сохранить**.

Это текущая конфигурация.

![](./media/virtual-machines-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)
 
Смоделированная гибридная облачная среда готова для тестирования.

## Дальнейшие действия

- [Добавление новой виртуальной машины](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md) к подсети TestVNET, например к подсети, где работает Microsoft SQL Server.

<!---HONumber=AcomDC_0211_2016-->