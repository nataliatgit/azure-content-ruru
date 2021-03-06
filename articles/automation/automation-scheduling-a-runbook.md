<properties 
   pageTitle="Создание расписания для Runbook в службе автоматизации Azure"
   description="Рассматривается создание расписания в службе автоматизации Azure, которое позволяет запускать модуль Runbook однократно или несколько раз в определенное время."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/03/2016"
   ms.author="bwren" />

# Создание расписания для Runbook в службе автоматизации Azure

Чтобы запускать модуль Runbook в определенное время с помощью расписания в службе автоматизации Azure, его необходимо привязать к одному или нескольким расписаниям. Расписание можно настроить как на однократный запуск, так и на повторный запуск через определенное количество часов или дней. Один модуль Runbook может быть связан с несколькими расписаниями, и одно расписание может иметь несколько привязанных модулей Runbook.

## Создание расписания

Создать новое расписание можно как с помощью портала управления Azure, так и с помощью Windows PowerShell. Также расписание можно создать при связывании Runbook с расписанием с помощью портала управления Azure.

### Создание расписания с помощью портала управления Azure

1. На портале управления Azure выберите службу автоматизации и затем имя учетной записи.
1. Выберите вкладку **Средства**.
1. Нажмите кнопку **Добавить параметр** в нижней части окна.
1. Щелкните **Добавить расписание**.
1. Введите **Имя** и при необходимости **Описание** нового расписания.
1. Укажите, будет ли расписание выполнено **Один раз**, или оно будет выполняться **Ежечасно** или **Ежедневно**.
1. Укажите **Время начала** и другие параметры в зависимости от типа выбранного расписания.

### Создание расписания с помощью Windows PowerShell

Для создания расписания в службе автоматизации Azure вам понадобится командлет [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx). Укажите время начала расписания и то, будет ли оно запущено один раз, или оно будет запускаться ежечасно или ежедневно.

Приведенные ниже примеры команд демонстрируют создание нового расписания, которое запускается каждый день в 15:30 начиная с 20 января 2015 г.

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –StartTime "1/20/2015 15:30:00" –DayInterval 1

## Связывание расписания с модулем Runbook

Один модуль Runbook может быть связан с несколькими расписаниями, и одно расписание может иметь несколько привязанных модулей Runbook. Если Runbook имеет параметры, для них можно указать значения. Необходимо предоставить значения для всех обязательных параметров, а также значения для необязательных параметров. Значения будут применяться каждый раз при запуске модуля Runbook с помощью расписания. Один и тот же модуль Runbook можно привязать к другому расписанию и указать другие значения параметров.

### Связывание расписания с модулем Runbook с помощью портала управления Azure

1. На портале управления Azure выберите **Служба автоматизации** и затем имя учетной записи службы автоматизации.
1. Выберите вкладку **Модули Runbook**.
1. Щелкните имя одного модуля Runbook для привязки к расписанию.
1. Перейдите на вкладку **Расписание**.
2. Если модуль Runbook в настоящее время не связан с расписанием, будет предоставлена возможность выбрать **ссылку на новое расписание** или **ссылку на существующее расписание**. Если модуль в настоящее время связан с расписанием, щелкните **Ссылки** в нижней части окна для доступа к этим параметрам.
1. Если модуль Runbook имеет параметры, их необходимо будет заполнить.  

### Связывание расписания с модулем Runbook с помощью Windows PowerShell

Чтобы связать расписание с модулем Runbook, вам понадобится командлет [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx). С помощью параметра "Параметры" можно указать значения параметров для модуля Runbook. Дополнительные сведения об указании значений параметров см. в разделе [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).

Приведенные ниже примеры команд показывают связывание расписания с модулем Runbook с параметрами.

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Test-Runbook"
	$scheduleName = "Sample-DailySchedule"
	$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
	Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## Отключение расписания

Если расписание отключено, модули Runbook, связанные с ним, больше не будут запускаться по такому расписанию. Можно отключить расписание вручную или задать срок действия для ежечасных и ежедневных расписаний в момент их создания. При достижении срока действия расписания будут отключены.

### Отключение расписания с помощью портала управления Azure

Отключить расписание на портале управления Azure можно на странице "Сведения о расписании" для соответствующего расписания.

1. На портале управления Azure выберите службу автоматизации и затем имя учетной записи.
1. Перейдите на вкладку "Средства".
1. Щелкните имя расписания и откройте страницу со сведениями о нем.
2. Задайте значение **Нет** для параметра **Включено**.

### Отключение расписания с помощью Windows PowerShell

Для изменения свойств существующего расписания воспользуйтесь командлетом [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx). Чтобы отключить расписание, задайте значение **False** для параметра **IsEnabled**.

Приведенные ниже примеры команд показывают, как отключить расписание.

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –IsEnabled $false

## Связанные статьи

- [Создание расписания для средств в службе автоматизации Azure](http://msdn.microsoft.com/library/azure/dn940016.aspx)
- [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md) 

<!---HONumber=AcomDC_0204_2016-->