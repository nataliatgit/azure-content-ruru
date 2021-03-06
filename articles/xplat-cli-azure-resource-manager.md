
<properties
	pageTitle="Интерфейс командной строки Azure с диспетчером ресурсов | Microsoft Azure"
	description="Использование интерфейса командной строки Azure для Mac, Linux и Windows для развертывания нескольких ресурсов в группе ресурсов."
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="azure-resource-manager"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/19/2016"
	ms.author="danlep"/>

# Использование Azure CLI для Mac, Linux и Windows с диспетчером ресурсов Azure

> [AZURE.SELECTOR]
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)



В этой статье описывается, как создавать ресурсы Azure и управлять ими с помощью интерфейса командной строки Azure для Mac, Linux и Windows в режиме диспетчера ресурсов Azure.

>[AZURE.NOTE] Чтобы создавать ресурсы Azure и управлять ими с помощью командной строки, нужна учетная запись Azure ([получить бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/)). Вам также нужно [установить интерфейс командной строки Azure](xplat-cli-install.md) и [войти в систему для использования ресурсов Azure, связанных с вашей учетной записью](xplat-cli-connect.md). Если все это уже сделано, у вас все готово для работы.

## Ресурсы Azure

Диспетчер ресурсов Azure используется для создания группы _ресурсов_ (управляемые пользователем сущности, такие как виртуальная машина, сервер базы данных, база данных или веб-сайт), и управления ими как одной логической единицей. Такая логическая единица называется _группой ресурсов_.

Одним из преимуществ диспетчера ресурсов Azure является то, что вы можете создавать ресурсы Azure _декларативно_. Это делается путем описания структуры и отношений развертываемой группы ресурсов в *шаблонах* JSON. В шаблоне описываются параметры, значения которых указываются в аргументах выполняемой команды либо хранятся в отдельном JSON-файле (azuredeploy-parameters.json). Это позволяет легко создавать новые ресурсы с помощью одного и того же шаблона, просто предоставляя различные параметры. Например, в шаблоне для создания веб-сайта, помимо прочих общих параметров, будут указаны параметры для имени сайта и региона, в котором он будет размещен.

Когда шаблон используется для изменения или создания группы, создается _развертывание_, которое затем применяется к этой группе. Дополнительные сведения о диспетчере ресурсов Azure см. в статье [Общие сведения о диспетчере ресурсов Azure](resource-group-overview.md).

После создания развертывания отдельными ресурсами можно управлять принудительно в командной строке так же, как и в классической модели развертывания (Service Management). Например, с помощью команд интерфейса командной строки диспетчера ресурсов Azure можно запускать, останавливать и удалять ресурсы, такие как [виртуальные машины диспетчера ресурсов Azure](virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md).

## Аутентификация

Для работы с диспетчером ресурсов Azure через интерфейс командной строки Azure нужно войти в Microsoft Azure, используя рабочую или учебную учетную запись (учетная запись организации) или учетную запись Microsoft (начиная с версии CLI 0.9.10). Аутентификация с использованием сертификата, установленного с помощью PUBLISHSETTINGS-файла, не поддерживается.

Дополнительные сведения об аутентификации в Microsoft Azure см. в статье [Подключение к подписке Azure через интерфейс командной строки Azure](xplat-cli-connect.md).

>[AZURE.NOTE] При использовании рабочей или учебной учетной записи, которая управляется службой Azure Active Directory, для управления доступом к ресурсам Azure и их использованием также можно использовать механизм контроля доступа к Azure на основе ролей (RBAC). Дополнительные сведения см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md).

## Настройка режима диспетчера ресурсов Azure

Режим диспетчера ресурсов Azure не активирован по умолчанию. Чтобы активировать команды интерфейса командной строки Azure для диспетчера ресурсов, необходимо выполнить следующую команду.

	azure config mode arm

>[AZURE.NOTE] Режим диспетчера ресурсов Azure и режим управления службами Azure являются взаимоисключающими. То есть ресурсами, созданными в одном из режимов, нельзя управлять из другого режима.

## Поиск расположений

Для большинства команд в режиме диспетчера ресурсов Azure нужно указывать допустимое расположение, в котором они будут создавать или искать ресурсы. Все доступные расположения различных ресурсов Azure можно найти с помощью следующей команды.

	azure location list

Она выводит список ресурсов и регионов Azure, в которых они доступны, например «Запад США», «Восток США» и т. д.

## Создание группы ресурсов

Группа ресурсов — это сетевые ресурсы, ресурсы хранилищ и другие ресурсы, объединенные в логическую группу. Почти для всех команд в режиме диспетчера ресурсов Azure требуется группа ресурсов. Например, чтобы создать группу ресурсов с именем _testRG_, нужно выполнить следующую команду.

	azure group create -n "testRG" -l "West US"

Развертывание в этой группе ресурсов testRG будет выполняться позже при использовании шаблона для запуска виртуальной машины Ubuntu. Создав группу ресурсов, вы можете добавить в нее ресурсы, такие как виртуальные машины и сети или хранилища.


## Использование шаблонов групп ресурсов

### Поиск и настройка шаблона группы ресурсов

При работе с шаблонами можно [создавать собственные шаблоны](resource-group-authoring-templates.md) или использовать шаблоны из [коллекции шаблонов](https://azure.microsoft.com/documentation/templates/), которые доступны на сайте [GitHub](https://github.com/Azure/azure-quickstart-templates).

Создание нового шаблона выходит за рамки данной статьи, поэтому для начала воспользуемся шаблоном _101-simple-vm-from-image_, который есть в [коллекции шаблонов](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux/). По умолчанию при его использовании создается одна виртуальная машина Ubuntu 14.04.2-LTS в новой виртуальной сети с одной подсетью в регионе "Запад США". Для использования этого шаблона достаточно указать следующие параметры:

* имя пользователя администратора виртуальной машины (`adminUsername`);
* пароль (`adminPassword`);
* имя домена для виртуальной машины (`dnsLabelPrefix`).

>[AZURE.TIP] Следующие действия показывают лишь один из возможных способов использования шаблона виртуальной машины с интерфейсом командной строки Azure. Другие примеры см. в статье [Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure](virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md).

1. Воспользуйтесь ссылкой "Подробнее с GitHub" для загрузки файлов azuredeploy.json и azuredeploy.parameters.json с сайта GitHub в рабочую папку на локальном компьютере. (Не забудьте выбрать формат _raw_ для каждого файла в GitHub.)

2. Откройте файл azuredeploy.parameters.json в текстовом редакторе и введите значения параметров для своей среды (не меняйте значение **ubuntuOSVersion**).

	```
			{
			  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
			  "contentVersion": "1.0.0.0",
			  "parameters": {
			    "adminUsername": {
			      "value": "azureUser"
			    },
			    "adminPassword": {
			      "value": "GEN-PASSWORD"
			    },
			    "dnsLabelPrefix": {
			      "value": "GEN-UNIQUE"
			    },
			    "ubuntuOSVersion": {
			      "value": "14.04.2-LTS"
			    }
			  }
			}

	```
3.  Изменив нужные параметры развертывания, вы можете выполнить развертывание виртуальной машины Ubuntu в группе ресурсов, которая была создана ранее. Выберите имя для развертывания, а затем используйте следующую команду для его запуска.

	```
	azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json testRG testRGdeploy
	```

	В этом примере создается развертывание с именем _testRGDeploy_, которое выполняется в группе ресурсов _testRG_. В параметре `-e` указывается файл azuredeploy.parameters.json, измененный в предыдущем действии. Параметр `-f` указывает файл шаблона azuredeploy.json.

	Эта команда возвращает значение ОК после передачи развертывания на сервер, но до его применения к ресурсам в группе.

4. Чтобы проверить состояние развертывания, используйте следующую команду.

	```
	azure group deployment show "testRG" "testRGDeploy"
	```

	**ProvisioningState** показывает состояние развертывания.

	Если развертывание пройдет успешно, выходные данные на экране будут аналогичны следующему примеру.

		azure-cli@0.8.0:/# azure group deployment show testRG testDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Running
		data:    Timestamp          : 2015-10-26T16:15:29.5562024Z
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ---------------------
		data:    newStorageAccountName  String        MyStorageAccount
		data:    adminUsername          String        MyUserName
		data:    adminPassword          SecureString  undefined
		data:    dnsNameForPublicIP     String        MyDomainName
		data:    ubuntuOSVersion        String        14.04.2-LTS
		info:    group deployment show command OK

	>[AZURE.NOTE] Если окажется, что ваша конфигурация неверна и требуется остановить длительное развертывание, используйте следующую команду.
	>
	> `azure group deployment stop "testRG" "testDeploy"`
	>
	> Если не указать имя развертывания, оно будет создано автоматически на основе имени файла шаблона. Оно будет возвращено в составе выходных данных команды `azure group create`.

	Теперь можно обращаться к виртуальной машине через протокол SSH с помощью указанного имени домена. При подключении к виртуальной машине нужно использовать полное доменное имя в формате `<domainName>.<region>.cloudapp.azure.com`, например `MyDomainName.westus.cloudapp.azure.com`.

5. Для просмотра группы используйте следующую команду.

		azure group show "testRG"

	Эта команда возвращает сведения о ресурсах в группе. Если у вас есть несколько групп, с помощью команды `azure group list` можно получить список их имен, а затем с помощью команды `azure group show` просмотреть сведения о конкретной группе.

Также можно использовать шаблон непосредственно с сайта [GitHub](https://github.com/Azure/azure-quickstart-templates), не загружая его на компьютер. Для этого укажите URL-адрес файла azuredeploy.json для шаблона в команде с помощью параметра **--template-url**. Чтобы получить URL-адрес, откройте файл azuredeploy.json на GitHub в режиме _raw_ и скопируйте URL-адрес из адресной строки браузера. С помощью этого URL-адреса можно напрямую создать развертывание с помощью команды, аналогичной следующей.

	azure group deployment create "testDeploy" testResourceGroup --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-linux/azuredeploy.json
Вам будет предложено ввести параметры шаблона.

> [AZURE.NOTE] Важно открыть шаблон JSON в режиме _raw_. URL-адрес в адресной строке браузера отличается от адреса, который отображается в обычном режиме. Чтобы открыть файл в режиме _raw_, при просмотре на сайте GitHub нажмите кнопку **Raw** (Без обработки) в правом верхнем углу экрана.

## Работа с ресурсами

Хотя шаблоны позволяют объявлять изменения в конфигурации для всей группы, иногда требуется работать исключительно с определенным ресурсом. Для этого воспользуйтесь командами `azure resource`.

> [AZURE.NOTE] Для любой команды `azure resource`, кроме команды `list`, необходимо указывать версию API ресурса, с которым вы работаете. Версия указывается в параметре `-o`. Если вы не уверены, какая именно версия API используется, обратитесь к файлу шаблона и найдите поле **apiVersion** для этого ресурса.

1. Чтобы получить список всех ресурсов в группе, используйте следующую команду.

		azure resource list "testRG"

2. Чтобы просмотреть отдельный ресурс в группе, используйте команду, аналогичную следующей.

		azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

	Обратите внимание на параметр **Microsoft.Compute/virtualMachines**. Он указывает тип ресурса, для которого вы запрашиваете информацию. Если посмотреть на скачанный ранее файл шаблона, можно заметить, что это же значение используется для определения типа ресурса виртуальной машины, описанного в шаблоне.

	Эта команда возвращает информацию, связанную с виртуальной машиной.

3. При просмотре сведений о ресурсе часто бывает полезно использовать параметр `--json`. Он делает выходные данные более удобочитаемыми, поскольку некоторые значения являются вложенными структурами или коллекциями. В следующем примере показано возвращение результатов команды **show** в виде документа JSON.

		azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json

	>[AZURE.NOTE] Чтобы сохранить данные JSON в файл, используйте символ &gt;. Таким образом выходные данные будут записаны в файл. Например:
	>
	> `azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

4. Чтобы удалить существующий ресурс, используйте следующую команду.

		azure resource delete "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

## Ведение журналов

Чтобы просмотреть записанную в журнал информацию об операциях, выполненных с группой, используйте команду `azure group log show`. По умолчанию при этом выводится последняя операция, выполненная с группой. Чтобы просмотреть все операции, используйте дополнительный параметр `--all`. Для последнего развертывания используйте параметр `--last-deployment`. Для конкретного развертывания используйте параметр `--deployment` и укажите имя развертывания. В следующем примере возвращается журнал всех операций, выполненных с группой *MyGroup*.

	azure group log show MyGroup --all

## Дальнейшие действия

* Дополнительные сведения о работе с диспетчером ресурсов Azure с помощью Azure PowerShell см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](powershell-azure-resource-manager.md).
* Дополнительные сведения о работе с диспетчером ресурсов Azure на портале Azure см. в статье [Использование групп ресурсов для управления ресурсами Azure][psrm].

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!---HONumber=AcomDC_0211_2016-->