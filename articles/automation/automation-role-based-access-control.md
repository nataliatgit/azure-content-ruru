<properties 
   pageTitle="Управление доступом на основе ролей в службе автоматизации Azure | Microsoft Azure"
   description="Контроль доступа на основе ролей (RBAC) Azure обеспечивает управление доступом к ресурсам Azure. В этой статье описывается настройка RBAC в службе автоматизации Azure."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/11/2016"
   ms.author="sngun"/>

# Управление доступом на основе ролей в службе автоматизации Azure

## Контроль доступа на основе ролей

Контроль доступа на основе ролей (RBAC) Azure обеспечивает управление доступом к ресурсам Azure. С помощью [RBAC](../active-directory/role-based-access-control-configure.md) вы можете распределить обязанности внутри своей команды и предоставить пользователям доступ на том уровне, который им необходим для выполнения поставленных задач. Для предоставления доступа на основе ролей можно использовать портал Azure, средства командной строки Azure и API управления Azure.

## Управление доступом в учетных записях автоматизации

В службе автоматизации Azure доступ предоставляется путем назначения соответствующей роли RBAC пользователям, группам и приложениям в области учетной записи автоматизации. Ниже перечислены встроенные роли, поддерживаемые учетной записью автоматизации.

|**Роль** | **Описание** |
|:--- |:---|
| Владелец | Роль владельца обеспечивает доступ ко всем ресурсам и действиям в учетной записи службы автоматизации, включая предоставление доступа на управление учетной записью службы автоматизации другим пользователям, группам и приложениям. |
| Участник | Роль участника позволяет управлять всем, кроме изменения разрешений других пользователей на доступ к учетной записи службы автоматизации. |
| читатель. | Роль читателя позволяет просматривать все ресурсы в учетной записи службы автоматизации, но не дает вносить никакие изменения. |
| Оператор службы автоматизации | Роль оператора службы автоматизации позволяет выполнять такие рабочие задачи, как запуск, остановка, приостановка, возобновление и планирование заданий. Эта роль пригодится, если вам нужно защитить ресурсы учетной службы автоматизации, такие как учетные данные, активы и модули Runbook, от просмотра и изменения, и в то же время разрешить членам вашей организации выполнять эти модули Runbook. В статье [Действия оператора службы автоматизации](../active-directory/role-based-access-built-in-roles.md#automation-operator) перечислены действия, поддерживаемые ролью оператора службы автоматизации и ее ресурсами. |
| Администратор доступа пользователей | Роль администратора доступа пользователей позволяет управлять доступом пользователей к учетным записям службы автоматизации Azure. |

В этой статье мы рассмотрим порядок настройки RBAC в службе автоматизации Azure.

## Настройки RBAC для учетной записи службы автоматизации с помощью портала Azure

1.	Войдите на [портал Azure](https://portal.azure.com/) и откройте свою учетную запись службы автоматизации в колонке "Учетные записи службы автоматизации".  

2.	Щелкните элемент управления **Доступ** в правом верхнем углу экрана. Откроется колонка **Пользователи**, в которую можно добавить новых пользователей, группы и приложения для управления учетной записью службы автоматизации и просмотра существующих ролей, настраиваемых для этой учетной записи.

    ![Кнопка доступа](media/automation-role-based-access-control/automation-01-access-button.png)

>[AZURE.NOTE]  **Администраторы подписки** уже существуют в качестве пользователя по умолчанию. Группа администраторов подписки Active Directory включает администраторов и соадминистраторов службы для подписки Azure. Администратор службы является владельцем подписки Azure и ее ресурсов, а также наследует роль владельца в учетных записях службы автоматизации. Это означает, что доступ к подписке **наследуется** для **администраторов и соадминистраторов службы** и **назначается** для всех остальных пользователей. Щелкните **Администраторы подписки**, чтобы открыть дополнительные сведения об их разрешениях.

### Добавление нового пользователя и назначение роли

1.	В колонке пользователей нажмите кнопку **Добавить**, чтобы открыть **колонку добавления доступа**, куда можно добавить пользователя, группу или приложение и назначить им роль.  

    ![Добавить пользователя](media/automation-role-based-access-control/automation-02-add-user.png)

2.	Выберите роль из списка доступных ролей. Мы выберем роль **Читатель**, но можно выбрать любую из доступных встроенных ролей, поддерживаемых учетной записью службы автоматизации, или пользовательскую роль, которую вы настроили сами.

    ![Выбрать роль](media/automation-role-based-access-control/automation-03-select-role.png)

3.	Щелкните **Добавление пользователей**, чтобы открыть колонку **Добавление пользователей**. Если вы уже добавить пользователей, группы или приложения для управления подпиской, выберите пользователей для добавления доступа в открывшемся списке. Если пользователей в списке нет или нужный вам пользователь не указан, щелкните **Пригласить**, чтобы открыть колонку **Приглашение гостя**, в которой можно пригласить пользователя с адресом электронной почты действительной учетной записи Майкрософт, например Outlook.com, OneDrive или Xbox Live. Указав адрес электронной почты пользователя, щелкните **Выбрать**, чтобы добавить пользователя, а затем нажмите кнопку **ОК**.

    ![Добавление пользователей](media/automation-role-based-access-control/automation-04-add-users.png)
 
Пользователь будет добавлен в колонку **Пользователи** с назначенной ролью **Читатель**.

![Список пользователей](media/automation-role-based-access-control/automation-05-list-users.png)

Кроме того, роль можно назначить пользователю в колонке **Роли**. Щелкните **Роли** в колонке "Пользователи", чтобы открыть колонку **Роли**. В этой колонке отображаются имена ролей и число пользователей и групп, которым она назначена.

![Назначение роли из колонки пользователей](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)
   
>[AZURE.NOTE] Управление доступом на основе ролей настраивается только на уровне учетной записи службы автоматизации и не может настраиваться в ресурсах более низкого уровня.

Пользователю, группе или приложению можно назначить сразу несколько ролей. Например, если добавить пользователю одновременно роль **оператора службы автоматизации** и роль **читателя**, он сможет просматривать все ресурсы службы автоматизации и выполнять задания модулей Runbook. Для просмотра всех назначенных пользователю ролей разверните раскрывающийся список.

![Просмотр нескольких ролей](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)
 
### Удаление пользователя

Разрешение на доступ для пользователя, который не управляет учетной записью службы автоматизации или прекращает работу в организации, можно удалить. Чтобы удалить пользователя, выполните описанные ниже действия.

1.	В колонке **Пользователи** выберите назначение роли, которое нужно удалить.

2.	Нажмите кнопку **Удалить** в колонке сведений о назначении.

3.	Нажмите кнопку **Да**, чтобы подтвердить удаление.

    ![Удаление пользователей](media/automation-role-based-access-control/automation-08-remove-users.png)

## Пользователь с назначенной ролью

Когда пользователь с назначенной ролью входит в свою учетную запись службы автоматизации, он видит учетную запись ее владельца в списке **каталогов по умолчанию**. Для просмотра учетной записи службы автоматизации, в которую он был добавлен, пользователь должен перейти из каталога по умолчанию в каталог владельца по умолчанию.

![Каталог по умолчанию](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)

### Возможности для пользователя с ролью оператора службы автоматизации

Когда пользователь с ролью оператора службы автоматизации просматривает учетную запись службы автоматизации, в которой он был назначен, он видит только список моделей Runbook, задания Runbook и расписания, созданные в учетной записи службы автоматизации, но не видит их определения. Такой пользователь может запускать, останавливать, приостанавливать, возобновлять и планировать задания Runbook. У него не будет доступа к другим ресурсам службы автоматизации, включая конфигурации, гибридные рабочие роли и узлы DSC.

![Нет доступа к ресурсам](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

Когда пользователь выбирает тот или иной модуль Runbook, команды для просмотра источника или правки этого модуля не отображаются, поскольку роль оператора службы автоматизации не позволяет выполнять подобные действия.

![Нет доступа к правке модуля](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)

Пользователь может просматривать и создавать расписания, но не имеет доступа к другим типам активов.

![Нет доступа к активам](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)

Кроме того, пользователь не видит, какие объекты Webhook связаны с модулем Runbook.

![Нет доступа к объектам webhook](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)

## Настройки RBAC для учетной записи службы автоматизации с помощью Azure PowerShell

Доступ к учетной записи службы автоматизации на основе ролей можно также настроить с помощью указанных ниже [командлетов PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).

• Командлет [Get AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) выдает список всех ролей, доступных в RBAC Azure Active Directory. При использовании со свойством **Name** он позволяет получить список всех пользователей в определенной ролью. **Пример:** ![Получение определения роли](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)

• Командлет [Get AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) выдает список назначений ролей Azure RBAC в указанной области. Без параметров эта команда возвращает все назначения ролей, существующие в подписке. Используйте параметр **ExpandPrincipalGroups**, чтобы вывести список назначений доступа для указанного пользователя, а также для групп, членом которых он является. **Пример.** Следующая команда выдает список всех пользователей в учетной записи службы автоматизации с указанием их ролей.

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Получение назначения роли](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• Командлет [New AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) предоставляет доступ пользователям, группам и приложениям в определенной области. **Пример.** Следующая команда создает новую роль оператора службы автоматизации для пользователя в области учетной записи службы автоматизации.

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![Новое назначение роли](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• Командлет [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) удаляет доступ для указанного пользователя, группы или приложения в определенной группе. **Пример.** Следующая команда создает новую роль оператора службы автоматизации для пользователя в области учетной записи службы автоматизации.

    Remove-AzureRmRoleAssignment -SignInName "<sign-in Id of a user you wish to remove>" -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

В представленных выше командлетах замените имя входа, идентификатор подписки, имя группы ресурсов и имя учетной записи службы автоматизации данными своей учетной записи. Нажмите кнопку **Да** в запросе подтверждения, чтобы удалить назначение роли.


## Дальнейшие действия
-  Сведения о различных способах настройки RBAC для службы автоматизации Azure см. в статье [Управление RBAC с помощью Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).
- Дополнительные сведения о различных способах запуска модуля Runbook см. в статье [Запуск модуля Runbook](automation-starting-a-runbook.md).
- Сведения о различных типах см. в статье [Типы модулей Runbook в службе автоматизации Azure](automation-runbook-types.md).

<!---HONumber=AcomDC_0218_2016-->