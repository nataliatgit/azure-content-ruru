<properties 
   pageTitle="Как подключить виртуальные сети ARM к классическим виртуальным сетям в Azure"
   description="Узнайте, как создать VPN-подключение между классическими виртуальными сетями и новыми виртуальными сетями."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# Подключение классических виртуальных сетей к новым виртуальным сетям

Сейчас в Azure есть два режима управления: Azure Service Manager (классический) и диспетчер ресурсов Azure (ARM). Если вы использовали Azure какое-то время, вероятно, у вас есть виртуальные машины и роли экземпляров Azure, выполняемые в классический виртуальной сети. А более новые виртуальные машины и экземпляры ролей могут выполняться в виртуальной сети, созданной ARM.

В таких ситуациях необходимо убедиться, что новая инфраструктура может взаимодействовать с классическими ресурсами. Это можно сделать, создав VPN-подключение между двумя виртуальными сетями.

В этой статье вы узнаете, как создать VPN-подключение типа «сеть-сеть» (S2S) между классической виртуальной сетью и виртуальной сетью ARM.

>[AZURE.NOTE]Предполагается, что у вас уже есть классические виртуальные сети и виртуальные сети ARM и что вы знакомы с настройкой VPN-подключения S2S для классических виртуальных сетей. Чтобы ознакомиться с подробным описанием комплексного решения для VPN-подключения S2S между классической виртуальной сетью и виртуальной сетью ARM, см. статью [Руководство по решению: подключение классической виртуальной сети и виртуальной сети ARM с помощью VPN-подключения S2S](../virtual-networks-arm-asm-s2s.md).

Ниже приведен обзор задач для создания VPN-подключения S2S между классической виртуальной сетью и виртуальной сетью ARM с помощью шлюзов Azure.

1\. [Создание VPN-шлюза для классической виртуальной сети](#Step-1:-Create-a-VPN-gateway-for-the-classic-VNet).

2\. [Создание VPN-шлюза для виртуальной сети ARM](#Step-2:-Create-a-VPN-gateway-for-the-ARM-VNet).

3\. [Создание подключения между шлюзами](#Step-3:-Create-a-connection-between-the-gateways).

## Шаг 1. Создание VPN-шлюза для классической виртуальной сети

Чтобы создать VPN-шлюз для классической виртуальной сети, следуйте приведенным ниже указаниям.

1. Откройте классический портал по ссылке https://manage.windowsazure.com и введите учетные данные, если необходимо.
2. В нижнем левом углу экрана нажмите кнопку **СОЗДАТЬ**, затем щелкните **СЕТЕВЫЕ СЛУЖБЫ**, **ВИРТУАЛЬНЫЕ СЕТИ** и **ДОБАВИТЬ ЛОКАЛЬНУЮ СЕТЬ**.
3. В окне **Укажите сведения о локальной сети** введите имя для виртуальной сети ARM, к которой необходимо подключиться, а затем в правом нижнем углу окна нажмите кнопку со стрелкой.
3. В текстовом поле **НАЧАЛЬНЫЙ IP-АДРЕС** адресного пространства введите префикс сети для виртуальной сети ARM, к которой необходимо подключиться. 
4. В раскрывающемся списке **CIDR (число адресов)** выберите количество битов для сетевой части блока CIDR, используемого виртуальной сетью ARM, к которой необходимо подключиться.
5. В поле **IP-АДРЕС УСТРОЙСТВА VPN (НЕОБЯЗАТЕЛЬНО)** введите любой допустимый общедоступный IP-адрес. Позже мы изменим этот IP-адрес. Щелкните кнопку с флажком в правой нижней части экрана. На рисунке ниже показан пример параметров для этой страницы.

	![Параметры локальной сети](..\virtual-network\media\virtual-networks-arm-asm-s2s-howto\figurex1.png)

5. На странице **Сети** щелкните **ВИРТУАЛЬНЫЕ СЕТИ**, затем щелкните свою классическую виртуальную сеть и щелкните **НАСТРОЙКА**.
6. В разделе **Подключение типа "сеть-сеть" ** установите флажок **Подключиться к локальной сети**.
7. Выберите локальную сеть, созданную на шаге 4, из перечня доступных сетей в раскрывающемся списке **ЛОКАЛЬНАЯ СЕТЬ** и щелкните **СОХРАНИТЬ**.
8. После сохранения параметров щелкните **ПАНЕЛЬ МОНИТОРИНГА**, затем в нижней части страницы щелкните **СОЗДАТЬ ШЛЮЗ**, затем — **ДИНАМИЧЕСКАЯ МАРШРУТИЗАЦИЯ** и нажмите кнопку **ДА**.
9. Подождите, пока шлюз не будет создан, и скопируйте его общедоступный IP-адрес. Он потребуется для настройки шлюза в виртуальной сети ARM.

## Шаг 2. Создание VPN-шлюза для виртуальной сети ARM

Чтобы создать VPN-шлюз для виртуальной сети ARM, следуйте приведенным ниже указаниям.

1. С помощью консоли PowerShell переключитесь в режим ARM, выполнив следующую команду.

		Switch-AzureMode AzureResourceManager

2. Создайте локальную сеть, выполнив следующую команду. В локальной сети необходимо использовать блок CIDR классической виртуальной сети, к которой необходимо подключиться, и общедоступный IP-адрес шлюза, созданного на шаге 1 ранее.

		New-AzureLocalNetworkGateway -Name VNetClassicNetwork `
			-Location "East US" -AddressPrefix "10.0.0.0/20" `
			-GatewayIpAddress "168.62.190.190" -ResourceGroupName RG1

3. Создайте общедоступный IP-адрес шлюза, выполнив следующую команду.

		$ipaddress = New-AzurePublicIpAddress -Name gatewaypubIP`
			-ResourceGroupName RG1 -Location "East US" `
			-AllocationMethod Dynamic

4. Получите подсеть, используемую для шлюза, выполнив следующую команду.

		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name GatewaySubnet `
			-VirtualNetwork (Get-AzureVirtualNetwork -Name VNetARM -ResourceGroupName RG1) 

	>[AZURE.IMPORTANT]Подсеть шлюза уже должна существовать и иметь имя GatewaySubnet.

5. Создайте объект конфигурации IP для шлюза, выполнив следующую команду. Обратите внимание на идентификатор подсети шлюза. Эта подсеть должна находиться в виртуальной сети.

		$ipconfig = New-AzureVirtualNetworkGatewayIpConfig `
			-Name ipconfig -PrivateIpAddress 10.1.2.4 `
			-SubnetId $subnet.id -PublicIpAddressId $ipaddress.id

	>[AZURE.IMPORTANT]В параметры *SubnetId* и *PublicIpAddressId* должны передаваться объекты свойства идентификатора из подсети и IP-адреса, соответственно. Нельзя использовать простую строку.
	
5. Создайте шлюз виртуальной сети ARM, выполнив следующую команду.

		New-AzureVirtualNetworkGateway -Name v1v2Gateway -ResourceGroupName RG1 `
			-Location "East US" -GatewayType Vpn -IpConfigurations $ipconfig `
			-EnableBgp $false -VpnType RouteBased

6. После создания VPN-шлюза получите его общедоступный IP-адрес, выполнив следующую команду. Скопируйте IP-адрес. Он потребуется для настройки локальной сети для классической виртуальной сети.

		Get-AzurePublicIpAddress -Name gatewaypubIP -ResourceGroupName RG1

## Шаг 3. Создание подключения между шлюзами

1. Откройте классический портал по ссылке https://manage.windowsazure.com и введите учетные данные, если необходимо.
2. На классическом портале прокрутите страницу вниз и щелкните **СЕТИ**, затем щелкните **ЛОКАЛЬНЫЕ СЕТИ** и выберите виртуальную сеть ARM, к которой необходимо подключиться, после чего нажмите кнопку **ИЗМЕНИТЬ**.
3. В поле **IP-АДРЕС УСТРОЙСТВА VPN (НЕОБЯЗАТЕЛЬНО)** введите IP-адрес шлюза виртуальной сети ARM, полученный на шаге 2 ранее, затем в нижнем правом углу щелкните стрелку вправо и нажмите кнопку с флажком.
4. С помощью консоли PowerShell переключитесь в режим Azure Service Manager, выполнив следующую команду.

		Switch-AzureMode AzureServiceManager

5. Настройте общий ключ, выполнив следующую команду. Обязательно замените имена виртуальных сетей именами своих сетей.

		Set-AzureVNetGatewayKey -VNetName VNetClassic `
			-LocalNetworkSiteName VNetARM -SharedKey abc123

6. С помощью консоли PowerShell переключитесь в режим диспетчера ресурсов Azure, выполнив следующую команду.

		Switch-AzureMode AzureResourceManager

7. Создайте VPN-подключение, выполнив приведенные ниже команды.

		$vnet01gateway = Get-AzureLocalNetworkGateway -Name VNetClassic -ResourceGroupName RG1
		$vnet02gateway = Get-AzureVirtualNetworkGateway -Name v1v2Gateway -ResourceGroupName RG1
		
		New-AzureVirtualNetworkGatewayConnection -Name arm-asm-s2s-connection `
			-ResourceGroupName RG1 -Location "East US" -VirtualNetworkGateway1 $vnet02gateway `
			-LocalNetworkGateway2 $vnet01gateway -ConnectionType IPsec `
			-RoutingWeight 10 -SharedKey 'abc123'

## Дальнейшие действия

- Узнайте больше о [поставщике сетевых ресурсов (NRP) для ARM](../resource-groups-networking.md).
- Создание [комплексного решения для подключения классической виртуальной сети к виртуальной сети ARM с помощью VPN-подключения S2S](../virtual-networks-arm-asm-s2s.md).

<!---HONumber=AcomDC_1217_2015-->