<properties 
   pageTitle="Управление DNS-серверами, используемыми виртуальной сетью"
   description="Узнайте, как добавлять и удалять DNS-серверы в виртуальной сети (VNet)."
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

# Управление DNS-серверами, используемыми виртуальной сетью

Списком DNS-серверов, которые используются в виртуальной сети, можно управлять с помощью портала управления или файла конфигурации сети. Для каждой виртуальной сети можно добавить до 12 DNS-серверов. При указании DNS-серверов важно убедиться, что они размещены в списке в правильном порядке для вашей среды. Списки DNS-серверов не работают по принципу циклического перебора. Серверы используются в том порядке, в котором они указаны. Если первый DNS-сервер в списке доступен, клиент будет использовать его вне зависимости от того, работает DNS-сервер правильно или нет. Чтобы изменить порядок DNS-серверов для виртуальной сети, удалите их из списка и добавьте обратно в требуемом порядке.

>[AZURE.WARNING]После обновления списка DNS необходимо перезагрузить виртуальные машины, расположенные в виртуальной сети, чтобы они приняли новые параметры DNS-сервера. До перезагрузки виртуальные машины будут продолжать использовать текущую конфигурацию.

## Изменение списка DNS-серверов для виртуальной сети с помощью портала управления

1. Войдите на **портал управления**.

1. В области навигации выберите **Сети**, а затем в столбце **Имя** щелкните имя виртуальной сети.

1. Нажмите **Настроить**.

1. В разделе **DNS-серверы** можно:

	- **Зарегистрировать (добавить) новый DNS-сервер**. Просто введите имя и IP-адрес в соответствующих полях. DNS-сервер будет добавлен в список DNS-серверов для виртуальной сети и зарегистрирован в Azure.

	- **Добавить DNS-сервер, который был зарегистрирован ранее**. Если DNS-сервер уже зарегистрирован в Azure, его можно выбрать в списке серверов.

	- **Удалить DNS-сервер из виртуальной сети**. Нажмите кнопку X рядом с сервером, который требуется удалить. Обратите внимание, что сервер будет удален только из списка данной виртуальной сети. DNS-сервер остается зарегистрированным в Azure и может использоваться в других виртуальных сетях. Чтобы удалить DNS-сервер из подписки, перейдите на страницу **Сети -> DNS-серверы**.

	- **Изменить порядок DNS-серверов**. Удалите все DNS-серверы в списке, а затем добавьте их обратно в требуемом порядке. Помните, что к списку DNS-серверов не применяется принцип циклического перебора.

	- **Переименовать DNS-сервер**. Выделите DNS-сервер в списке и введите новое имя. При этом в службе Azure будет зарегистрирован новый DNS-сервер, который также будет добавлен в список DNS-серверов для вашей виртуальной сети. Старый DNS-сервер и его IP-адрес останутся зарегистрированными в Azure. Его можно удалить на странице **DNS-серверы**, если он не используется в других виртуальных сетях.

1. Нажмите кнопку **Сохранить** внизу страницы, чтобы сохранить новую конфигурацию DNS-серверов.

1. Перезапустите виртуальные машины в виртуальной сети, чтобы они приняли новые параметры DNS.

## Изменение списка DNS-серверов с помощью файла конфигурации сети

Чтобы изменить список DNS-серверов с помощью файла конфигурации сети, сначала на портале управления экспортируйте параметры конфигурации. Затем измените файл конфигурации сети и импортируйте его обратно через портал управления. Ниже приведен краткий список действий для выполнения этой процедуры.

1. Экспортируйте параметры виртуальной сети в файл конфигурации. Дополнительные сведения об экспортировании параметров конфигурации сети и соответствующие инструкции см. в статье [Экспорт параметров виртуальной сети в файл конфигурации сети](virtual-networks-using-network-configuration-file.md).

1. Укажите данные DNS-сервера для виртуальной сети. Дополнительные сведения об определении DNS-сервера см. в статье [Задание параметров DNS в файле конфигурации виртуальной сети](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md). Дополнительные сведения о файлах конфигурации сети см. в статьях [Схема конфигурации виртуальной сети Microsoft Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) и [Настройка виртуальной сети с помощью файлов конфигурации](virtual-networks-using-network-configuration-file.md).

1. Импортируйте файл конфигурации сети. Дополнительные сведения об импортировании файла конфигурации сети и соответствующие инструкции см. в статье [Импорт файла конфигурации сети](virtual-networks-using-network-configuration-file.md).

1. Перезапустите виртуальные машины в виртуальной сети, чтобы они приняли новые параметры DNS.

## Дальнейшие действия

[Управление свойствами виртуальной сети (VNet)](../virtual-networks-settings)

[Использование общедоступных IP-адресов в виртуальной сети](../virtual-networks-public-ip-within-vnet)

[Удаление виртуальной сети](../virtual-networks-delete-vnet)

<!----HONumber=AcomDC_1217_2015-->