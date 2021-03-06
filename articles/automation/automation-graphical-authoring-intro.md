<properties 
   pageTitle="Графическая разработка в службе автоматизации Azure"
   description="Графическая разработка позволяет создавать модули Runbook для службы автоматизации Azure без работы с кодом. Эта статья содержит вводные сведения о графическом создании и всю информацию, необходимую, чтобы приступить к созданию графического Runbook."
   services="automation"   
   documentationCenter=""
   authors="mgoedtel"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="magoedte;bwren" />

# Графическая разработка в службе автоматизации Azure

## Введение

Графическая разработка позволяет создавать модули Runbook для службы автоматизации Azure без сложностей программирования в Windows PowerShell. Вы добавляете на холст действия из библиотеки командлетов и других действий и связываете их вместе, чтобы сформировать рабочий процесс.

Эта статья содержит вводные сведения о графическом создании и основные понятия, необходимые, чтобы приступить к созданию графического Runbook.

## Графические модули Runbook

Все модули Runbook в службе автоматизации Azure представляют собой рабочие процессы Windows PowerShell. Графические модули Runbook создают код PowerShell, который запускается рабочими процессами службы автоматизации, но который вы не можете непосредственно просмотреть или изменить. Графические модули Runbook нельзя преобразовать в текстовые модули Runbook, а существующий текстовый Runbook нельзя импортировать в графический редактор.


## Обзор графического редактора

Графический редактор можно открыть на портале Azure, создав или открыв для редактирования графический Runbook.

![Графическая рабочая область](media/automation-graphical-authoring-intro/graphical-editor.png)


В следующих разделах описываются элементы управления в графическом редакторе.


### Холст
Холст — это место, где формируется Runbook. Вы добавляете в Runbook действия из узлов в элементе управления "Библиотека" и соединяете их с помощью связей, чтобы определить логику Runbook.

Для увеличения и уменьшения масштаба можно использовать элементы управления в нижней части холста.

![Графическая рабочая область](media/automation-graphical-authoring-intro/canvas-zoom.png)

### Элемент управления "Библиотека"

В элементе управления "Библиотека" можно выбрать [действия](#activities) для добавления в Runbook. Вы добавляете их на холст, где соединяете с другими действиями. Он содержит четыре раздела, описанные в следующей таблице.

| Раздел | Описание |
|:---|:---|
| Командлеты | Включает в себя все командлеты, которые могут использоваться в модуле Runbook. Командлеты группируются по модулям. Будут доступны все модули, которые установлены в вашей учетной записи службы автоматизации. |
| Модули Runbook | Содержит модули Runbook в вашей учетной записи службы автоматизации, упорядоченные по тегам. Так как у Runbook может быть более одного тега, он может отображаться в списке по нескольким тегам. Такие модули Runbook можно добавлять на холст в качестве дочерних Runbook. Текущий редактируемый Runbook отображается, но его нельзя добавить на холст, так как Runbook не может вызывать сам себя.
| Активы | Содержит [ресурсы службы автоматизации](http://msdn.microsoft.com/library/dn939988.aspx) в вашей учетной записи службы автоматизации, которые можно использовать в модуле Runbook. При добавлении ресурса в Runbook добавляется действие рабочего процесса, которое получает выбранный ресурс. В случае ресурсов переменных можно либо добавить действие для получения переменной, либо задать переменную.
| Элемент управления "Runbook" | Содержит действия элемента управления "Runbook", которые могут использоваться в текущем модуле Runbook. *Соединения* принимает несколько входных данных и ожидает, пока обработка их всех не будет завершена, прежде чем продолжить рабочий процесс. *Сценарий рабочего процесса* выполняет одну или несколько строк кода рабочего процесса PowerShell. Это действие можно использовать для пользовательского кода или функции, которую не удается реализовать с помощью других действий.|

### Элемент управления "Конфигурация"

В элементе управления "Конфигурация" показаны данные объекта, выбранного на холсте. Свойства, доступные в этом элементе управления, будут зависеть от типа выбранного объекта. При выборе параметра в элементе управления "Конфигурация" открываются дополнительные колонки с дополнительной информацией.

### Элемент управления "Тест"

Элемент управления "Тест" не отображается при первом запуске графического редактора. Он открывается, когда вы интерактивно [тестируете графический Runbook](#graphical-runbook-procedures).

## Процедуры графического Runbook 

### Экспорт и импорт графического модуля Runbook

Можно экспортировать только опубликованную версию графического модуля Runbook. Если Runbook еще не был опубликован, то кнопка **Экспортировать опубликованный** будет неактивна. При нажатии кнопки **Экспортировать опубликованный** модуль Runbook скачивается на локальный компьютер. Имя файла соответствует имени модуля Runbook. Расширение файла — *GRAPHRUNBOOK*.

![Экспортировать опубликованный](media/automation-graphical-authoring-intro/runbook-export.png)

Файл графического модуля Runbook можно импортировать, выбрав параметр **Импорт** при добавлении Runbook. При выборе файла для импорта можно сохранить то же **имя** или ввести новое.

![Импортировать Runbook](media/automation-graphical-authoring-intro/runbook-import.png)


### Тестирование графического Runbook

Вы можете протестировать черновую версию Runbook на портале Azure, оставив при этом опубликованную версию Runbook без изменений, или протестировать новую версию до ее публикации. Это позволяет проверить правильность работы Runbook перед заменой опубликованной версии. При тестировании Runbook исполняется черновой Runbook и выполняются все его действия. Журнал заданий не создается, но выходные данные отображаются в области вывода теста.

Откройте элемента управления "Тест" для Runbook, открыв Runbook для редактирования и нажав кнопку **области тестирования**.

![Кнопка панели "Тест"](media/automation-graphical-authoring-intro/runbook-edit-test-pane.png)

Будет предложено ввести входные параметры, после чего вы сможете запустить Runbook, нажав кнопку **Запуск**.

![Кнопки элемента управления "Тест"](media/automation-graphical-authoring-intro/runbook-test-start.png)

### Публикация графического Runbook

У каждого Runbook в службе автоматизации Azure есть черновая и опубликованная версия. Запустить можно только опубликованную версию, а изменить — только черновую. Изменения, внесенные в черновик, не влияют на опубликованную версию. Когда черновая версия готова, ее можно опубликовать, заменив опубликованную версию содержимым черновой.

Графический Runbook можно опубликовать, открыв его для редактирования и нажав кнопку **Опубликовать**.

![Кнопка "Опубликовать"](media/automation-graphical-authoring-intro/runbook-edit-publish.png)

Если Runbook еще не опубликован, он находится в состоянии **Новый**. После публикации он находится в состоянии **Опубликован**. Если изменить Runbook после публикации, и черновая и опубликованная версии отличаются, Runbook находится в состоянии **Редактирование**.

![Состояния Runbook](media/automation-graphical-authoring-intro/runbook-statuses.png)

Также имеется возможность восстановить опубликованную версию Runbook. При этом будут отброшены все изменения, внесенные с момента последней публикации Runbook, и черновая версия Runbook будет заменена опубликованной версией.

![Кнопка "Вернуть опубликованную"](media/automation-graphical-authoring-intro/runbook-edit-revert-published.png)


## Действия

Действия являются стандартными блоками модуля Runbook. Действием может быть командлет PowerShell, дочерний Runbook или действие рабочего процесса. Вы можете добавить действия в Runbook, щелкнув его правой кнопкой мыши в элементе управления "Библиотека" и выбрав пункт **Добавить на холст**. Затем можно щелкнуть и перетащить действие, чтобы поместить его на холсте туда, куда необходимо. Расположение действия на холсте никак не влияет на работу Runbook. Можно по-разному компоновать Runbook, однако вы поймете, что удобнее всего наглядно представить его работу.

![Добавление на холст](media/automation-graphical-authoring-intro/add-to-canvas.png)

Выберите действие на холсте, чтобы настроить его свойства и параметры в колонке "Конфигурация". Вы можете заменить **метку** действия чем-нибудь, что будет описывать его. Исходный командлет будет выполняться без изменений, вы просто меняете его отображаемое имя, которое будет использоваться в графическом редакторе. Метки должны быть уникальными в пределах Runbook.

### Наборы параметров

Набор параметров определяет обязательные и необязательные параметры, которые будет принимать значения для определенного командлета. У каждого командлета имеется по крайней мере один набор параметров, а у некоторых их несколько. Если у командлета несколько наборов параметров, перед настройкой параметров необходимо выбрать, какой из них будет использоваться. Параметры, которые можно настроить, будут зависеть от выбранного набора параметров. Можно изменить набор параметров, используемый действием, щелкнув **Набор параметров** и выбрав другой набор. В этом случае все настроенные значения параметров будут потеряны.

В следующем примере у командлета Get-AzureVM два набора параметров. Нельзя настроить значения параметров, пока не выбран один из наборов параметров. Набор параметров ListAllVMs предназначен для возврата всех виртуальных машин, и в нем один необязательный параметр. GetVMByServiceandVMName предназначен для указания виртуальной машины, которую следует вернуть, и имеет один обязательный и два необязательных параметра.

![Набор параметров](media/automation-graphical-authoring-intro/parameter-set.png)

#### Значения параметров

При указании значения для параметра вы выбираете источник данных, чтобы определить, как будет указано значение. Источники данных, доступные для определенного параметра, будут зависеть от допустимых значений для этого параметра. Например, значение NULL будет недоступно для параметра, который не допускает значений NULL.

| Источник данных | Описание |
|:---|:---|
|Постоянное значение|Введите значение для параметра. Доступно только для следующих типов данных: Int32, Int64, String, Boolean, DateTime, Switch. |
|Выходные данные действия|Выходные данные действия, предшествующего текущему действию в рабочем процессе. Будут перечислены все допустимые действия. Выберите действие, чтобы использовать его выходные данные для значения параметра. Если действие выводит объект с несколькими свойствами, то можно ввести имя свойства после выбора действия.|
|Входной параметр Runbook|Выберите входной параметр Runbook в качестве входных данных для параметра действия.|  
|Ресурс переменных службы автоматизации|Выберите переменную службы автоматизации в качестве входных данных.|  
|Ресурс учетных данных службы автоматизации|Выберите учетные данные службы автоматизации в качестве входных данных.|  
|Ресурс сертификатов службы автоматизации|Выберите сертификат службы автоматизации в качестве входных данных.|  
|Ресурс подключений службы автоматизации|Выберите подключение службы автоматизации в качестве входных данных.| 
|Выражение PowerShell|Укажите простое [выражение PowerShell](#powershell-expressions). Выражение оценивается перед выполнением действия, и его результат используется в качестве значения параметра. Вы можете использовать переменные, чтобы ссылаться на выходные данные действия или входной параметр Runbook.|
|Пустая строка|Пустое строковое значение.|
|NULL|Значение NULL.|
|Отменить выбор|Очищает все значения, которые были настроены ранее.|


#### Необязательные дополнительные параметры

У всех командлетов будет возможность предоставить дополнительные параметры. Это могут быть общие параметры PowerShell или другие пользовательские параметры. Появится текстовое поле, где можно будет указать параметры с помощью синтаксиса PowerShell. Например, чтобы использовать общий параметр **Verbose**, необходимо указать **"-Verbose:$True"**.

### Повтор действий

**Режим повтора** обеспечивает многократное выполнение действий — вплоть до удовлетворения определенного условия. Эта функция используется для действий, которые необходимо выполнить несколько раз (включая те, которые могут выполняться с ошибками).

Включая для действия режим повтора, можно задать условие и значение задержки. Задержка — это время (в секундах или минутах) ожидания модуля Runbook перед повторным выполнением действия. Если задержка не указана, действие будет повторно выполнено сразу же после завершения.

![Задержка повтора действия](media/automation-graphical-authoring-intro/retry-delay.png)

Условие повтора — это выражение PowerShell, которое вычисляется после каждого выполнения действия. Если выражение возвращает значение True, действие выполняется повторно. Если выражение возвращает значение False, действие не выполняется повторно, а модуль Runbook переходит к следующему действию.

![Задержка повтора действия](media/automation-graphical-authoring-intro/retry-condition.png)

В качестве условия повтора может использоваться переменная с именем $RetryData, которая предоставляет доступ к сведениям о попытках выполнить действие. У этой переменной есть свойства, приведенные в таблице ниже.

| Свойство | Описание |
|:--|:--|
| NumberOfAttempts | Количество запусков действия. |
| Выходные данные | Результат последнего выполнения действия. |
| TotalDuration | Время с момента первого запуска действия. |
| StartedAt | Время первого запуска действия в формате UTC. |

Ниже приведены примеры условий, при которых действие выполняется повторно.

	# Run the activity exactly 10 times.
	$RetryData.NumberOfAttempts -ge 10 

	# Run the activity repeatedly until it produces any output.
	$RetryData.Output.Count -ge 1 

	# Run the activity repeatedly until 2 minutes has elapsed. 
	$RetryData.TotalDuration.TotalMinutes -ge 2

### Элемент управления сценария рабочего процесса

Элемент управления сценария рабочего процесса — специальное действие, которое принимает код рабочего процесса PowerShell, чтобы обеспечить функциональность, которая иначе недоступна. Это не полный рабочий процесс, но он должен содержать допустимые строки кода рабочего процесса PowerShell. Он не может принимать параметры, но может использовать переменные для выходных данных действия и входных параметров Runbook. Любые выходные данные действия добавляются в шину данных, если нет исходящей связи. Если она есть, они добавляется в выходные данные Runbook.

Например, следующий код выполняет вычисление дат с помощью входной переменной Runbook — $NumberOfDays. Затем он отправляет вычисленную дату и время как выходные данные для использования последующими действиями в Runbook.

    $DateTimeNow = InlineScript{(Get-Date).ToUniversalTime()}
    $DateTimeStart = InlineScript{($using:DateTimeNow).AddDays(-$using:NumberOfDays)}
	$DateTimeStart


## Связи и рабочий процесс

**Связь** в графическом Runbook соединяет два действия. Она отображается на холсте как стрелка от исходного действия к действию назначения. Действия выполняются в направлении стрелки: действие назначения запускается после завершения исходного действия.

### Создание связи

Создайте связь между двумя действиями, выбрав исходное действие и щелкнув круг в нижней части фигуры. Перетащите стрелку к действию назначения и отпустите ее.

![Создание связи](media/automation-graphical-authoring-intro/create-link.png)

Выберите связь, чтобы настроить ее свойства в колонке "Конфигурация". К ним также относится тип связи, который описан в следующей таблице.

| Тип связи | Описание |
|:---|:---|
| Конвейер | Действие назначения выполняется один раз для каждого вывода объекта из исходного действия. Действие назначения не выполняется, если исходное действие не создает выходные данные. Выходные данные из исходного действия доступны в виде объекта. |
| Последовательность | Действие назначения выполняется только один раз. Оно получает массив объектов из исходного действия. Выходные данные из исходного действия доступны в виде массива объектов. |

### Запуск действия

Графический Runbook начнется с любых действий, у которых нет входящей связи. Часто это будет только одно действие, которое будет выступать в качестве начального действия Runbook. Если у нескольких действий нет входящих связей, Runbook будет запущен при их одновременном выполнении. Затем, по завершении каждого из них, он будет переходить по связям, выполняя другие действия.

### Условия

Если для связи указано условие, действие назначения выполняется, только если условие возвращает true. В условии обычно используется переменная $ActivityOutput для получения выходных данных исходного действия.

В конвейерной связи указывается условие для одного объекта, и оно вычисляется для каждого вывода объекта исходным действием. Затем действие назначения выполняется с каждым объектом, который удовлетворяет условию. Например, если исходное действие — Get-AzureVM, следующий синтаксис может использоваться в условной конвейерной связи для получения только виртуальных машин, которые запущены в данный момент.

	$ActivityOutput['Get-AzureVM'].PowerState -eq 'Started'

Для последовательной связи условие вычисляется только один раз, так как возвращается один массив, содержащий все объекты, выведенные исходным действием. По этой причине последовательную связь нельзя использовать для фильтрации, как конвейерную связь, так как она будет просто определять, выполняется ли следующее действие. В следующем коде показан тот же пример, оценивающий выходные данные Get-AzureVM, чтобы определить выполняющиеся виртуальные машины. В этом случае код проходит по всем объектам в массиве и возвращает значение true, если хотя бы одна виртуальная машина запущена. Действие назначения будет отвечать за анализ этих данных.

	$test = $false
	$VMs = $ActivityOutput['Get-AzureVm']
	Foreach ($VM in VMs)
	{
		If ($VM.PowerState –eq 'Started')
			{
				$test = $true
			}
	}
	$test

При использовании условной связи данные из исходного действия, доступные для других действий в этой ветви, будут фильтроваться по условию. Если действие является источником нескольких связей, данные, доступные для действий в каждой ветви, будут зависеть от условия в связи, присоединенной к этой ветви.

Например, исходное действие в Runbook ниже получает все виртуальные машины. У него две условные связи и связь без условия. В первой условной связи используется выражение *$ActivityOutput['Get-AzureVM'].PowerState -eq 'Started'* для фильтрации только тех виртуальных машин, которые запущены в настоящий момент. Во второй используется выражение *$ActivityOutput['Get-AzureVM'].PowerState -eq 'Stopped'*, которое отбирает только остановленные виртуальные машины.

![Пример условной связи](media/automation-graphical-authoring-intro/conditional-links.png)

Любое действие, следующее по первой связи и использующее выходные данные действия Get-AzureVM, получит только виртуальные машины, которые были запущены на момент выполнения Get-AzureVM. Любое действие, которое следует по второй связи, получит только виртуальные машины, которые были остановлены на момент выполнения Get-AzureVM. Действие, следующее по третьей связи, получит все виртуальные машины вне зависимости от того, запущены они или нет.

### Соединения

Соединение — это специальное действие, которое будет ожидать завершения всех входящих ветвей. Это дает возможность параллельно выполнять несколько действий и гарантировать, что все они будут завершены перед выполнением следующего шага.

Хотя у соединения может быть неограниченное число входящих связей, конвейерной может быть только одна из них. Число входящих последовательных связей не ограничено. Вы сможете создать соединение с несколькими входящими конвейерными связями и сохранить Runbook, но его запуск завершится ошибкой.

В приведенном ниже примере показана часть Runbook, которая запускает набор виртуальных машин, одновременно скачивая исправления для них. Соединение используется, чтобы гарантировать, что оба процесса будут выполнены, прежде чем выполнение Runbook будет продолжено.

![Соединение](media/automation-graphical-authoring-intro/junction.png)

### Циклические процессы

Циклический процесс происходит, когда действие назначения обратно связано с исходным действием или другим действием, которое в конечном итоге обратно связано с его исходным действием. В настоящее время использование циклических процессов в графической разработке запрещено. Если Runbook содержит циклический процесс, он будет сохранен правильно, но при его выполнении произойдет ошибка.

![Циклический процесс](media/automation-graphical-authoring-intro/cycle.png)

### Циклы

Цикл происходит, когда действие повторяется указанное число раз или продолжает повторяться, пока не будет выполнено определенное условие. Сейчас циклы не поддерживаются в графических Runbook.

### Обмен данными между действиями

Любые данные, которые выводятся действием с исходящей связью, записываются в *шину данных* для Runbook. Любое действие в Runbook может использовать данные в шине данных, чтобы заполнить значения параметров или добавить эти данные в код сценария. Действие может обращаться к выходным данным любого предыдущего действия в рабочем процессе.

То, как данные записываются в шину данных, зависит от типа связи действия. Для **конвейерной** связи данные выводятся в виде нескольких объектов. Для **последовательной** связи данные выводятся в виде массива. Если имеется только одно значение, оно будет выведено в виде одноэлементного массива.

Получить доступ к данным в шине данных можно одним из двух методов. Первый — использовать источник данных **Выходные данные действия** для заполнения параметра другого действия. Если выходными данными является объект, можно указать одно свойство.

![Выходные данные действия](media/automation-graphical-authoring-intro/activity-output-datasource.png)

Также можно получить выходные данные действия из источника данных **Выражение PowerShell** или действия **Сценарий рабочего процесса** с использованием переменной ActivityOutput. Если выходными данными является объект, можно указать одно свойство. Для переменных ActivityOutput используется следующий синтаксис.

	$ActivityOutput['Activity Label']
	$ActivityOutput['Activity Label'].PropertyName 

### Контрольные точки

В графическом модуле Runbook можно задать [контрольные точки](automation-powershell-workflow/#checkpoints), выбрав параметр *Создать контрольную точку для модуля Runbook* для любого действия. Это настраивает создание контрольной точки после выполнения действия.

![Контрольная точка](media/automation-graphical-authoring-intro/set-checkpoint.png)

Процедура создания контрольных точек в модуле Runbook применима и для графических модулей Runbook. При приостановке и перезапуске модуля Runbook из контрольной точки в другой рабочей роли все действия с контрольными точками должны наследоваться. Если такой модуль использует командлеты Azure, это можно сделать с помощью командлета Add-AzureRMAccount.


## Аутентификация для доступа к ресурсам Azure

Большинству модулей Runbook службы автоматизации Azure для доступа к ресурсам Azure потребуется проходить аутентификацию. Обычный метод аутентификации — командлет Add-AzureAccount с [ресурсом учетных данных](http://msdn.microsoft.com/library/dn940015.aspx), представляющим пользователя Active Directory с доступом к учетной записи Azure. Это описано в разделе [Настройка службы автоматизации Azure](automation-configuring.md).

Вы можете добавить эту функциональную возможность в графический Runbook, сначала добавив на холст ресурс учетных данных, а затем — действие Add-AzureAccount. Add-AzureAccount использует действие учетных данных в качестве входных данных. Это продемонстрировано в следующем примере.

![Действия аутентификации](media/automation-graphical-authoring-intro/authentication-activities.png)

Проходить аутентификацию необходимо при запуске Runbook и после каждой контрольной точки. Это означает добавление действия Add-AzureAccount после любого действия Checkpoint-Workflow. Так как можно использовать одно действие учетных данных, другие добавлять не требуется.

![Выходные данные действия](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## Входные и выходные данные Runbook

### Входные данные Runbook

Может потребоваться ввод от пользователя при запуске Runbook на портале Azure или из другого Runbook, если текущий используется в качестве дочернего. Например, если имеется Runbook, который создает виртуальную машину, при каждом запуске Runbook вам может потребоваться предоставлять такие данные, как имя виртуальной машины и другие свойства.

Для приема входных данных Runbook можно определить один или несколько входных параметров. Значения для этих параметров вводятся при каждом запуске Runbook. При запуске Runbook с помощью портала Azure вам будет предложено ввести значения для всех входных параметров Runbook.

Входные параметры для модуля можно открыть, нажав кнопку **Вход и выход** на панели инструментов Runbook.

![Входные и выходные данные Runbook](media/automation-graphical-authoring-intro/runbook-edit-input-output.png)

Откроется элемент управления **Вход и выход**, в котором можно изменить существующий входной параметр или создать новый, нажав кнопку **Добавить входные данные**.

![Добавление входных данных](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Каждый входной параметр определяется с помощью свойств, указанных в следующей таблице.

|Свойство|Описание|
|:---|:---|
| Имя | Уникальное имя параметра. Оно может содержать только буквы или цифры и не может содержать пробел. |
| Описание | Необязательное описание входного параметра. |
| Тип | Предполагаемый тип данных значения параметра. Портал Azure предоставит соответствующий элемент управления для типа данных каждого параметра при запросе ввода его значения. |
| Обязательно | Указывает, должно ли быть указано значение для параметра. Запустить Runbook не удастся, если не указать значение для каждого обязательного параметра, у которого нет определенного значения по умолчанию. |
| Значение по умолчанию | Указывает, какое значение используется для параметра, если оно не указано. Это может быть NULL или какое-нибудь определенное значение. |


### Выходные данные Runbook

Данные, созданные каким-либо действием, у которого нет исходящей связи, будут добавлены в [выходные данные Runbook](http://msdn.microsoft.com/library/azure/dn879148.aspx). Выходные данные сохраняются заданием Runbook и доступны родительскому модулю Runbook, если Runbook используется как дочерний.


## Выражения PowerShell

Одним из преимуществ графической разработки является возможность создавать модули Runbook, располагая минимальными знаниями о PowerShell. В настоящее время необходимо знать лишь некоторые общие принципы работы с PowerShell для заполнения определенных [значений параметров](#activities) и настройки [условий связи](#links-and-workflow). Данный раздел содержит краткое введение в выражения PowerShell для тех пользователей, которые с ними не знакомы. Полные сведения о PowerShell можно найти в статье [Создание сценариев с помощью Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx).


### Выражение PowerShell как источник данных

Выражение PowerShell можно использовать в качестве источника данных для заполнения значения [параметра действия](#activities) результатами некоторого кода PowerShell. Это может быть одна строка кода, которая выполняет некоторые простые функции, или несколько строк, выполняющие определенную сложную логику. Любые выходные данные команды, не присвоенные переменной, выводятся в значении параметра.

Например, следующая команда будет выводить текущую дату.

	Get-Date

Следующие команды создают строку из текущей даты и присваивают ее переменной. Затем содержимое переменной передается в выходные данные.

	$string = "The current date is " + (Get-Date)
	$string

Следующие команды вычисляют текущую дату и возвращают строку, указывающую, является ли текущий день выходным или рабочим.

	$date = Get-Date
	if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
	else { "Weekday" }
	
 

### Выходные данные действия

Чтобы использовать выходные данные предыдущего действия в модуле Runbook, используйте переменную $ActivityOutput со следующим синтаксисом.

	$ActivityOutput['Activity Label'].PropertyName

Например, при наличии действия со свойством, которое требует имя виртуальной машины, можно использовать следующее выражение.

	$ActivityOutput['Get-AzureVm'].Name

При наличии свойства, требующего объект виртуальной машины вместо одного только свойства, можно вернуть весь объект, используя следующий синтаксис.

	$ActivityOutput['Get-AzureVm']

Выходные данные действия также можно использовать в более сложном выражении следующего вида, добавляющем текст к имени виртуальной машины.

	"The computer name is " + $ActivityOutput['Get-AzureVm'].Name


### Условия

Используйте [операторы сравнения](https://technet.microsoft.com/library/hh847759.aspx) для сравнения значений или определения того, соответствует ли значение указанному шаблону. Оператор сравнения возвращает значение $true или $false.

Например, следующее условие определяет, *остановлена* ли виртуальная машина из действия *Get-AzureVM* в данный момент.

	$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"

Следующее условие проверяет, находится ли та же виртуальная машина в любом состоянии, кроме *остановлено*.

	$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"

Можно объединить несколько условий с помощью [логического оператора](https://technet.microsoft.com/library/hh847789.aspx), например **И** или **ИЛИ**. Например, следующее условие проверяет, находится ли виртуальная машина из предыдущего примера в состоянии *остановлена* или *идет остановка*.

	($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping") 


### Хэш-таблицы

[Хэш-таблицы](http://technet.microsoft.com/library/hh847780.aspx) представляют собой пары "имя-значение", которые удобны для получения набора значений. Свойства для определенных действий могут ожидать хэш-таблицу вместо простого значения. Иногда хэш-таблицу называют словарем.

Хэш-таблицу можно создать, используя следующий синтаксис. Хэш-таблица может содержать любое количество записей, каждая из которых определяется именем и значением.

	@{ <name> = <value>; [<name> = <value> ] ...}

Например, следующее выражение создает хэш-таблицу для использования в источнике данных для параметра действия, который ожидает хэш-таблицу со значениями для поиска в Интернете.

	$query = "Azure Automation"
	$count = 10
	$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
	$h

В следующем примере выходные данные действия *Get Twitter Connection* используются для заполнения хэш-таблицы.

	@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
	  'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
	  'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
	  'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}



## Связанные статьи

- [Изучение рабочего процесса Windows PowerShell](automation-powershell-workflow.md)
- [Ресурсы службы автоматизации](http://msdn.microsoft.com/library/azure/dn939988.aspx)
- [Операторы](https://technet.microsoft.com/library/hh847732.aspx)
 

<!---HONumber=AcomDC_0302_2016-->