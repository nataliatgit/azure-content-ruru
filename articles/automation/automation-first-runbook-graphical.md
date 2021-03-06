<properties
    pageTitle="Мой первый графический модуль Runbook в службе автоматизации Azure | Microsoft Azure"
    description="Учебник, в котором описывается создание, тестирование и публикация простого графического модуля Runbook."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="stevenka"
    editor=""/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="02/23/2016"
    ms.author="magoedte;bwren"/>

# Первый графический Runbook

> [AZURE.SELECTOR] - [Graphical](automation-first-runbook-graphical.md) - [PowerShell Workflow](automation-first-runbook-textual.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md)

В данном учебнике описана процедура создания [графического модуля Runbook](automation-runbook-types.md#graphical-runbooks) в службе автоматизации Azure. Для начала мы протестируем и опубликуем простой модуль Runbook и расскажем, как отслеживать состояние его заданий. Затем мы изменим Runbook, настроив его на фактическое управление ресурсами Azure (в данном случае на запуск виртуальной машины Azure). Затем мы создадим еще один, улучшенный модуль Runbook, добавив параметры и условную связь.

## Предварительные требования

Для работы с этим учебником необходимо следующее.

-	Подписка Azure. Если у вас ее нет, [активируйте преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или <a href="/pricing/free-trial/" target="_blank">[подпишитесь на бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/).
-	[Учетная запись в службе автоматизации](automation-configuring.md) для хранения модуля Runbook.
-	Виртуальная машина Azure. Это не должна быть рабочая машина, поскольку в процессе изучения данного материала ее нужно будет остановить и запустить заново.
-	[Пользователь Azure Active Directory и ресурс учетных данных службы автоматизации](automation-configuring.md) для проверки подлинности и получения доступа к ресурсам Azure. Этот пользователь должен иметь разрешение на запуск и остановку виртуальной машины.

## Шаг 1. Создание нового модуля Runbook

Для начала мы создадим простой модуль Runbook, выводящий на экран текст *Привет, мир!*

1.	На портале Azure выберите свою учетную запись в службе автоматизации. Страница учетной записи в службе автоматизации позволяет быстро получить представление о ресурсах, доступных в этой учетной записи. Некоторые ресурсы уже должны быть доступны. Большинство из них — это модули, которые добавляются в каждую новую учетную запись в службе автоматизации по умолчанию. Кроме того, вам потребуется ресурс учетных данных, упомянутый в [предварительных требованиях](#prerequisites).
2.	Щелкните **Модули Runbook**, чтобы открыть список модулей Runbook.<br> ![Элемент управления "Модули Runbook"](media/automation-first-runbook-graphical/runbooks-control.png)
3.	Создайте новый модуль Runbook, щелкнув **Добавить Runbook**, а затем **Создать новый Runbook**.
4.	Присвойте модулю Runbook имя *MyFirstRunbook-Graphical*.
5.	В данном случае мы создаем [графический Runbook](automation-graphical-authoring-intro.md), поэтому выберите в поле **Тип модуля Runbook** значение **Графический**.<br>![Новый Runbook](media/automation-first-runbook-graphical/new-runbook.png)
6.	Щелкните **Создать**, чтобы создать модуль Runbook и открыть графический редактор.

## Шаг 2. Добавление действий в модуль Runbook

Элемент управления "Библиотека" в левой части редактора позволяет выбрать действия для добавления в модуль Runbook. Мы добавим командлет **Write-Output** для вывода текста из модуля Runbook.

1.	В элементе управления "Библиотека" разверните узел **Командлеты**, а затем **Microsoft.PowerShell.Utility**.<br>![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/cmdlets-powershell-utility.png)
2.	Прокрутите список до конца. Щелкните правой кнопкой мыши командлет **Write-Output** и выберите пункт **Добавить на холст**.
3.	Выберите действие **Write-Output** на холсте. Откроется элемент управления "Конфигурация", где можно настроить конфигурацию действия.
4.	В поле **Метка** по умолчанию указывается имя командлета, но можно указать и более понятное название. Введите в это поле значение *Вывод текста "Привет, мир!"*.
5.	Щелкните **Параметры**, чтобы указать значения для параметров командлета. Некоторые командлеты имеют несколько наборов параметров, и вам нужно выбрать, какой из них будет использоваться. В данном случае командлет **Write-Output** имеет только один набор параметров, поэтому выбор делать не нужно.<br>![Свойства командлета Write-Output](media/automation-first-runbook-graphical/write-output-properties.png)
6.	Выберите параметр **InputObject**. В этом параметре мы укажем текст для отправки в поток выходных данных.
7.	В раскрывающемся списке **Источник данных** выберите пункт **Выражение PowerShell**. Раскрывающийся список **Источник данных** включает разные источники, которые можно использовать для заполнения значения параметра. Можно использовать выходные данные из таких источников, как другое действие, ресурс автоматизации или выражение PowerShell. В данном случае нам нужно вывести только текст *Привет, мир!* Для этого мы воспользуемся выражением PowerShell и укажем строку.
8.	В поле **Выражение** введите текст *Привет, мир!* и дважды нажмите кнопку **ОК**, чтобы вернуться к холсту.<br>![Выражение PowerShell](media/automation-first-runbook-graphical/expression-hello-world.png)
9.	Щелкните **Сохранить**, чтобы сохранить модуль Runbook.<br> ![Сохранить Runbook](media/automation-first-runbook-graphical/runbook-edit-toolbar-save.png)

## Шаг 3. Тестирование модуля Runbook

Прежде чем опубликовать модуль Runbook и, таким образом, сделать его доступным для рабочей среды, необходимо проверить, нормально ли он работает. Чтобы протестировать модуль Runbook, нужно запустить его **черновую** версию и проверить его работу в интерактивном режиме.

1.	Щелкните **Область тестирования**.<br> ![Область тестирования](media/automation-first-runbook-graphical/runbook-edit-toolbar-test-pane.png)
2.	Щелкните **Пуск**, чтобы начать тестирование. Активным должен быть только этот параметр.
3.	При этом создается объект [Задание Runbook](automation-runbook-execution.md), а его состояние отображается на панели. Сначала задание получает состояние *В очереди*, показывающее, что оно ожидает доступа к исполнителю Runbook в облаке. Как только исполнитель затребует задание, оно получит состояние *Запущено*, а с началом фактического выполнения модуля Runbook — состояние *Выполняется*.  
4.	Когда задание модуля Runbook будет выполнено, на экране появится результат. В нашем случае это должен быть текст *Привет, мир!*<br>![Hello World](media/automation-first-runbook-graphical/test-output-hello-world.png)
5.	Закройте область тестирования, чтобы вернуться на холст.

## Шаг 4. Публикация и запуск модуля Runbook

Модуль, который мы только что создали, все еще находится в режиме проекта. Прежде чем запустить модуль в рабочей среде, его нужно опубликовать. При публикации модуля Runbook существующая опубликованная версия перезаписывается черновой версией. В нашем случае опубликованной версии не существует, поскольку Runbook был создан только что.

1.	Нажмите кнопку **Опубликовать**, чтобы опубликовать Runbook, а затем кнопку **Да** в появившемся запросе.<br> ![Опубликовать](media/automation-first-runbook-graphical/runbook-edit-toolbar-publish.png)
2.	Прокрутив экран влево до области **Модули Runbook**, вы увидите, что в поле **Состояние авторизации** для данного модуля Runbook появилось значение **Опубликован**.
3.	Прокрутите экран вправо до области **MyFirstRunbook**. Параметры в верхней части экрана позволяют запустить модуль Runbook, запланировать его запуск в определенное время или создать [Webhook](automation-webhooks.md), чтобы модуль можно было запускать с помощью HTTP-вызова.
4.	Нам нужно только запустить модуль Runbook, поэтому щелкните **Пуск**, а затем **Да** в появившемся запросе.<br> ![Запуск модуля Runbook](media/automation-first-runbook-graphical/runbook-toolbar-start.png)
5.	Откроется область заданий с созданным нами заданием Runbook. Эту область можно закрыть, но в данном случае мы оставим ее открытой, чтобы проследить за ходом выполнения задания.
6.	Состояние задания отображается в поле **Сводные данные задания** и отражает состояния, которые мы наблюдали при тестировании модуля Runbook.<br> ![Сводные данные задания](media/automation-first-runbook-graphical/job-pane-summary.png)
7.	Как только состояние модуля Runbook изменится на *Выполнено*, щелкните **Выходные данные**. Откроется область **Выходные данные** с текстом *Привет, мир!*<br> ![Сводные данные задания](media/automation-first-runbook-graphical/job-pane-output.png)  
8.	Закройте область выходных данных.
9.	Щелкните **Потоки**, чтобы открыть область "Потоки" для задания Runbook. В потоке выходных данных должен отображаться только текст *Привет, мир!*, но могут присутствовать и другие потоки заданий Runbook, например "Подробная информация" и "Ошибка", если Runbook записывает в них какие-то данные.<br> ![Сводные данные задания](media/automation-first-runbook-graphical/job-pane-streams.png)
10.	Закройте область потоков и область заданий, чтобы вернуться в область MyFirstRunbook.
11.	Щелкните **Задания**, чтобы открыть область "Задания" для этого Runbook. Откроется список всех заданий, созданных этим модулем Runbook. В нем должно быть только одно задание, так как мы запускали задание только один раз.<br> ![Задания](media/automation-first-runbook-graphical/runbook-control-jobs.png)
12.	Если щелкнуть это задание, откроется та же область заданий, которую мы видели при запуске модуля Runbook. С ее помощью можно вернуться назад и просмотреть сведения о любом задании, созданном для конкретного модуля Runbook.

## Шаг 5. Добавление проверки подлинности для управления ресурсами Azure

Мы протестировали и опубликовали свой модуль Runbook, но пока он не выполняет никаких полезных действий. Нам нужно, чтобы он управлял ресурсами Azure. Для этого модуль должен проходить проверку подлинности, используя учетные данные, упомянутые в [предварительных требованиях](#prerequisites). Для этого используется командлет **Set-AzureAccount**.

1.	Откройте графический редактор, щелкнув **Изменить** в области MyFirstRunbook.<br> ![Изменить Runbook](media/automation-first-runbook-graphical/runbook-toolbar-edit.png)
2.	Название **Вывод текста "Привет, мир!"** нам больше не нужно, поэтому щелкните его правой кнопкой мыши и выберите команду **Удалить**.
3.	В элементе управления "Библиотека" разверните узел **Командлеты**, а затем **Azure**.
4.	Добавьте командлет **Add-AzureAccount** на холст.<br> ![Add-AzureAccount](media/automation-first-runbook-graphical/add-azureaccount.png)
5.	Выберите командлет **Add-AzureAccount** и щелкните **Параметры** в области конфигурации.
6.	Командлет **Add-AzureAccount** имеет несколько наборов параметров, поэтому прежде, чем вводить значения, необходимо выбрать один из наборов параметров. Щелкните **Набор параметров** и выберите вариант **Пользователь**.
7.	Параметры из выбранного набора появятся в области конфигурации параметров действия. Щелкните **Учетные данные**.<br> ![Добавить параметры учетной записи Azure](media/automation-first-runbook-graphical/add-azureaccount-parameters.png)
8.	Нам нужно, чтобы этот командлет использовал ресурс учетных данных из нашей учетной записи в службе автоматизации, поэтому выберите в поле **Источник данных** значение **Ресурс учетных данных**.
9.	Выберите ресурс учетных данных с разрешением на запуск и остановку виртуальной машины в среде Azure.<br> ![Добавить источник данных для учетной записи Azure](media/automation-first-runbook-graphical/credential-data-source.png)
10.	Дважды нажмите кнопку **ОК**, чтобы вернуться на холст.

## Шаг 6. Добавление действия запуска виртуальной машины

Теперь добавим действие **Start-AzureVM** для запуска виртуальной машины. Вы можете выбрать любую виртуальную машину в своей подписке Azure. Имя этой машины будет прописано в коде командлета.

1.	Разверните узел **Командлеты**, а затем **Azure**.
2.	Добавьте командлет **Start-AzureVM** на холст, а затем перетащите его под командлет **Add-AzureAccount**.
3.	Наведите указатель мыши на командлет **Add-AzureAccount**, и в нижней части формы появится кружок. Щелкните этот кружок и подведите стрелку к командлету **Start-AzureVM**. Стрелка, которую вы только что создали, является *связью*. Модуль Runbook будет начинать работу с командлета **Add-AzureAccount**, а затем запускать командлет **Start-AzureVM**.<br> ![Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm.png)
4.	Выберите командлет **Start-AzureVM**. Щелкните **Параметры**, а затем **Набор параметров**, чтобы просмотреть наборы параметров для командлета **Start-AzureVM**. Выберите набор параметров **ПоИмени**. Обратите внимание, что рядом с параметрами **Имя** и **ИмяСлужбы** есть восклицательные знаки. Это означает, что данные параметры являются обязательными.
5.	Выберите параметр **Имя**. Выберите в списке **Источник данных** вариант **Постоянное значение** и введите имя виртуальной машины для запуска. Нажмите кнопку **ОК**.
6.	Выберите параметр **ИмяСлужбы**. Выберите в списке **Источник данных** вариант **Постоянное значение** и введите имя виртуальной машины для запуска. Нажмите кнопку **ОК**.<br> ![Параметры командлета Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm-params.png)
7.	Щелкните область тестирования, чтобы проверить модуль Runbook.
8.	Щелкните **Пуск**, чтобы начать тестирование. После завершения теста проверьте, запущена ли виртуальная машина.

## Шаг 7. Добавление входного параметра в модуль Runbook

На данный момент наш модуль Runbook запускает виртуальную машину, указанную в командлете **Start-AzureVM**, но будет более полезно, если виртуальную машину можно будет указывать при запуске модуля Runbook. Для этого добавим в модуль Runbook входной параметр.

1.	Откройте графический редактор, щелкнув **Изменить** в области **MyFirstRunbook**.
2.	Щелкните **Ввод и вывод**, а затем **Добавить входные данные**, чтобы открыть область входных параметров для модуля Runbook.<br>![Входные и выходные данные Runbook](media/automation-first-runbook-graphical/runbook-edit-toolbar-inputoutput.png)
3.	Укажите для параметра **Имя** значение *ИмяВМ*. Для параметра **Тип** оставьте значение *Строка*, но для параметра **Обязательный** выберите значение *Да*. Нажмите кнопку **ОК**.
4.	Создайте второй обязательный входной параметр с именем *ИмяСлужбыВМ* и нажмите кнопку **ОК**, чтобы закрыть область **Ввод и вывод**.<br> ![Входные параметры Runbook](media/automation-first-runbook-graphical/input-parameters.png)
5.	Выберите действие **Start-AzureVM** и щелкните **Параметры**.
6.	Измените значение в поле **Источник данных** для параметра **Имя** на **Входные данные Runbook**, а затем выберите **ИмяВМ**.<br> ![Параметр "Имя" командлета Start-AzureVM](media/automation-first-runbook-graphical/vmname-property.png)
7.	Измените значение в поле **Источник данных** для параметра **ИмяСлужбы** на **Входные данные Runbook**, а затем выберите **ИмяСлужбыВМ**.<br> ![Параметры командлета Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm-params-inputs.png)
8.	Сохраните модуль Runbook и откройте область тестирования. Обратите внимание, что теперь значения двух входных переменных, которые будут использоваться в тесте, можно указать.
9.	Закройте область тестирования.
10.	Щелкните **Опубликовать**, чтобы опубликовать новую версию модуля Runbook.
11.	Остановите виртуальную машину, запущенную на предыдущем этапе.
12.	Щелкните **Пуск**, чтобы запустить модуль Runbook. Введите **ИмяВМ** и **ИмяСлужбыВМ** для виртуальной машины, которую нужно запустить.<br> ![Запуск модуля Runbook](media/automation-first-runbook-graphical/start-runbook-input-params.png)
13.	Когда модуль Runbook будет выполнен, проверьте, запустилась ли виртуальная машина.

## Шаг 8. Создание условной связи

Теперь изменим Runbook таким образом, чтобы попытка запуска этого модуля предпринималась только в том случае, если он еще не запущен. Для этого добавим в модуль Runbook командлет **Get-AzureVM**, который будет содержать текущее состояние виртуальной машины. Затем мы добавим условную связь, которая будет выполнять командлет **Start-AzureVM** только в том случае, если модуль остановлен. Если модуль Runbook не остановлен, будет выводиться сообщение.

1.	Откройте **MyFirstRunbook** в графическом редакторе.
2.	Удалите связь между командлетами **Add-AzureAccount** и **Start-AzureVM**, щелкнув ее и нажав клавишу *Delete*.
3.	В элементе управления "Библиотека" разверните узел **Командлеты**, а затем **Azure**.
4.	Добавьте командлет **Get-AzureVM** на холст.
5.	Создайте связь от командлета **Add-AzureAccount** к командлету **Get-AzureVM**. Создайте еще одну связь — от командлета **Get-AzureVM** к командлету **Start-AzureVM**.<br>![Runbook с командлетом Get-AzureVM](media/automation-first-runbook-graphical/get-azurevm.png)  
6.	Выберите командлет **Get-AzureVM** и щелкните **Параметры**. Выберите набор параметров *ПолучитьВМпоИмениСлужбыИВМ*.
7.	Для параметра **Имя** выберите в поле **Источник данных** значение **Входные данные Runbook**, а затем выберите**ИмяВМ**.  
8.	Для параметра **ИмяСлужбы** укажите в поле **Источник данных** значение **Входные данные Runbook**, а затем выберите **ИмяСлужбыВМ**.  
9.	Выберите связь между командлетами **Get-AzureVM** и **Start-AzureVM**.
10.	В области конфигурации измените значение параметра **Применять условие** на **Верно**. Обратите внимание, что связь станет пунктирной, показывая, что целевое действие будет выполняться только при соблюдении заданного условия.
11.	В поле **Выражение условия** введите *$ActivityOutput['Get-AzureVM'].PowerState -eq "Stopped"*. Теперь командлет **Start-AzureVM** будет выполняться, только если виртуальная машина остановлена.<br> ![Условие связи](media/automation-first-runbook-graphical/link-condition.png)
12.	В элементе управления "Библиотека" разверните узел **Командлеты** и выберите **Microsoft.PowerShell.Utility**.
13.	Добавьте командлет **Write-Output** на холст.
14.	Создайте связь от командлета **Get-AzureVM** к командлету **Write-Output**.
15.	Выберите эту связь и измените значение параметра **Применять условие** на **Верно**.
16.	В поле **Выражение условия** введите *$ActivityOutput['Get-AzureVM'].PowerState -ne "Stopped"*. Теперь командлет **Write-Output** будет выполняться, только если виртуальная машина не остановлена.<br> ![Runbook с командлетом Write-Output](media/automation-first-runbook-graphical/write-output-link.png)
17.	Выберите командлет **Write-Output** и щелкните **Параметры**.
18.	Для параметра **ОбъектВвода** измените значение в поле **Источник данных** на **Выражение PowerShell** и введите выражение *"$VMName.Name already started"*.
19.	Сохраните модуль Runbook и откройте область тестирования.
20.	Запустите модуль Runbook при остановленной виртуальной машине; модуль должен запуститься.
21.	Запустите модуль Runbook при запущенной виртуальной машине; должно появиться сообщение о том, что модуль уже запущен.

## Связанные статьи

-	[Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md)
-	[Первый Runbook рабочего процесса PowerShell](automation-first-runbook-textual.md)
-	[Мой первый модуль Runbook PowerShell](automation-first-runbook-textual-PowerShell.md)

<!---HONumber=AcomDC_0302_2016-->