<properties 
   pageTitle="Создание виртуальной машины с несколькими сетевыми адаптерами"
   description="Узнайте, как создавать и настраивать виртуальные машины с несколькими сетевыми адаптерами."
   services="virtual-network, virtual-machines"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" 
   tags="azure-service-management,azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

# Создание виртуальной машины с несколькими сетевыми адаптерами

Можно создать виртуальные машины в Azure и подключить к каждой из них несколько сетевых карт. Такая возможность необходима для целого ряда сетевых устройств (например, в составе решений для доставки приложений и оптимизации работы глобальной сети). Кроме того, поддержка нескольких сетевых карт обеспечивает доступ к дополнительным функциям управления сетевым трафиком, включая изоляцию трафика между интерфейсным и внутренним адаптерами либо разделение трафика данных и трафика управления.

![Несколько сетевых адаптеров на виртуальной машине](./media/virtual-networks-multiple-nics/IC757773.png)

На иллюстрации выше показана виртуальная машина с тремя сетевыми адаптерами, каждый из которых подключен к отдельной подсети.

## Требования и ограничения

В настоящее время с использованием нескольких сетевых адаптеров связаны перечисленные ниже требования и ограничения.

- Виртуальные машины с несколькими сетевыми адаптерами должны создаваться в виртуальных сетях Azure. Виртуальные машины за пределами таких сетей не поддерживаются. 
- В одной облачной службе (классические развертывания) или группе ресурсов (развертывание диспетчера ресурсов) допустимы только следующие параметры: 
	- Каждая виртуальная машина в облачной службе должна иметь несколько сетевых адаптеров. Или: 
	- На каждой виртуальной машине в облачной службе должен быть только один сетевой адаптер. 

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.
 
- Виртуальные IP-адреса с выходом в Интернет (классические развертывания) поддерживаются только на сетевых картах по умолчанию. IP-адресу сетевого интерфейса по умолчанию соответствует только один виртуальный IP-адрес. 
- В настоящее время для виртуальных машин с несколькими сетевыми картами не поддерживаются общедоступные IP-адреса уровня экземпляра (LPIP) (классические развертывания). 
- Порядок сетевых адаптеров внутри ВМ определяется случайным образом и может измениться по мере обновления инфраструктуры Azure. При этом соответствие между IP-адресами и MAC-адресами Ethernet не нарушится. Например предположим, что интерфейсу **Eth1** соответствует IP-адрес 10.1.0.100 и MAC-адрес 00-0D-3A-B0-39-0D; после обновления инфраструктуры Azure и перезагрузки название интерфейса может измениться на **Eth2**, однако пара IP- и MAC-адреса останется прежней. Если перезагрузка инициируется пользователем, порядок сетевых адаптеров не меняется. 
- Адрес каждого сетевого адаптера на каждой виртуальной машине должен быть расположен в подсети; нескольким сетевым адаптерам на одной виртуальной машине можно назначить адреса из одной подсети. 
- Количество сетевых адаптеров, которые можно создать для виртуальной машины, определяется ее размером. В таблице ниже указано количество сетевых адаптеров, соответствующих каждому из размеров. 

|Размер виртуальной машины (номера SKU уровня Standard)|Сетевые адаптеры (макс. количество на одной машине)|
|---|---|
|Все размеры Basic|1|
|A0\\очень малый|1|
|A1\\малый|1|
|A2\\средний|1|
|A3\\большой|2|
|A4\\очень большой|4\.|
|A5|1|
|A6|2|
|A7|4\.|
|A8|2|
|A9|4\.|
|A10|2|
|A11|4\.|
|D1|1|
|D2|2|
|D3|4\.|
|D4|8|
|D11|2|
|D12|4\.|
|D13|8|
|D14|8|
|DS1|1|
|DS2|2|
|DS3|4\.|
|DS4|8|
|DS11|2|
|DS12|4\.|
|DS13|8|
|DS14|8|
|D1\_v2|1|
|D2\_v2|2|
|D3\_v2|4\.|
|D4\_v2|8|
|D5\_v2|8|
|D11\_v2|2|
|D12\_v2|4\.|
|D13\_v2|8|
|D14\_v2|8|
|G1|1|
|G2|2|
|G3|4\.|
|G4|8|
|G5|8|
|Все остальные размеры|1|

## Группы безопасности сети (NSG)
В развертывании диспетчера ресурсов любая сетевая карта на виртуальной машине может быть связана с группой безопасности сети (NSG), включая сетевые карты на виртуальных машинах с поддержкой нескольких сетевых карт. Если сетевому адаптеру назначен адрес в подсети, которая связана с NSG, правила этой NSG распространяются и на соответствующий адаптер. С NSG можно связывать не только подсети, но и отдельные сетевые адаптеры.

Если подсеть связана с NSG, а сетевая карта в этой подсети также связана с NSG, то соответствующие правила NSG применяются **в порядке прохождения** трафика, передаваемого через сетевую карту в том или ином направлении.

- ****Входящий трафик**, направляемый на соответствующий сетевой адаптер, сначала проходит через подсеть, вызывая срабатывание правил NSG этой подсети, а затем поступает на адаптер, где к нему применяются правила NSG этого адаптера.
- **Исходящий трафик**, направляемый с соответствующего сетевого адаптера, сначала выходит из этого адаптера, вызывая срабатывание правил связанной с ним NSG, а затем проходит через подсеть, где к нему применяются правила NSG этой подсети. 

Узнайте больше о [группах безопасности сети](virtual-networks-nsg.md) и том, как они применяются для подсетей, виртуальных машин и сетевых карт.

## Как настроить виртуальную машину с несколькими сетевыми картами в классическом развертывания

С помощью приведенных ниже инструкций вы сможете создать виртуальную машину с тремя сетевыми адаптерами: одним по умолчанию и двумя дополнительными. На соответствующих этапах настройки создается виртуальная машина, параметры которой соответствуют приведенному ниже фрагменту файла конфигурации службы.

	<VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
	<AddressSpace>
	  <AddressPrefix>10.1.0.0/16</AddressPrefix>
	    </AddressSpace>
	    <Subnets>
	      <Subnet name="Frontend">
	        <AddressPrefix>10.1.0.0/24</AddressPrefix>
	      </Subnet>
	      <Subnet name="Midtier">
	        <AddressPrefix>10.1.1.0/24</AddressPrefix>
	      </Subnet>
	      <Subnet name="Backend">
	        <AddressPrefix>10.1.2.0/23</AddressPrefix>
	      </Subnet>
	      <Subnet name="GatewaySubnet">
	        <AddressPrefix>10.1.200.0/28</AddressPrefix>
	      </Subnet>
	    </Subnets>
	… Skip over the remainder section …
	</VirtualNetworkSite>


Для выполнения команд PowerShell из этого примера необходимы перечисленные ниже компоненты.

- Подписка Azure.
- Настроенная виртуальная сеть. Дополнительные сведения о виртуальных сетях см. в статье [Общие сведения о виртуальных сетях](virtual-networks-overview.md).
- Загруженная и установленная последняя версия Azure PowerShell. См. статью [Установка и настройка Azure PowerShell](../install-configure-powershell).

Чтобы создать виртуальную машину с несколькими сетевыми картами, сделайте следующее:

1. Выберите образ виртуальной машины в коллекции образов Azure. Обратите внимание на то, что образы часто меняются, а их доступность зависит от региона. Образ, указанный в примере ниже, может быть изменен либо недоступен в вашем регионе, поэтому выберите подходящий образ. 
	    
		$image = Get-AzureVMImage `
	    	-ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. Создайте конфигурацию виртуальной машины.

		$vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
			-Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. Создайте имя входа по умолчанию для администратора.

		Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
			-Password "<YourAdminPassword>"

1. Добавьте в конфигурацию виртуальной машины дополнительные сетевые адаптеры.

		Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
			-SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm 
		Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
			-SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. Укажите подсеть и IP-адрес сетевого адаптера по умолчанию.

		Set-AzureSubnet -SubnetNames "Frontend" -VM $vm 
		Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm

1. Создайте виртуальную машину в своей виртуальной сети.

		New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE] Указанная здесь виртуальная сеть должна уже существовать (это оговорено в необходимых условиях). В примере ниже задана виртуальная сеть **MultiNIC-VNet**.

## Доступ к другим подсетям с использованием дополнительной сетевой карты

В настоящее время все сетевые карты виртуальной машины в Azure настраиваются для использования шлюза по умолчанию. Благодаря этому сетевые карты могут взаимодействовать с IP-адресами за пределами своей подсети. В операционных системах, которые используют слабую модель маршрутизации узла, например Linux, при использовании для исходящего и входящего трафика разных сетевых карт подключение к Интернету прерывается.

Чтобы устранить эту проблему, в начале июля 2015 года Azure выпускает обновление платформы, которое позволит изменить настройки и не использовать для дополнительных сетевых карт шлюз по умолчанию. Это не повлияет на существующие виртуальные машины, пока они не будут перезагружены. После перезагрузки новые параметры вступят в силу, в результате чего поток трафика дополнительных сетевых карт будет ограничиваться одной подсетью. Если требуется активировать взаимодействие сетевых карт вне собственный подсети, необходимо добавить запись в таблицу маршрутизации для настройки шлюза, как описано ниже.

### Настройка виртуальных машин Windows

Предположим, что у вас есть виртуальная машина Windows с двумя сетевыми картами со следующими адресами:

- IP-адрес основной сетевой карты: 192.168.1.4;
- IP-адрес дополнительной сетевой карты: 192.168.2.5.

Таблица маршрутов IPv4 для этой виртуальной машины будет выглядеть следующим образом:

	IPv4 Route Table
	===========================================================================
	Active Routes:
	Network Destination        Netmask          Gateway       Interface  Metric
	          0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
	        127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
	        127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
	  127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
	    168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
	      192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
	      192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
	    192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
	      192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
	      192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
	    192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
	        224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
	        224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
	        224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
	  255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
	  255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
	  255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
	===========================================================================

Обратите внимание, что маршрут по умолчанию (0.0.0.0) доступен только для основной сетевой карты. Дополнительная сетевая карта не сможет получать доступ к ресурсам вне своей подсети, как показано ниже:

	C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
	 
	Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
	PING: transmit failed. General failure.
	PING: transmit failed. General failure.
	PING: transmit failed. General failure.
	PING: transmit failed. General failure.

Чтобы добавить для дополнительной сетевой карты маршрут по умолчанию, сделайте следующее:

1. Выполните следующую команду в командной строке, чтобы определить номер индекса для дополнительной сетевой карты:

		C:\Users\Administrator>route print
		===========================================================================
		Interface List
		 29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
		 27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
		  1...........................Software Loopback Interface 1
		 14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
		 20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
		===========================================================================

2. Обратите внимание на вторую запись в таблице с индексом 27 (в данном примере).
3. Выполните в командной строке команду **route add**, как показано ниже. В этом примере для дополнительной сетевой карты указывается шлюз по умолчанию с IP-адресом 192.168.2.1:

		route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27

4. Для проверки связи откройте командную строку и попытайтесь проверить связь с другой подсетью с использованием дополнительной сетевой карты, как показано в примере ниже:

		C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
		 
		Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
		Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
		Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
		Reply from 192.168.1.7: bytes=32 time<1ms TTL=128

5. Вы можете также просмотреть таблицу маршрутов, чтобы проверить новый добавленный маршрут, как показано ниже:

		C:\Users\Administrator>route print

		...

		IPv4 Route Table
		===========================================================================
		Active Routes:
		Network Destination        Netmask          Gateway       Interface  Metric
		          0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
		          0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
		        127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### Настройка виртуальных машин Linux

Так как для виртуальных машин Linux по умолчанию используется слабая модель маршрутизации узла, мы советуем ограничивать поток трафика сетевых карт той же подсетью. Однако, если в определенных сценариях требуется подключение вне подсети, необходимо включить маршрутизацию на основе политики, чтобы для входящего и исходящего трафика использовалась одна сетевая карта.

## Дальнейшие действия

- Развертывание [виртуальных машин с несколькими сетевыми картами в сценарии 2-уровневого приложения в развертывании диспетчера ресурсов](virtual-network-deploy-multinic-arm-template.md).
- Развертывание [виртуальных машин с несколькими сетевыми картами в сценарии 2-уровневого приложения в классическом развертывании](virtual-network-deploy-multinic-classic-ps.md).

<!---HONumber=AcomDC_0211_2016-->