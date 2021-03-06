<properties
	pageTitle="Управление доступом на основе ролей с помощью Windows PowerShell"
	description="Управление доступом на основе ролей с помощью Windows PowerShell"
	services="active-directory"
	documentationCenter="na"
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="kgremban"/>

# Управление доступом на основе ролей с помощью Windows PowerShell

> [AZURE.SELECTOR]
- [Windows PowerShell](role-based-access-control-powershell.md)
- [Интерфейс командной строки Azure](role-based-access-control-xplat-cli.md)


Функция управления доступом на основе ролей (RBAC) на портале Azure и в API управления ресурсами Azure позволяет управлять доступом к подписке с высокой точностью. С ее помощью вы можете предоставлять доступ пользователям, группам и субъектам-службам Active Directory, назначая им роли с определенной областью.

Из этого учебника вы узнаете, как использовать Windows PowerShell для управления RBAC. Он представляет собой пошаговое руководство по созданию ролей и проверке их назначений.

**Предполагаемое время выполнения:** 15 минут.

## Предварительные требования

Чтобы использовать Windows PowerShell для управления RBAC, вам понадобится:

- Windows PowerShell версии 3.0 или 4.0. Чтобы найти версию Windows PowerShell, введите `$PSVersionTable` и убедитесь, что для `PSVersion` указано значение 3.0 или 4.0. Чтобы установить совместимую версию, см. статью [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) или [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

- Azure PowerShell версии 0.8.8 или выше. Чтобы установить последнюю версию и связать ее со своей подпиской Azure, см. статью [Как установить и настроить Azure PowerShell](../powershell-install-configure.md);

>[AZURE.IMPORTANT] Перед использованием командлетов в этой статье необходимо [установить командлеты Azure Resource Manager](https://msdn.microsoft.com/library/mt125356.aspx) в PowerShell.

Этот учебник предназначен для начинающих пользователей Windows PowerShell, но предполагается, что вы знакомы с основными понятиями, такими как модули, командлеты и сеансы. Дополнительные сведения о Windows PowerShell см. в разделе [Начало работы с Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Чтобы получить подробную справку по любому командлету, который встречается в этом руководстве, используйте командлет `Get-Help`.

	Get-Help <cmdlet-name> -Detailed

Например, чтобы получить справку для командлета `Add-AzureAccount`, введите:

	Get-Help Add-AzureAccount -Detailed

Чтобы узнать, как настраивать и использовать диспетчер ресурсов Azure в Windows PowerShell, также прочитайте следующие руководства:

- [Установка и настройка Azure PowerShell](../powershell-install-configure.md)
- [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md)


## Подключение к своим подпискам

RBAC работает только с диспетчером ресурсов Azure, поэтому в первую очередь необходимо перейти в режим диспетчера ресурсов Azure.

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Дополнительные сведения см. в разделе [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).

Чтобы подключиться к подпискам Azure, введите:

    PS C:\> Add-AzureAccount

Во всплывающем окне браузера введите имя пользователя и пароль учетной записи Azure. PowerShell соберет сведения обо всех подписках для этой учетной записи. По умолчанию будет использоваться первая подписка. Обратите внимание: при использовании RBAC вы получите только те подписки, в которых у вас есть какие-либо разрешения, т. е. для которых вы являетесь соадминистратором или вам назначена какая-либо роль.

Если у вас несколько подписок и вы хотите переключиться на другую, используйте следующие команды:

    # This will show you the subscriptions under the account.
    PS C:\> Get-AzureSubscription
    # Use the subscription name to select the one you want to work on.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

Дополнительные сведения см. в разделе [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## Проверка существующих назначений ролей

Теперь давайте проверим, какие назначения ролей уже есть в подписке. Введите:

    PS C:\> Get-AzureRoleAssignment

Эта команда возвращает все назначения ролей в подписке. Обратите внимание на два момента:

1. Вам потребуется доступ на чтение на уровне подписки.
2. Если в подписке много назначений ролей, получение сведений о них может занять некоторое время.

Можно также проверить существующие назначения ролей для конкретного определения роли с определенной областью и для определенного пользователя. Тип:

    PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

Эта команда возвращает все назначения ролей для определенного пользователя в клиенте Active Directory, которому назначена роль "Владелец" для группы ресурсов group1. Назначение роли может иметь два разных источника:

1. Назначение роли "Владелец" пользователю для группы ресурсов.
2. Назначение роли "Владелец" пользователю для родительского объекта группы ресурсов (в этом случае — подписки). Если у вас есть какое-либо разрешение на родительском уровне, вы получите аналогичные разрешения на доступ ко всем дочерним объектам.

Все параметры этого командлета необязательны. Вы можете использовать их в различных сочетаниях для проверки назначений ролей с разными фильтрами.

## Создание назначения роли

При создании назначения роли необходимо учесть перечисленные ниже моменты.

Кому вы хотите назначить роль? Для просмотра пользователей, групп и субъектов-служб, имеющихся в клиенте Active Directory, можно использовать следующие командлеты Azure Active Directory:

    PS C:\> Get-AzureADUser
	PS C:\> Get-AzureADGroup
	PS C:\> Get-AzureADGroupMember
	PS C:\> Get-AzureADServicePrincipal

Какую роль вы хотите назначить? Для просмотра поддерживаемых определений ролей можно использовать следующий командлет:

    PS C:\> Get-AzureRoleDefinition

Какую область вы хотите назначить? Доступны три уровня:
  - текущая подписка;
  - группа ресурсов; чтобы получить список групп ресурсов, введите `PS C:\> Get-AzureResourceGroup`;
  - ресурс; чтобы получить список ресурсов, введите `PS C:\> Get-AzureResource`.

Затем используйте `New-AzureRoleAssignment`, чтобы создать назначение роли. Например:

	#This will create a role assignment at the current subscription level for a user as a reader.
	PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Reader

	#This will create a role assignment at a resource group level.
	PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Contributor -ResourceGroupName group1

	#This will create a role assignment for a group at a resource group level.
	PS C:\> New-AzureRoleAssignment -ObjectID <group object ID> -RoleDefinitionName Reader -ResourceGroupName group1

	#This will create a role assignment at a resource level.
	PS C:\> $resources = Get-AzureResource
    PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId


## Проверка разрешений

Если вашей учетной записи назначены какие-либо роли, вы можете просмотреть, какие именно разрешения предоставляют эти назначения, с помощью следующих командлетов:

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

Эти два командлета возвращают только группы ресурсов или ресурсы, для которых у вас есть разрешение на чтение. Они также отображают имеющиеся у вас разрешения.

Попытавшись затем выполнить другие командлеты, например `New-AzureResourceGroup`, вы получите сообщение об отказе в доступе, если у вас нет соответствующего разрешения.

## Дальнейшие действия

Чтобы узнать больше об управлении доступом на основе ролей с помощью Windows PowerShell, воспользуйтесь следующими ресурсами:

- [Управление доступом на основе ролей в Azure](role-based-access-control-configure.md)
- [Командлеты диспетчера ресурсов Azure](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409): узнайте, как использовать командлеты в модуле диспетчера ресурсов Azure.
- [Управление ресурсами Azure с помощью портала Azure](../azure-portal/resource-group-portal.md): узнайте об Azure Resource Manager.
- [Блог Azure](http://blogs.msdn.com/windowsazure): узнайте о новых возможностях в Azure.
- [Блог Windows PowerShell](http://blogs.msdn.com/powershell): узнайте о новых возможностях в Windows PowerShell.
- [Блог "Hey, Scripting Блог](http://blogs.technet.com/b/heyscriptingguy/): реальные советы и рекомендации от сообщества Windows PowerShell.
- [Настройка управления доступом на основе ролей с помощью Azure CLI](role-based-access-control-xplat-cli.md)
- [Устранение неполадок управления доступом на основе ролей](role-based-access-control-troubleshooting.md)

<!---HONumber=AcomDC_0302_2016-->