<properties 
   pageTitle="Перемещение виртуальной машины или экземпляра роли в другую подсеть"
   description="Узнайте, как переместить виртуальные машины и экземпляры ролей в другую подсеть"
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

# Перемещение виртуальной машины или экземпляра роли в другую подсеть

С помощью PowerShell можно переместить виртуальные машины из одной подсети в другую в той же виртуальной сети (VNet). Экземпляры ролей можно перемещать путем редактирования CSCFG вместо использования PowerShell.

Зачем перемещать виртуальные машины в другую подсеть? Миграция между подсетями полезна в тех случаях, когда старая подсеть слишком мала и не может быть расширена из-за существующих работающих виртуальных машин в этой подсети. В этом случае можно создать другую подсеть большего размера и переместить виртуальные машины в новую подсеть, а затем после завершения миграции старую пустую подсеть можно удалить.

## Перемещение виртуальной машины в другую подсеть

Чтобы переместить виртуальную машину, запустите командлет PowerShell Set-AzureSubnet, используя приведенный ниже пример в качестве шаблона. В приведенном ниже примере выполняется перемещение TestVM из текущей подсети в подсеть Subnet-2. Не забудьте отредактировать пример в соответствии с вашей средой. Обратите внимание, что при каждом выполнении командлета Update-AzureVM в рамках процедуры он будет перезапускать виртуальную машину в рамках процесса обновления.

	Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
	| Set-AzureSubnet –SubnetNames Subnet-2 `
	| Update-AzureVM

Если для виртуальной машины задан статический DIP, необходимо будет удалить эту настройку перед перемещением виртуальной машины в новую подсеть. В этом случае используйте следующую команду:

	Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
	| Remove-AzureStaticVNetIP `
	| Update-AzureVM
	Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
	| Set-AzureSubnet -SubnetNames Subnet-2 `
	| Update-AzureVM

## Перемещение экземпляра роли в другую подсеть

Чтобы переместить экземпляр роли, измените файл CSCFG. В приведенном ниже примере выполняется перемещение роли Role0 в виртуальной сети *VNETName* из текущей подсети в подсеть *Subnet-2*. Поскольку экземпляр роли уже развернут, достаточно изменить имя подсети = Subnet-2. Не забудьте отредактировать пример в соответствии с вашей средой.

	<NetworkConfiguration>
	    <VirtualNetworkSite name="VNETName" />
	    <AddressAssignments>
	       <InstanceAddress roleName="Role0">
	            <Subnets><Subnet name="Subnet-2" /></Subnets>
	       </InstanceAddress>
	    </AddressAssignments>
	</NetworkConfiguration> 

<!---HONumber=AcomDC_1217_2015-->