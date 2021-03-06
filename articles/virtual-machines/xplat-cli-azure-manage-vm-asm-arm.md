<properties
	pageTitle="Эквивалентные команды интерфейса командной строки Azure для задач виртуальных машин| Microsoft Azure"
	description="Эквивалентные команды интерфейса командной строки Azure для создания виртуальных машин Azure и управления ими в режимах диспетчера ресурсов Azure и управления службами Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="command-line-interface"
	ms.workload="infrastructure-services"
	ms.date="12/14/2015"
	ms.author="danlep"/>


# Эквивалентные команды диспетчера ресурсов и управления службами для задач виртуальных машин в интерфейсе командной строки Azure
В этой статье показаны эквивалентные команды интерфейса командной строки Microsoft Azure (Azure CLI) для создания виртуальных машин Azure и управления ими в режиме управления службами Azure и режиме диспетчера ресурсов Azure. Используйте ее в качестве удобного руководства по переноса сценариев из одной режима команд в другой.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



* Если вы не установили Azure CLI и не подключили его к своей подписке, см. статьи [Установка Azure CLI](../xplat-cli-install.md) и [Подключение к подписке Azure из Azure CLI](../xplat-cli-connect.md). Если вам нужно использовать команды режима диспетчера ресурсов, выполните подключение с помощью метода входа.

* Чтобы приступить к работе в режиме диспетчера ресурсов в интерфейсе командной строки Azure, необходимо переключить режимы команды. По умолчанию интерфейс командной строки Azure запускается в режиме управления службами. Чтобы перейти в режим диспетчера ресурсов, выполните команду `azure config mode arm`. Чтобы вернуться в режим управления службами, выполните команду `azure config mode asm`.

* Чтобы использовать интерактивную справку по командам и параметры, введите `azure <command> <subcommand> --help` или `azure help <command> <subcommand>`.

## Задачи виртуальных машин
В следующей таблице сравниваются распространенные задачи виртуальных машин, которые можно выполнять с помощью команд интерфейса командной строки Azure в режиме диспетчера ресурсов и управления службами. Для многих команд диспетчера ресурсов необходимо передавать имя существующей группы ресурсов.

> [AZURE.NOTE]Эти примеры не включают операции на основе шаблонов, которые обычно рекомендуются для развертываний виртуальных машин в диспетчере ресурсов. Дополнительные сведения см. в статье [Использование интерфейса командной строки Azure с диспетчером ресурсов Azure](../xplat-cli-azure-resource-manager.md) и [Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure](virtual-machines-deploy-rmtemplates-azure-cli.md).

Задача | Service Management | Диспетчер ресурсов
-------------- | ----------- | -------------------------
Создание самой простой виртуальной машины | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Получите `image-urn` из команды `azure vm image list`. Примеры см. в [этой статье](resource-groups-vm-searching.md).)
Создание виртуальной машины Linux | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Создание виртуальной машины Windows | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
Вывод списка виртуальных машин | `azure  vm list [options]` | `azure  vm list [options]`
Получение информации о виртуальной машине | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
Запуск виртуальной машины | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
Остановка виртуальной машины | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
Освобождение виртуальной машины | Недоступно | `azure vm deallocate [options] <resource-group> <name>`
Перезапуск виртуальной машины | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
Удаление виртуальной машины | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
Запись виртуальной машины | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
Создание виртуальной машины из образа пользователя | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
Создание виртуальной машины из специализированного диска | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
Добавление диска данных в виртуальную машину | `azure  vm disk attach [options] <vm-name> <disk-image-name>` ИЛИ <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
Удаление диска данных от виртуальной машины | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
Добавление универсального расширения в виртуальную машину | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
Добавление в виртуальную машину расширения VM Access | Недоступно | `azure vm reset-access [options] <resource-group> <name>`
Добавление в виртуальную машину расширения Docker | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
Добавление в виртуальную машину расширения Chef | `azure  vm extension get-chef [options] <vm-name>` | Недоступно
Отключение расширения виртуальной машины | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | Недоступно
Удаление расширения виртуальной машины | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
Вывод списка расширений виртуальной машины | `azure vm extension list [options]` | Недоступно
Отображение образа виртуальной машины | `azure vm image show [options]` | Недоступно
Получение сведений об использовании ресурсов виртуальной машины | Недоступно | `azure vm list-usage [options] <location>`
Получить все доступные размеры виртуальной машины | Недоступно | `azure vm sizes [options]`


## Дальнейшие действия

* Дополнительные примеры команд для командной строки см. в разделах [Использование интерфейса командной строки Azure с управлением службами Azure](virtual-machines-command-line-tools.md) и [Использование интерфейса командной строки Azure с диспетчером ресурсов Azure](azure-cli-arm-commands.md).

<!---HONumber=AcomDC_1223_2015-->