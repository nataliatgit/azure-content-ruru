<properties
   pageTitle="Настройка системы диагностики для облачных служб и виртуальных машин Azure | Microsoft Azure"
   description="Из этой статьи вы узнаете, как в Visual Studio настроить сбор диагностической информации для отладки облачных служб и виртуальных машин Azure."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="01/30/2016"
   ms.author="tarcher" />

# Настройка системы диагностики для облачных служб и виртуальных машин Azure

Чтобы устранять неполадки в облачных службах или виртуальных машинах Azure, в Visual Studio можно быстро настроить систему диагностики Azure. Система диагностики Azure собирает системные данные и журналы, поступающие от виртуальных машин и экземпляров виртуальных машин, на которых работает ваша облачная служба. Затем она передает эти данные в указанную вами учетную запись хранения. В статье [Включение ведения журнала диагностики для веб-приложений в службе приложений Azure](/app-service-web/web-sites-enable-diagnostic-log.md) собрана подробная информация о диагностических журналах в Azure.

В этом разделе мы продемонстрируем, как включить и настроить систему диагностики Azure в Visual Studio до или после развертывания, и как выполнить то же самое на виртуальных машинах Azure. В статье мы также расскажем, как правильно выбирать типы собираемых диагностических данных и просматривать собранную информацию.

Настроить систему диагностики Azure можно двумя способами.

- Изменить конфигурации системы диагностики через диалоговое окно **Конфигурация диагностики** в Visual Studio. Параметры сохраняются в файл с именем diagnostics.wadcfgx (diagnostics.wadcfg в Azure SDK до версии 2.4 включительно). Также вы можете напрямую изменить файл конфигурации. Если вы измените файл вручную, изменения конфигурации вступят в силу после следующего развертывания облачной службы в Azure или следующего запуска службы в эмуляторе.

- Изменить параметры диагностики для работающей облачной службы или виртуальной машины через **обозреватель облака** или **обозреватель сервера** в Visual Studio.

## Изменения в системе диагностики Azure версии 2.6

Для проектов, созданных в Visual Studio с помощью пакета Azure SDK 2.6, были внесены следующие изменения. (Эти изменения действуют и в более поздних версиях Azure SDK.)

- Локальный эмулятор теперь поддерживает систему диагностики. Это означает, что на этапе разработки и тестирования в Visual Studio вы можете собирать диагностические данные. Это позволит вам убедиться, что приложение создает правильные трассировки. При запуске проекта облачной службы в Visual Studio строка подключения `UseDevelopmentStorage=true` включает сбор диагностических данных с помощью эмулятора хранения Azure. Все диагностические данные собираются в учетную запись хранения (хранилище для разработки).

- Строка подключения к учетной записи хранения диагностической информации (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) также хранится в файле конфигурации (CSCFG) службы. В пакете Azure SDK версии 2.5 учетная запись хранения диагностических данных указывается в файле diagnostics.wadcfgx.

Существуют важные различия между работой строки подключения в старых версиях пакета Azure SDK (до версии 2.4 включительно) и более новых (начиная с версии 2.6).

- В Azure SDK 2.4 и более ранних версиях строка подключения использовалась как среда выполнения подключаемого модуля диагностики, который получал из нее параметры учетной записи хранения для передачи журналов диагностики.

- В Azure SDK 2.6 и более поздних версиях Visual Studio использует строку подключения диагностики для настройки учетной записи хранения в расширении диагностики во время публикации. Строка подключения позволяет вам указать разные учетные записи хранения для различных конфигураций службы. Visual Studio будет использовать их во время публикации. Но подключаемый модуль диагностики больше не доступен (в пакетах Azure SDK, начиная с версии 2.5), поэтому сам CSCFG-файл уже не может включить расширение диагностики. Его нужно включать отдельно с помощью таких средств, как Visual Studio или PowerShell.

- Выходные данные пакета Visual Studio содержат, среди прочего, общедоступный XML-файл конфигурации расширения диагностики для каждой роли. Это позволяет упростить процесс настройки расширения с помощью PowerShell. С помощью строки подключения диагностики Visual Studio получает из общедоступной конфигурации данные учетной записи хранения. Общедоступные файлы конфигураций создаются в папке «Расширения» с именами в формате bvt.bnPaaSDiagnostics.<RoleName>.PubConfig.xml. Все развертывания с использованием PowerShell могут использовать этот шаблон имени для сопоставления каждой конфигурации с ролью.

- Строку подключения из CSCFG-файла также использует портал Azure для доступа к диагностическим данным, отображаемым на вкладке **Мониторинг**. Строка подключения позволяет настроить в службе отображение на портале подробных данных мониторинга.

## Перенос проектов на Azure SDK версии 2.6 и выше

Когда проект переносится с Azure SDK 2.5 на Azure SDK 2.6 или более новую версию, данные учетной записи хранения диагностической информации, указанные в WADCFGX-файле, сохраняются. Чтобы воспользоваться возможностями гибкого использования разных учетных записей хранения для различных конфигураций, вам следует вручную добавить в проект строку подключения. Когда проект переносится с Azure SDK 2.4 или более ранней версии на Azure SDK 2.6, строки подключения диагностики сохраняются. Однако не забывайте, что в Azure SDK 2.6 строки подключения используются иначе (см. предыдущий раздел).

### Определение учетной записи хранения диагностических данных в Visual Studio

- Если в CSCFG-файле указана строка подключения диагностики, Visual Studio использует ее для настройки расширения диагностики во время публикации, а также при создании общедоступных XML-файлов конфигурации на этапе упаковки.

- Если в CSCFG-файле строка подключения диагностики не указана, Visual Studio использует для этих же целей учетную запись хранения, указанную в WADCFGX-файле.

- Строка подключения диагностики в CSCFG-файле имеет более высокий приоритет, чем учетная запись хранения в WADCFGX-файле. Если в CSCFG-файле присутствует строка подключения диагностики, Visual Studio использует именно ее, игнорируя учетную запись хранения в WADCFGX-файле.

### Флажок «Обновлять строки подключения хранилища разработки...»

Флажок **Обновлять строки подключения хранилища разработки для диагностики и кэширования, используя учетную запись хранения Microsoft Azure, при публикации в Microsoft Azure** позволяет быстро заменить строку подключения хранилища для разработки учетной записью хранения Azure, указанной во время публикации.

Предположим, вы установили этот флажок, а в строке подключения диагностики указан параметр `UseDevelopmentStorage=true`. Когда вы будете публиковать проект в Azure, Visual Studio автоматически заменит строки подключения диагностики учетной записью хранения, которую вы указали в мастере публикации. Но если в строке подключения диагностики указана реальная учетная запись хранения, будет использоваться именно она.

## Функциональные различия диагностики в Azure SDK между более ранними (до 2.4 включительно) и более поздними (с 2.5 включительно) версиями

Обновляя свой проект с Azure SDK 2.4 на Azure SDK 2.5 и выше, помните о следующих функциональных различиях.

- **API конфигурации больше не используются**. Программная конфигурация диагностики доступна в Azure SDK 2.4 и более ранних версиях, но начиная с версии Azure SDK 2.5 она не используется. Если конфигурация диагностики в вашем проекте определена в коде, после переноса проекта на более новую версию SDK все нужно настроить заново, иначе диагностика работать не будет. Azure SDK 2.4 используется файл конфигурации диагностики diagnostics.wadcfg, а Azure SDK 2.5 и более поздние версии — diagnostics.wadcfgx.

- **Диагностику для приложений облачной службы можно настроить только на уровне роли, а не на уровне экземпляра.**

- **Конфигурация диагностики обновляется при каждом развертывании приложения**. Если вы измените конфигурацию диагностики в обозревателе сервера и повторно развернете приложение, могут возникнуть проблемы с контролем четности.

- **В Azure SDK 2.5 и более поздних версиях аварийные дампы настраиваются в файле конфигурации диагностики, а не в коде**. Если вы настроили аварийные дампы в коде проекта, следует вручную перенести параметры из кода в файл конфигурации, поскольку при переходе на Azure SDK 2.6 аварийные дампы не переносятся.

## Включение диагностики в проекте облачной службы перед его развертыванием

В Visual Studio при запуске службы в эмуляторе перед ее развертыванием можно настроить сбор диагностических данных для ролей, выполняемых в Azure. Все изменения параметров диагностики в Visual Studio сохраняются в файл конфигурации diagnostics.wadcfgx. Эти параметры конфигурации определяют учетную запись хранения, в которую сохраняются диагностические данные после развертывания облачной службы.

### Включение диагностики в Visual Studio перед развертыванием

1. В контекстном меню интересующей вас роли выберите пункт **Свойства** и в окне **Свойства** перейдите на вкладку **Конфигурация**.

1. В разделе **Диагностика** установите флажок **Включить диагностику**.

    ![Флажок «Включить диагностику»](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)

1. Нажмите кнопку с многоточием (...) и укажите учетную запись хранения, в которой будут храниться данные диагностики.

    ![Выбор учетной записи хранения](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)

1. В диалоговом окне **Создание строки подключения хранилища** укажите способ подключения: эмулятор хранения Azure, подписка Azure или вручную введенные учетные данные.

    ![Диалоговое окно учетной записи хранения](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)

  - Если вы выберете эмулятор хранения Microsoft Azure, строка подключения будет иметь вид UseDevelopmentStorage=true.

  - Если вы выберете вариант с подпиской, то сможете выбрать нужную подписку Azure и имя учетной записи. Для управления подписками Azure нажмите кнопку «Управление учетными записями».

  - Если вы выберете вариант ручного ввода учетных данных, вам будет предложено ввести имя и ключ учетной записи Azure, которую вы хотите использовать.

1. Нажмите кнопку **Настроить**, чтобы открыть диалоговое окно **Конфигурация диагностики**. Здесь каждая вкладка (кроме вкладок **Общие** и **Каталоги журналов**) соответствует определенному источнику диагностических данных, которые можно собирать. По умолчанию открывается вкладка **Общие**, где вы можете выбрать следующие режимы сбора данных диагностики: **Только ошибки**, **Все сведения** и **Пользовательский план**. По умолчанию установлен режим **Только ошибки**, для которого требуется наименьший объем хранилища, поскольку в этом режиме предупреждения и сообщения трассировки не передаются. В режиме «Все сведения» передается больше всего информации; это самый затратный по объему хранения вариант.

    ![Включение и настройка диагностики Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)

1. В нашем примере мы выберем режим **Пользовательский план**, в котором вы можете настроить параметры сбора данных.

1. В поле **Квота диска в МБ** вы указываете, сколько хотите выделить места для диагностических данных в своей учетной записи хранения. Значение по умолчанию можно изменять.

1. На каждой вкладке, которая содержит нужные вам диагностические данные, установите флажок **Включить передачу <log type>**. Например, если вы хотите собирать журналы приложений, установите флажок **Включить передачу журналов приложения** на вкладке **Журналы приложений**. Установите также все остальные параметры, необходимые для каждого типа диагностических данных. Далее в этой статье есть раздел **Настройка источников диагностических данных**, где собрана информация о параметрах на каждой вкладке.

1. Настроив все нужные параметры сбора диагностических данных, нажмите кнопку **ОК**.

1. Теперь запустите проект облачной службы Azure в Visual Studio обычным образом. В процессе использования приложения вся информация, сбор который вы включили, будет сохраняться в указанной учетной запись хранения Azure.

## Включение диагностики на виртуальных машинах Azure

В Visual Studio вы можете включить сбор диагностических данных для виртуальных машин Azure.

### Включение диагностики для виртуальных машин Azure

1. В **обозревателе сервера** выберите узел Azure и подключитесь к подписке Azure (если это еще не сделано).

1. Разверните узел **Виртуальные машины**. Вы можете создать новую виртуальную машину или выбрать уже существующую.

1. В контекстном меню нужной виртуальной машины выберите пункт **Настроить**. Откроется диалоговое окно конфигурации виртуальной машины.

    ![Настройка виртуальной машины Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)

1. Добавьте расширение Microsoft Monitoring Agent Diagnostics, если оно еще не установлено. Это расширение позволяет собирать диагностические данные для виртуальной машины Azure. В списке установленных расширений выберите в раскрывающемся меню «Выбрать допустимое расширение» пункт Microsoft Monitoring Agent Diagnostics.

    ![Установка расширения виртуальной машины Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)

    >[AZURE.NOTE] Для виртуальных машин существуют и другие диагностические расширения. Дополнительные сведения см. в статье «Расширения и компоненты виртуальных машин Azure».

1. Нажмите кнопку **Добавить**, чтобы добавить расширение, и изучите информацию в диалоговом окне **Конфигурация диагностики**.

1. Нажмите кнопку **Настроить**, укажите учетную запись хранения и нажмите кнопку **ОК**.

    Каждая вкладка (кроме вкладок **Общие** и **Каталоги журналов**) соответствует определенному источнику диагностических данных, которые можно собирать.

    ![Включение и настройка диагностики Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)

    По умолчанию открывается вкладка **Общие**, где вы можете выбрать следующие режимы сбора данных диагностики: **Только ошибки**, **Все сведения** и **Пользовательский план**. По умолчанию установлен режим **Только ошибки**, для которого требуется наименьший объем хранилища, поскольку в этом режиме предупреждения и сообщения трассировки не передаются. В режиме **Все сведения** передается больше всего информации; это самый затратный по объему хранения вариант.

1. В нашем примере мы выберем режим **Пользовательский план**, в котором вы можете настроить параметры сбора данных.

1. В поле **Квота диска в МБ** вы указываете, сколько хотите выделить места для диагностических данных в своей учетной записи хранения. Значение по умолчанию можно изменять.

1. На каждой вкладке, которая содержит нужные вам диагностические данные, установите флажок **Включить передачу <log type>**.

    Например, если вы хотите собирать журналы приложений, установите флажок **Включить передачу журналов приложения** на вкладке **Журналы приложений**. Установите также все остальные параметры, необходимые для каждого типа диагностических данных. Далее в этой статье есть раздел **Настройка источников диагностических данных**, где собрана информация о параметрах на каждой вкладке.

1. Настроив все нужные параметры сбора диагностических данных, нажмите кнопку **ОК**.

1. Сохраните обновленный проект.

    В окне **Журнал действий Microsoft Azure** вы увидите сообщение о выполненном обновлении виртуальной машины.

## Настройка источников диагностических данных

Включив сбор диагностических данных, вы можете указать нужные вам источники данных и выбрать конкретную информацию, которую желаете собирать. Ниже приведен список вкладок в диалоговом окне **Конфигурация диагностики** и описание каждого параметра.

### Журналы приложений

На вкладке **Журналы приложений** содержатся диагностические данные, созданные веб-приложениями. Если вы хотите собирать журналы приложений, установите флажок **Включить передачу журналов приложения**. Чтобы увеличить или уменьшить количество минут, через которое журналы приложений будут передаваться в учетную запись хранения, измените значение параметра **Период передачи (мин)**. Также вы можете изменить объем сведений, сохраняемых в журнале, установив уровень ведения журнала. Например, выбрав уровень **Подробный**, вы будете получать подробные сведения, а выбрав уровень **Критические** — только критические ошибки. Если существует определенный провайдер диагностических данных, создающий журналы приложений, вы можете настроить сбор этих журналов, добавив идентификатор поставщика в поле **GUID поставщика**.

  ![Журналы приложений](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

  Дополнительную информацию о диагностических журналах см. в статье [Включение ведения журнала диагностики для веб-приложений в службе приложений Azure](web-sites-enable-diagnostic-log.md).

### Журналы событий Windows

Если вы хотите собирать журналы событий Windows, установите флажок **Включить перемещение журналов событий Windows**. Чтобы увеличить или уменьшить количество минут, через которое журналы событий будут передаваться в учетную запись хранения, измените значение параметра **Период передачи (мин)**. Установите флажки для тех типов событий, которые хотите отслеживать.

  ![Журналы событий](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Если вы используете пакет SDK Azure 2.6 (или более позднюю версию) и хотите указать пользовательский источник данных, введите его в текстовое поле **<Data source name>**, а затем нажмите рядом с ним кнопку **Добавить**. Источник данных добавляется в файл diagnostics.cfcfg.

Если вы используете Azure SDK 2.5 и хотите указать пользовательский источник данных, вы можете добавить его в файл diagnostics.wadcfgx в раздел `WindowsEventLog`, как показано в следующем примере.

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### Счетчики производительности

Сведения о счетчиках производительности помогут вам найти проблемы в системе и оптимизировать производительность системы и приложений. Дополнительные сведения см. в статье [Создание и использование счетчиков производительности в приложении Azure](https://msdn.microsoft.com/library/azure/hh411542.aspx). Если вы хотите сохранять данные счетчиков производительности, установите флажок **Включить перемещение счетчиков производительности**. Чтобы увеличить или уменьшить количество минут, через которое журналы счетчиков производительности будут передаваться в учетную запись хранения, измените значение параметра **Период передачи (мин)**. Установите флажки для тех счетчиков, которые хотите отслеживать.

  ![Счетчики производительности](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Чтобы отслеживать счетчик производительности, который отсутствует в списке, введите его имя, используя предложенный синтаксис, и нажмите кнопку **Добавить**. Список счетчиков производительности, которые можно отслеживать, зависит от операционной системы на виртуальной машине. Дополнительные сведения о синтаксисе см. в статье [Указание пути к счетчику](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### Журналы инфраструктуры

Журналы инфраструктуры содержат сведения об инфраструктуре диагностики Azure и модулях RemoteAccess и RemoteForwarder. Если вы хотите собирать эти данные, установите флажок **Включить передачу журналов инфраструктуры**. Чтобы увеличить или уменьшить количество минут, через которое журналы будут передаваться в учетную запись хранения, измените значение параметра «Период передачи (мин)».

  ![Журналы инфраструктуры диагностики](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

  Дополнительные сведения см. в статье [Сбор данных журналов с помощью средств диагностики Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### Каталоги журналов

В каталоги журналов собираются данные из выбранных вами папок, а также из каталогов, в которых хранятся журналы запросов к службам IIS и журналы неудачных запросов. Если вы хотите собирать эти данные, установите флажок **Включить перемещение каталогов журналов**. Вы можете увеличить или уменьшить количество минут, через которое журналы будут перемещаться в учетную запись хранения, изменив значение параметра **Период передачи (мин)**.

С помощью флажков выберите те журналы, которые хотите собирать, например **Журналы IIS** и **Неудачные запросы**. Имена контейнеров хранилища задаются автоматически, но при желании вы можете их изменить.

Кроме того, вы можете собирать журналы из любой папки. Для этого укажите путь к ней в разделе **Журнал из абсолютного каталога** и нажмите кнопку **Добавить каталог**. Журналы будут сохраняться в указанные контейнеры.

  ![Каталоги журналов](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### Журналы трассировки событий Windows

Если вы используете [трассировку событий для Windows] (https://msdn.microsoft.com/library/windows/desktop/bb968803(v=vs.85).aspx) (ETW) и хотите собирать журналы ETW, установите флажок **Включить перемещение журналов трассировки событий Windows**. Чтобы увеличить или уменьшить количество минут, через которое журналы будут передаваться в учетную запись хранения, измените значение параметра **Период передачи (мин)**.

События записываются из указанных вами источников событий и манифестов событий. Чтобы указать источник событий, введите его имя в разделе **Источники событий**, а затем нажмите кнопку **Добавить источник событий**. Аналогичным образом вы можете указать манифест событий в разделе **Манифесты событий** и нажать кнопку **Добавить манифест событий**.

  ![Журналы трассировки событий Windows](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

  ASP.NET поддерживает структуру ETW с использованием классов в пространстве имен [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110). Пространство имен Microsoft.WindowsAzure.Diagnostics, которое наследует и расширяет стандартные классы [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110), позволяет использовать [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110) как платформу ведения журналов в среде Azure. Дополнительные сведения см. в статьях [Управление протоколированием и трассировкой в Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) и [Включение диагностики в облачных службах и виртуальных машинах Azure](/cloud-services/cloud-services-dotnet-diagnostics.md).

### Аварийные дампы

Если вы хотите собирать сведения об аварийном завершении работы экземпляра роли, установите флажок **Включить перемещение аварийных дампов**. Поскольку ASP.NET обрабатывает большинство исключений, обычно это полезно только для рабочих ролей. Чтобы увеличить или уменьшить процент выделяемого под аварийные дампы места в хранилище, измените значение параметра **Квота каталога (%)**. Вы можете изменить контейнер хранения аварийных дампов, а также выбрать, какой дамп нужно сохранять: **полный** или **мини**.

В списке указаны все отслеживаемые процессы. Установите флажки для тех процессов, данные о которых вы хотите собирать. Чтобы добавить в список другой процесс, введите имя процесса и нажмите кнопку **Добавить процесс**.

  ![Аварийные дампы](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

  Дополнительные сведения см. в статьях [Управление протоколированием и трассировкой в Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) и [Система диагностики Microsoft Azure, часть 4: настраиваемые компоненты ведения журналов и изменения в системе диагностики Azure 1.3](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/).

## Просмотр диагностических данных

Собранные диагностические данные для облачной службы или виртуальной машины можно просматривать.

### Просмотр диагностических данных облачной службы

1. Разверните и запустите облачную службу обычным образом.

1. Диагностические данные можно просмотреть в виде отчета, который создает Visual Studio, или в виде таблиц в учетной записи хранения. Чтобы просмотреть данные в отчете, откройте **обозреватель облака** или **обозреватель сервера**, затем откройте контекстное меню интересующего вас узла и выберите пункт **Просмотреть диагностические данные**.

    ![Просмотр диагностических данных](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)

    Появится отчет по имеющимся данным.

    ![Отчет о системе диагностики Microsoft Azure в Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)

    Если в нем не содержатся самые свежие данные, вам следует дождаться завершения очередного периода передачи.

    Для немедленного обновления данных нажмите ссылку **Обновить**. Также вы можете выбрать новый интервал автоматической передачи данных в выпадающем списке **Автообновление**. Чтобы экспортировать данные об ошибках, нажмите кнопку **Экспорт в CSV**. Будет создан CSV-файл, который вы сможете открыть в виде электронной таблицы.

    В **обозревателе облака** или **обозревателе сервера** откройте учетную запись хранения, связанную с развертыванием.

1. Откройте диагностические таблицы в средстве просмотра таблиц и изучите собранные данные. Для просмотра пользовательских журналов и журналов служб IIS вы можете открыть контейнер больших двоичных объектов. В следующей таблице вы найдете имена таблиц или контейнеров больших двоичных объектов, которые содержат нужные данные. В дополнение к данным соответствующего журнала таблицы содержат поля EventTickCount, DeploymentId, Role и RoleInstance. Они помогут вам понять, какая виртуальная машина и роль создали эти данные, а также когда эти данные были созданы.

    |Диагностические данные|Описание|Расположение|
    |---|---|---|
    |Журналы приложений|Журналы, которые ваш код создает при вызове класса System.Diagnostics.Trace.|WADLogsTable|
    |Журналы событий|Эти данные собираются из журналов событий Windows на виртуальных машинах. В этих журналах хранится информация ОС Windows, но приложения и службы также могут записывать в них ошибки или данные.|WADWindowsEventLogsTable|
    |Счетчики производительности|Вы можете собирать данные с любого счетчика производительности, который доступен на вашей виртуальной машине. Операционная система предоставляет различные счетчики, которые позволяют изучать всевозможную статистику, например использование памяти и загруженность процессора.|WADPerformanceCountersTable|
    |Журналы инфраструктуры|Эти журналы создает сама инфраструктура диагностики.|WADDiagnosticInfrastructureLogsTable|
    |Журналы IIS|В эти журналы записываются веб-запросы. Если в облачную службу приходит очень много запросов, эти журналы могут быть довольно длинными, поэтому собирать и хранить их следует только при необходимости.|Журналы неудачных запросов можно найти в контейнере больших двоичных объектов в папке wad-iis-failedreqlogs, расположенной в каталоге конкретного развертывания, роли и экземпляра. Полные журналы вы найдете в папке wad-iis-logfiles. В таблицу WADDirectories вносятся записи для каждого файла.|
    |Аварийные дампы|Эта информация содержит двоичные образы процесса облачной службы (обычно рабочей роли).|Контейнер больших двоичных объектов wad-crush-dumps|
    |Файлы пользовательских журналов|Настроенные вами журналы.|Вы можете в коде задать расположение файлов пользовательских журналов в своей учетной записи хранения, например указать для них пользовательский контейнер больших двоичных объектов.|

1. Если данные любого типа усекаются, вы можете попробовать увеличить размер буфера для этого типа данных или уменьшить интервал между передачами данных из виртуальной машины в учетную запись хранения.

1. Время от времени удаляйте данные из учетной записи хранения, чтобы снизить общие затраты на хранение. Удалять данные не обязательно.

1. Когда вы выполняете полное развертывание, Azure обновляет файл diagnostics.cscfg (diagnostics.wadcfgx в Azure SDK 2.5) и облачная служба принимает все изменения, внесенные в конфигурацию диагностики. Если же вы обновляете существующее развертывание, Azure не обновляет CSCFG-файл. Но вы и в этом случае можете изменить конфигурацию диагностики, выполнив действия, описанные в следующем разделе. Дополнительные сведения о полном развертывании и обновлении существующего развертывания см. в статье [Мастер публикации приложений Azure](vs-azure-tools-publish-azure-application-wizard.md).

### Просмотр диагностических данных виртуальной машины

1. Выберите в контекстном меню виртуальной машины пункт **Просмотреть данные диагностики**.

    ![Просмотр диагностических данных на виртуальной машине Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)

    Откроется диалоговое окно **Сводка диагностики**.

    ![Сводка по диагностическим данным виртуальной машины](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)

    Если в нем не содержатся самые свежие данные, вам следует дождаться завершения очередного периода передачи.

    Для немедленного обновления данных нажмите ссылку **Обновить**. Также вы можете выбрать новый интервал автоматической передачи данных в выпадающем списке **Автообновление**. Чтобы экспортировать данные об ошибках, нажмите кнопку **Экспорт в CSV**. Будет создан CSV-файл, который вы сможете открыть в виде электронной таблицы.

## Настройка диагностики облачной службы после развертывания

Если вы изучаете проблему, возникшую в уже запущенной облачной службе, вам могут потребоваться новые данные, которые не были указаны перед первоначальным развертыванием этой роли. В этом случае вы можете начать сбор новых данных, изменив настройки в обозревателе сервера. Вы можете настроить диагностику для одного или всех экземпляров роли. В первом случае следует открыть диалоговое окно настройки диагностики из контекстного меню для экземпляра, а во втором — для роли. Если вы изменяете настройки в узле роли, все вносимые изменения применяются ко всем экземплярам. Если вы изменяете настройки в узле экземпляра, все вносимые изменения применяются только к этому экземпляру.

### Настройка диагностики для работающей облачной службы

1. В обозревателе сервера разверните узел **Облачные службы**, затем разверните в нем нужные узлы, чтобы найти анализируемую роль или экземпляр (или и то, и другое).

    ![Настройка системы диагностики](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)

1. В контекстном меню узла экземпляра или роли выберите пункт **Обновить параметры диагностики**, а затем выберите параметры диагностики, которые желаете собирать.

    Сведения о параметрах конфигурации см. в разделе **Настройка источников диагностических данных** в этой статье. Информацию о просмотре диагностических данных см. в разделе **Просмотр диагностических данных** в этой статье.

    Когда вы изменяете настройки сбора данных в **обозревателе сервера**, эти изменения остаются в силе до следующего полного развертывания облачной службы. Если вы используете стандартные параметры публикации, внесенные изменения сохранятся, так как по умолчанию при публикации выполняется обновление существующего развертывания, а не полное повторное развертывание. Чтобы очистить эти настройки во время развертывания, перейдите в мастере публикации на вкладку **Дополнительные параметры** и снимите флажок **Обновление развертывания**. При повторном развертывании со снятым флажком параметры снова принимают значения, заданные для роли в WADCFGX- или WADCFG-файле через редактор свойств. Если вы обновляете развертывание, Azure сохраняет старые настройки.

## Устранение неполадок облачной службы Azure

Если вы столкнетесь с проблемами в проектах облачных служб (например, роль «зависает» в состоянии «занято», постоянно перезапускается или выдает внутреннюю ошибку сервера), вы можете использовать несколько инструментов и методов для диагностики и устранения этих проблем. Конкретные примеры распространенных проблем и решений, а также общие сведения о концепциях и инструментах диагностики и устранения ошибок вы найдете в блоге [Данные диагностики для вычислительных сред Microsoft Azure PaaS](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## Вопросы и ответы

**Что такое размер буфера и каким он должен быть? **

Для каждого экземпляра виртуальной машины существуют квоты, которые ограничивают объем хранения диагностических данных в локальной файловой системе. Кроме того, вы определяете размер буфера для каждого доступного типа диагностических данных. Этот буфер выполняет роль индивидуальной квоты для соответствующего типа данных. В нижней части диалогового окна вы можете найти общую квоту и объем свободной памяти. Если вы укажете большие размеры буферов или большее количество типов данных, можно превысить общую квоту. Общую квоту вы можете изменить, отредактировав файл конфигурации diagnostics.wadcfg (или diagnostics.wadcfgx). Диагностические данные хранятся в той же файловой системе, что и данные приложения, поэтому не следует увеличивать общую квоту для диагностики, если приложение использует много места на диске.

**Что такое период передачи и каким он должен быть?**

Период передачи определяет количество времени между последовательными сборами данных. По окончании каждого периода передачи система диагностики перемещает данные из локальной файловой системы виртуальной машины в таблицы в вашей учетной записи хранения. Если до окончания периода передачи объем собираемых данных превысит квоту, старые данные удаляются. Если вы теряете данные из-за превышения размера буфера или общей квоты, советуем уменьшить период передачи.

**Какой часовой пояс используется для отметок времени?**

В отметках времени указан локальный часовой пояс того центра обработки данных, в котором размещена ваша облачная служба. В таблицах журналов есть такие три столбца отметки времени:

  - **PreciseTimeStamp** — отметка времени событий ETW. Это время регистрации события клиентом.

  - **TIMESTAMP** представляет собой PreciseTimeStamp с округлением до ближайшей границы периода передачи. Например, если вы используете период передачи 5 минут, а событие регистрируется в 00:17:12, в поле TIMETAMP будет установлено значение 00:15:00.

  - **Timestamp** — это отметка времени создания записи в таблице Azure.

**Как снизить затраты на сбор диагностической информации?**

По умолчанию выбраны значения параметров (**Уровень ведения журнала** имеет значение **Ошибка**, а **Период передачи** имеет значение **1 минута**), которые обеспечивают минимальные затраты. Затраты на вычисления увеличиваются, если вы собираете больше диагностических данных или уменьшаете период передачи. Не следует собирать больше данных, чем вам действительно нужно. Также не забудьте отключить сбор данных, когда это вам не нужно. Вы всегда можете включить эту функцию снова, даже во время выполнения, как показано в предыдущем разделе.

**Как собирать журналы неудачных запросов службы IIS?**

По умолчанию службы IIS не собирают журналы неудачных запросов. Чтобы настроить сбор этих данных в службах IIS, измените файл web.config для веб-роли.

**Я не получаю сведения о трассировке от методов RoleEntryPoint, например OnStart. Что не так?**

Методы RoleEntryPoint вызываются в контексте WAIISHost.exe, а не IIS. Таким образом, к ним не применяются настройки конфигурации из файла web.config, который обычно включает трассировку. Чтобы устранить эту проблему, добавьте в проект веб-роли CONFIG-файл с именем, которое соответствует имени выходной сборки, в которой размещен код RoleEntryPoint. В проекте веб-роли по умолчанию этот файл должен называться WAIISHost.exe.config. Добавьте в этот файл следующие строки.

```
<system.diagnostics>
  <trace>
      <listeners>
          <add name “AzureDiagnostics” type=”Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener”>
              <filter type=”” />
          </add>
      </listeners>
  </trace>
</system.diagnostics>
```

Теперь в окне **Свойства** задайте для свойства **Копировать в выходной каталог** значение **Всегда копировать**.

## Дальнейшие действия

Дополнительные сведения о ведении журналов диагностики в Azure см. в статьях [Включение диагностики в облачных службах и виртуальных машинах Azure](cloud-services-dotnet-diagnostics.md) и [Включение ведения журнала диагностики для веб-приложений в службе приложений Azure](web-sites-enable-diagnostic-log.md).

<!---HONumber=AcomDC_0204_2016-->