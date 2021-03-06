<properties
	pageTitle="Автоматическое масштабирование в масштабируемых наборах виртуальных машин | Microsoft Azure"
	description="Приступите к созданию своего первого масштабируемого набора виртуальных машин Azure и научитесь им управлять с помощью Azure CLI"
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="davidmu"/>

# Автоматическое масштабирование машин в масштабируемом наборе виртуальных машин

> [AZURE.SELECTOR]
- [Azure CLI](virtual-machines-vmss-walkthrough-cli.md)
- [Azure PowerShell](virtual-machines-vmss-walkthrough.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.

Масштабируемые наборы виртуальных машин упрощают развертывание идентичных виртуальных машин в виде набора и управление ими. Масштабируемые наборы обеспечивают высокую степень масштабируемости и персонализацию уровня вычислений для гипермасштабируемых приложений. Кроме того, они поддерживают образы платформ Windows и Linux, а также пользовательские образы и расширения. Дополнительные сведения о масштабируемых наборах см. в статье [Масштабируемые наборы виртуальных машин](virtual-machines-vmss-overview.md).

В этом учебнике показано, как создать масштабируемый набор виртуальных машин Linux с помощью последней исправленной версии Ubuntu Linux и автоматически масштабировать машины в наборе. Для этого нужно создать шаблон диспетчера ресурсов Azure и развернуть его с помощью Azure PowerShell. Дополнительную информацию о шаблонах см. в статье [Создание шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md).

Шаблон, создаваемый в этом руководстве, похож на шаблон, который можно найти в коллекции шаблонов. Дополнительные сведения см. в статье [Развертывание простого масштабируемого набора виртуальных машин с помощью виртуальных машин Linux и Jumpbox](https://azure.microsoft.com/documentation/templates/201-vmss-linux-jumpbox/).

Перед началом выполнения действий в этом учебнике [установите Azure CLI](../xplat-cli-install.md).

[AZURE.INCLUDE [virtual-machines-vmss-preview-cli](../../includes/virtual-machines-vmss-preview-cli-include.md)]

## Шаг 1. Создание группы ресурсов и учетной записи хранения

1. **Вход в Microsoft Azure** — в интерфейсе командной строки (Bash, терминал, командная строка) убедитесь, что вы находитесь в режиме диспетчера ресурсов, введя `azure config mode arm`, а затем [войдите с рабочим или учебным идентификатором](../xplat-cli-connect.md#use-the-log-in-method), введя `azure login` и следуя указаниям для интерактивного входа в учетную запись Azure.

	> [AZURE.NOTE] Если вы знаете, что двухфакторная проверка подлинности не включена, но имеется рабочий или учебный идентификатор, вы можете войти альтернативным способом, используя рабочий или учебный идентификатор в комбинации с `azure login -u`. Если у вас нет рабочего или учебного идентификатора, его можно [создать в личной учетной записи Майкрософт](resource-group-create-work-id-from-personal.md).

2. **Создайте группу ресурсов**. Все ресурсы должны быть развернуты в группе ресурсов. В этом руководстве назовем группу ресурсов **vmsstest1**.

	```
	azure group create vmsstestrg1 westus
	```

3. **Разверните учетную запись хранения в новой группе ресурсов**. В этом руководстве используется несколько учетных записей для упрощения работы с масштабируемым набором виртуальных машин. Создайте учетную запись хранения с именем **vmsstestsa**. Не закрывайте окно команд интерфейса для выполнения дальнейших шагов этого учебника:

	```
	azure storage account create --type LRS -g vmsstestrg1 -l westus vmsstestsa
	```

## Шаг 2. Создание шаблона
С помощью шаблона диспетчера ресурсов Azure можно развертывать ресурсы Azure и управлять ими совокупно, используя JSON-описание ресурсов и связанные параметры развертывания.

1. В любом текстовом редакторе создайте файл C:\\VMSSTemplate.json и добавьте начальную структуру JSON для поддержки шаблона.

	```
	{
		"$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
		}
		"variables": {
		}
		"resources": [
		]
	}
	```

2. Параметры не всегда обязательны, но они упрощают управление шаблонами. Они указывают способ определения значений для шаблона, описывают тип значения, значение по умолчанию (если требуется) и иногда допустимые значения параметра.

	Добавьте следующие параметры в родительский элемент параметров, добавленный в шаблон:

	- Имя основной виртуальной машины, которая используется для доступа к машинам в масштабируемом наборе.
	- Имя учетной записи хранения, в которой хранится шаблон.
	- Количество экземпляров виртуальных машин, которое требуется создать сначала в масштабируемом наборе.
	- Имя и пароль учетной записи администратора на виртуальных машинах.
	- Префикс для ресурсов, создаваемых в группе ресурсов.


	```
	"vmName": {
		"type": "string"
	},
	"vmSSName": {
		"type": "string"
	},
	"instanceCount": {
		"type": "string"
	},
	"adminUsername": {
		"type": "string"
	},
	"adminPassword": {
		"type": "securestring"
	},
	"resourcePrefix": {
		"type": "string"
	}
	```

3. Переменные в шаблоне можно использовать для указания значений, которые могут часто изменяться, или значений, которые должны быть созданы из сочетания значений параметров.

	Добавьте следующие переменные в родительский элемент переменных, добавленный в шаблон:

	- DNS-имена, которые используются сетевыми интерфейсами.
	- Размер виртуальных машин, используемых в масштабируемом наборе. Дополнительную информацию о размерах виртуальных машин см. в статье [Размеры виртуальных машин](virtual-machines-size-specs.md).
	- Сведения об образе платформы для определения операционной системы, которая будет запущена на виртуальных машинах в масштабируемом наборе. Дополнительную информацию о выборе образов см. в статье [Просмотр и выбор образов виртуальных машин Azure с помощью оболочки Windows PowerShell и инфраструктуры Azure CLI](resource-groups-vm-searching.md).
	- Имена и префиксы IP-адресов для виртуальной сети и подсетей.
	- Имена и идентификаторы виртуальной сети, балансировщик нагрузки и сетевые интерфейсы.
	- Имена учетных записей хранения, связанных с машинами в масштабируемом наборе.
	- Параметры для расширения системы диагностики, которая установлена на виртуальных машинах. Дополнительную информацию о расширении системы диагностики см. в статье [Создание виртуальной машины Windows с мониторингом и диагностикой с помощью шаблона диспетчера ресурсов Azure](virtual-machines-extensions-diagnostics-windows-template.md).

	```
	"apiVersion": "2015-06-15"
	"dnsName1": "[concat(parameters('resourcePrefix'),'dn1')] ",
	"dnsName2": "[concat(parameters('resourcePrefix'),'dn2')] ",
	"vmSize": "Standard_A0",
	"imagePublisher": "Canonical",
	"imageOffer": "UbuntuServer",
	"imageVersion": "15.10",
	"addressPrefix": "10.0.0.0/16",
	"subnetName": "Subnet",
	"subnetPrefix": "10.0.0.0/24",
	"publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
	"publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
	"loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
	"virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
	"nicName1": "[concat(parameters('resourcePrefix'),'nc1')]",
	"nicName2": "[concat(parameters('resourcePrefix'),'nc2')]",
	"vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
	"publicIPAddressID1": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIP1'))]",
	"publicIPAddressID2": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIP2'))]",
	"lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
	"nicId": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName2'))]",
	"frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
	"storageAccountType": "Standard_LRS",
	"storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
	"diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
	"accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
	"wadlogs": "<WadCfg><DiagnosticMonitorConfiguration>",
	"wadperfcounter": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor\\PercentProcessorTime" sampleRate="PT15S" unit="Percent"><annotation displayName="CPU percentage guest OS" locale="ru-RU"/></PerformanceCounterConfiguration>",
	"wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId="')]",
	"wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
	"wadcfgxend": "[concat('"><MetricAggregation scheduledTransferPeriod="PT1H"/><MetricAggregation scheduledTransferPeriod="PT1M"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
	```

4. Следуя инструкциям этого руководства, вы развернете следующие ресурсы и расширения:

 - Microsoft.Storage/storageAccounts
 - Microsoft.Network/virtualNetworks
 - Microsoft.Network/publicIPAddresses
 - Microsoft.Network/loadBalancers
 - Microsoft.Network/networkInterfaces
 - Microsoft.Compute/virtualMachines
 - Microsoft.Compute/virtualMachineScaleSets;
 - Microsoft.Insights.VMDiagnosticsSettings;
 - Microsoft.Insights/autoscaleSettings.

	Дополнительную информацию о ресурсах диспетчера ресурсов см. в статье [Поставщики вычислительных и сетевых ресурсов, а также ресурсов хранения Azure в диспетчере ресурсов Azure](virtual-machines-azurerm-versus-azuresm.md).

	Добавьте ресурс учетной записи хранения в родительский элемент ресурсов, добавленный в шаблон. Этот шаблон использует цикл для создания рекомендуемых пяти учетных записей хранения, в которых хранятся диски операционной системы и диагностические данные. Этот набор учетных записей может поддерживать до 100 виртуальных машин в масштабируемом наборе (это максимальное количество на данный момент). Каждой учетной записи хранения присваивается буквенный код, который определен в переменных в сочетании с суффиксом, указанным в параметрах шаблона.

	```
	{
		"type": "Microsoft.Storage/storageAccounts",
		"name": "[concat(parameters('resourcePrefix'), parameters('storageAccountSuffix')[copyIndex()])]",
		"apiVersion": "2015-05-01-preview",
		"copy": {
			"name": "storageLoop",
			"count": 5
		},
		"location": "[resourceGroup().location]",
		"properties": {
			"accountType": "[variables('storageAccountType')]"
		}
	},
	```

5. Добавьте ресурс виртуальной сети. Дополнительную информацию см. в статье [Поставщик сетевых ресурсов](../virtual-network/resource-groups-networking.md).

	```
	{
		"apiVersion": "[variables('apiVersion')]",
		"type": "Microsoft.Network/virtualNetworks",
		"name": "[variables('virtualNetworkName')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"addressSpace": {
				"addressPrefixes": [
					"[variables('addressPrefix')]"
				]
			},
			"subnets": [
				{
					"name": "[variables('subnetName')]",
					"properties": {
						"addressPrefix": "[variables('subnetPrefix')]"
					}
				}
			]
		}
	},
	```

6. Добавьте ресурсы общедоступных IP-адресов, используемые балансировщиком нагрузки и сетевым интерфейсом.

	```
	{
		"apiVersion": "[variables('apiVersion')]",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('publicIP1')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"publicIPAllocationMethod": "Dynamic",
			"dnsSettings": {
				"domainNameLabel": "[variables('dnsName1')]"
			}
		}
	},
	{
		"apiVersion": "[variables('apiVersion')]",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('publicIP2')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"publicIPAllocationMethod": "Dynamic",
			"dnsSettings": {
				"domainNameLabel": "[variables('dnsName2')]"
			}
		}
	},
	```

7. Добавьте ресурс балансировщика нагрузки, используемый масштабируемым набором. Дополнительные сведения см. в статье [Поддержка диспетчера ресурсов Azure для балансировщика нагрузки](../load-balancer/load-balancer-arm.md).

	```
	{
		"apiVersion": "[variables('apiVersion')]",
		"name": "[variables('loadBalancerName')]",
		"type": "Microsoft.Network/loadBalancers",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
		],
		"properties": {
			"frontendIPConfigurations": [
				{
					"name": "loadBalancerFrontEnd",
					"properties": {
						"publicIPAddress": {
							"id": "[variables('publicIPAddressID1')]"
						}
					}
				}
			],
			"backendAddressPools": [
				{
					"name": "bepool1"
				}
			],
			"inboundNatPools": [
				{
					"name": "natpool1",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('frontEndIPConfigID')]"
						},
						"protocol": "tcp",
						"frontendPortRangeStart": 50000,
						"frontendPortRangeEnd": 50500,
						"backendPort": 3389
					}
				}
			]
		}
	},
	```

8. Добавьте ресурс сетевого интерфейса, используемый основной виртуальной машиной. К виртуальным машинам в масштабируемом наборе нельзя получить доступ напрямую с помощью общедоступного IP-адреса, поэтому в виртуальной сети масштабируемого набора создается основная виртуальная машина, которая используется для удаленного доступа к машинам в наборе.

	```
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Network/networkInterfaces",
		"name": "[variables('nicName1')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
			"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
		],
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Dynamic",
						"publicIPAddress": {
							"id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
						},
						"subnet": {
							"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/',variables('subnetName'))]"
						}
					}
				}
			]
		}
	},
	```


9. Добавьте виртуальную машину jumpbox в ту же сеть, в которой находится масштабируемый набор.

	```
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Compute/virtualMachines",
		"name": "[parameters('vmName')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"storageLoop",
			"[concat('Microsoft.Network/networkInterfaces/', variables('nicName1'))]"
		],
		"properties": {
			"hardwareProfile": {
				"vmSize": "[variables('vmSize')]"
			},
			"osProfile": {
				"computername": "[parameters('vmName')]",
				"adminUsername": "[parameters('adminUsername')]",
				"adminPassword": "[parameters('adminPassword')]"
			},
			"storageProfile": {
				"imageReference": {
					"publisher": "[variables('imagePublisher')]",
					"offer": "[variables('imageOffer')]",
					"sku": "[variables('imageVersion')]",
					"version": "latest"
				},
				"osDisk": {
					"name": "osdisk1",
					"vhd": {
						"uri":  "[concat('https://',parameters('resourcePrefix'),'saa.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'osdisk1.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
					{
						"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName1'))]"
					}
				]
			}
		}
	},
	```

10.	Добавьте ресурс масштабируемого набора виртуальных машин и укажите расширение системы диагностики, установленное на всех виртуальных машинах в масштабируемом наборе. Многие параметры данного ресурса похожи на ресурс виртуальной машины. Их основные различия приведены ниже.

	- **capacity** — указывает, сколько виртуальных машин должно быть инициализировано в масштабируемом наборе. Чтобы определить это значение, укажите значение для параметра instanceCount.

	- **upgradePolicy** — указывает, как выполняются обновления на виртуальных машинах в масштабируемом наборе. Значение Manual (Вручную) означает, что изменения в шаблоне применяются только к новым виртуальным машинам после повторного развертывания шаблона. Значение Automatic (Автоматически) означает, что все машины в масштабируемом наборе обновляются и перезапускаются.

	Масштабируемый набор виртуальных машин не будет создан, пока не будут созданы все учетные записи хранения, которые указаны в элементе dependsOn.

	```
	{
		"type": "Microsoft.Compute/virtualMachineScaleSets",
		"apiVersion": "[variables('apiVersion')]",
		"name": "[parameters('vmSSName')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"storageLoop",
			"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
			"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
		],
		"sku": {
			"name": "[variables('vmSize')]",
			"tier": "Standard",
			"capacity": "[parameters('instanceCount')]"
		}
		"properties": {
			"upgradePolicy": {
				"mode": "Manual"
			},
			"virtualMachineProfile": {
				"storageProfile": {
					"osDisk": {
						"vhdContainers": [
							"[concat('https://', parameters('resourcePrefix'), 'saa.blob.core.windows.net/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'sag.blob.core.windows.net/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'sam.blob.core.windows.net/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'sas.blob.core.windows.net/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'say.blob.core.windows.net/vmss')]"
						],
						"name": "vmssosdisk",
						"caching": "ReadOnly",
						"createOption": "FromImage"
					},
					"imageReference": {
						"publisher": "[variables('imagePublisher')]",
						"offer": "[variables('imageOffer')]",
						"sku": "[variables('imageVersion')]",
						"version": "latest"
					}
				},
				"osProfile": {
					"computerNamePrefix": "[parameters('vmSSName')]",
					"adminUsername": "[parameters('adminUsername')]",
					"adminPassword": "[parameters('adminPassword')]"
				},
				"networkProfile": {
					"networkInterfaceConfigurations": [
						{
							"name": "[variables('nicName2')]",
							"properties": {
								"primary": "true",
								"ipConfigurations": [
									{
										"name": "ip1",
										"properties": {
											"subnet": {
												"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/',variables('subnetName'))]"
											},
											"loadBalancerBackendAddressPools": [
												{
													"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
												}
											],
											"loadBalancerInboundNatPools": [
												{
													"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
												}
											]
										}
									}
								]
							}
						}
					]
				},
				"extensionProfile": {
					"extensions": [
						{
							"name":"LinuxDiagnostic",
							"properties": {
								"publisher":"Microsoft.OSTCExtensions",
								"type":"LinuxDiagnostic",
								"typeHandlerVersion":"2.1",
								"autoUpgradeMinorVersion":false,
								"settings": {
									"xmlCfg":"[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
									"storageAccount":"[variables('diagnosticsStorageAccountName')]"
								},
								"protectedSettings": {
									"storageAccountName":"[variables('diagnosticsStorageAccountName')]",
									"storageAccountKey":"[listkeys(variables('accountid'), variables('apiVersion')).key1]",
									"storageAccountEndPoint":"https://core.windows.net"
								}
							}
						}
					]
				}
			}
		}
	},
	```

11.	Добавьте ресурс autoscaleSettings, который определяет, как масштабируемый набор изменяется в зависимости от использования процессоров на машинах в наборе. Далее приведены важные параметры для этого руководства.

 - **metricName** — это то же самое, что счетчик производительности, определенный в переменной wadperfcounter. Используя эту переменную, расширение системы диагностики собирает данные счетчика **Processor\\PercentProcessorTime**.
 - **metricResourceUri** — это идентификатор ресурса масштабируемого набора виртуальных машин.
 - **timeGrain** — это степень детализации собираемых метрик. В данном шаблоне значение этого параметра равно 1 минуте.
 - **statistic** — этот параметр определяет, как объединяются метрики для выполнения автоматического масштабирования. Возможные значения: Average (Среднее), Min (Минимальное), Max (Максимальное). В этом шаблоне мы хотим определить среднее использование ЦП на виртуальных машинах в масштабируемом наборе.
 - **timeWindow** — это диапазон времени, в течение которого собираются данные об экземплярах. Значение должно составлять от 5 минут до 12 часов.
 - **timeAggregation** — этот параметр определяет способ объединения собранных данных с течением времени. Значение по умолчанию — Average (Среднее). Возможные значения: Average (Среднее), Minimum (Минимальное), Maximum (Максимальное), Last (Последнее), Total (Всего), Count (Количество).
 - **operator** — это оператор, который используется для сравнения данных метрики и порогового значения. Возможные значения: Equals (Равно), NotEquals (Не равно), GreaterThan (Больше), GreaterThanOrEqual (Больше или равно), LessThan (Меньше), LessThanOrEqual (Меньше или равно).
 - **threshold** — это значение, которое активирует действие масштабирования. В этом шаблоне машины добавляются в масштабируемый набор, когда среднее использование ЦП на машинах в наборе превышает 50 %.
 - **direction** — этот параметр определяет действие, которое выполняется, когда достигается пороговое значение. Допустимые значения: Increase (Увеличить) или Decrease (Уменьшить). В этом шаблоне количество виртуальных машин в масштабируемом наборе увеличивается, если пороговое значение превышает 50 % за определенный период.
 - **type** — это тип действия, которое должно выполняться. Для этого параметра должно быть установлено значение ChangeCount (Изменить количество).
 - **value** — это количество виртуальных машин, которые добавляются в масштабируемый набор или удаляются из него. Для этого параметра должно быть указано значение не меньше 1. Значение по умолчанию — 1. В этом шаблоне количество машин в масштабируемом наборе увеличивается на 1 при достижении порогового значения.
 - **cooldown** — это общее время ожидания с момента последнего действия масштабирования перед выполнением следующего действия. Это значение должно быть в диапазоне от 1 минуты до 1 недели.

	```
	{
		"type": "Microsoft.Insights/autoscaleSettings",
		"apiVersion": "2015-04-01",
		"name": "[concat(parameters('resourcePrefix'),'as1')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
		],
		"properties": {
			"enabled": true,
			"name": "[concat(parameters('resourcePrefix'),'as1')]",
			"profiles": [
				{
					"name": "Profile1",
					"capacity": {
						"minimum": "1",
						"maximum": "10",
						"default": "1"
					},
					"rules": [
						{
							"metricTrigger": {
								"metricName": "\\Processor\\PercentProcessorTime",
								"metricNamespace": "",
								"metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
								"timeGrain": "PT1M",
								"statistic": "Average",
								"timeWindow": "PT5M",
								"timeAggregation": "Average",
								"operator": "GreaterThan",
								"threshold": 50.0
							},
							"scaleAction": {
								"direction": "Increase",
								"type": "ChangeCount",
								"value": "1",
								"cooldown": "PT5M"
							}
						}
					]
				}
			],
			"targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
		}
	}
	```

12.	Сохраните файл шаблона.    

## Шаг 3. Отправка шаблона в хранилище

Шаблон можно отправить из интерфейса командной строки, если вы знаете имя учетной записи и первичный ключ учетной записи хранения, созданной на шаге 1.

1. В интерфейсе командной строки (Bash, терминал, командная строка) выполните следующие команды, чтобы задать переменные среды, необходимые для доступа к учетной записи хранения:

		export AZURE_STORAGE_ACCOUNT={account_name}
		export AZURE_STORAGE_ACCESS_KEY={key}

	Чтобы получить этот ключ, щелкните значок ключа в представлении ресурсов учетной записи хранения на портале Azure. При использовании командной строки Windows введите **установить** вместо экспорта.

2. Создайте контейнер для хранения шаблона:

		azure storage container create -p Blob templates

3. Отправьте файл шаблона в новый контейнер.

		azure storage blob upload VMSSTemplate.json templates VMSSTemplate.json

## Шаг 4. Развертывание шаблона

После создания шаблона можно начать развертывать ресурсы. Чтобы запустить процесс, используйте следующую команду:

	azure group deployment create --template-uri https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json vmsstestrg1 vmsstestdp1

Когда вы нажмете клавишу ВВОД, появится запрос на ввод переменных, которые вы назначили. Укажите следующие значения:

	vmName: vmsstestvm1
	vmSSName: vmsstest1
	instanceCount: 5
	adminUserName: vmadmin1
	adminPassword: VMpass1
	resourcePrefix: vmsstest

Для успешного развертывания всех ресурсов может потребоваться около 15 минут.

>[AZURE.NOTE]Вы также можете использовать возможности портала для развертывания ресурсов. Чтобы сделать это, используйте следующую ссылку: https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>

## Шаг 5. Мониторинг ресурсов

Сведения о масштабируемых наборах виртуальных машин можно получать, используя следующие методы:

 - Портал Azure — с помощью портала в настоящее время можно получить ограниченный объем сведений.
 - [Обозреватель ресурсов Azure](https://resources.azure.com/) — это лучшее средство для просмотра текущего состояния масштабируемого набора. Используйте этот путь, чтобы увидеть представление экземпляра созданного масштабируемого набора:

		subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - Azure CLI — используйте эту команду, чтобы получить сведения:

		azure resource show -n vmsstest1 -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g vmsstestrg1

 - Подключитесь к основной виртуальной машине так же, как к любому другому компьютеру. Теперь вы сможете удаленно подключаться к виртуальным машинам в масштабируемом наборе, чтобы отслеживать отдельные процессы.

>[AZURE.NOTE]Полный REST API для получения информации о масштабируемых наборах можно найти на странице [Масштабируемые наборы виртуальных машин](https://msdn.microsoft.com/library/mt589023.aspx).

## Шаг 6. Удаление ресурсов

Так как за использование ресурсов Azure взимается плата, рекомендуется всегда удалять ресурсы, которые больше не нужны. Не нужно отдельно удалять каждый ресурс из группы ресурсов. Можно удалить группу ресурсов, тогда все ее ресурсы будут автоматически удалены.

		azure group delete vmsstestrg1

## Дальнейшие действия

Ознакомьтесь с шаблоном [Автоматическое масштабирование масштабируемого набора виртуальных машин с помощью приложения Ubuntu, Apache или PHP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale), который настраивает стек LAMP для выполнения автоматического масштабирования масштабируемых наборов виртуальных машин.

<!---HONumber=AcomDC_0128_2016-->