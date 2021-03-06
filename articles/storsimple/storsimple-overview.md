<properties 
   pageTitle="Что такое StorSimple? | Microsoft Azure" 
   description="Описание процедуры распределения по уровням, устройства StorSimple, виртуального устройства, служб и технологий управления хранилищем, а также определения основных терминов, используемых в Azure Storsimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="01/15/2016"
   ms.author="v-sharos@microsoft.com"/>

# Серия StorSimple 8000: решение гибридного облачного хранилища

## Обзор

Добро пожаловать в Microsoft Azure StorSimple — интегрированное решение хранилища, которое управляет задачами хранилища на локальных устройствах и в облачном хранилище Microsoft Azure. StorSimple — это эффективное, экономичное и легко управляемое решение сети хранения данных (SAN), которое устраняет многие проблемы и расходы, связанные с корпоративным хранилищем и защитой данных. Оно использует собственное устройство StorSimple серии 8000, интегрируется с облачными службами и предоставляет набор инструментов управления для непрерывного представления всего корпоративного хранилища, включая облачное. (Сведения о развертывании StorSimple, опубликованные на веб-сайте Microsoft Azure, применяются только к устройствам StorSimple серии 8000. Если используется устройство StorSimple серии 5000/7000, обратитесь к [справке по StorSimple](http://onlinehelp.storsimple.com/).)

StorSimple использует [многоуровневое хранилище](#automatic-storage-tiering) для управления данными, хранящимися на различных носителях данных. Текущий рабочий набор хранится локально на твердотельных накопителях (SSD), данные, которые используются реже, хранятся на жестких дисках, а архивные данные помещаются в облако. Кроме того, StorSimple использует дедупликацию и сжатие для уменьшения объема хранилища, используемого данными. Дополнительные сведения см. в статье [Дедупликация и сжатие](#deduplication-and-compression). Определения других ключевых терминов и понятий, используемых в документации к устройствам StorSimple серии 8000, см. в разделе [Терминология StorSimple](#storsimple-terminology) в конце этой статьи.

С выходом обновления 2 для StorSimple вы можете назначить соответствующие тома как *локально закрепленные*, чтобы основные данные оставались в локальной среде и не перемещались в облако. Это позволяет выполнять рабочие нагрузки, чувствительные к характерным для облака задержкам, такие как рабочие нагрузки SQL и виртуальных машин, на локально закрепленных томах, продолжая использовать облако для архивации. Дополнительные сведения о локально закрепленных томах см. в статье [Использование службы диспетчера StorSimple для управления томами](storsimple-manage-volumes-u2.md).

Обновление 2 также позволяет создавать виртуальные устройства StorSimple, использующие преимущества хранилища класса Premium Azure, заключающиеся в низких задержках и высокой производительности. Дополнительные сведения о виртуальных устройствах StorSimple класса Premium см. в статье [Развертывание виртуального устройства StorSimple в Azure и управление им](storsimple-virtual-device-u1.md). Дополнительные сведения о хранилище класса Premium Azure см. в статье [Хранилище Premium: высокопроизводительное хранилище для рабочих нагрузок виртуальной машины Azure](../storage/storage-premium-storage.md).

Помимо управления хранением функции защиты данных StorSimple позволяют создавать резервные копии по запросу и запланированные резервные копии и сохранять их локально или в облаке. Резервные копии создаются в виде добавочных моментальных снимков. Это означает, что их можно быстро создать и быстро восстановить. Облачные моментальные снимки могут быть критически важными в сценариях аварийного восстановления, так как заменяют дополнительные системы хранения данных (например, систему архивации на ленту) и позволяют восстанавливать данные в центр обработки данных или на альтернативные узлы, если это необходимо.

![значок видео](./media/storsimple-overview/video_icon.png) Посмотрите видео с краткой информацией о Microsoft Azure StorSimple.

> [AZURE.VIDEO storsimple-hybrid-cloud-storage-solution]

## Зачем использовать StorSimple?

В приведенной ниже таблице описаны некоторые ключевые преимущества, предоставляемые Microsoft Azure StorSimple.

| Функция | Преимущество |
|---------|---------|
|Прозрачная интеграция | Microsoft Azure StorSimple использует протокол iSCSI, чтобы незаметно связать оборудование хранилища данных. Это гарантирует, что данные, сохраненные в облаке, в центре обработки данных или на удаленных серверах, будут сохранены в одном местоположении.|
|Снижение затрат на хранилище|Microsoft Azure StorSimple выделяет достаточное локальное или облачное хранилище, соответствующее требованиям, и расширяет облачное хранилище только при необходимости. Оно дополнительно сокращает требования к хранилищу и расходы, устраняя избыточные версии тех же данных (дедупликацию) и используя сжатие.|
|Упрощенное управление хранилищем|Microsoft Azure StorSimple предоставляет инструменты системного администрирования, которые можно использовать для настройки и управления данными, сохраненными локально, на удаленном сервере и в облаке. Кроме того, вы можете управлять функциями архивации и восстановления из оснастки консоли управления (MMC). StorSimple предоставляет отдельный дополнительный интерфейс, который можно использовать для распространения служб управления и защиты данных StorSimple на контент, сохраненный на серверах SharePoint Server. |
|Усовершенствованное аварийное восстановление и соответствие|Microsoft Azure StorSimple не требует дополнительного времени восстановления. Вместо этого данные восстанавливаются по мере необходимости. Это означает, что нормальная работа может быть продолжена с минимальными простоями. Кроме того, вы можете настроить в политиках расписание архивации и сохранения данных.
|Мобильность данных|Данные, которые отправляются в облачные службы Microsoft Azure, доступны с других сайтов для восстановления и миграции. Кроме того, вы можете использовать StorSimple для настройки виртуальных устройств StorSimple на виртуальных машинах, выполняющихся в Microsoft Azure. Виртуальные машины могут затем использовать виртуальные устройства, чтобы получить доступ к сохраненным данным для тестирования или восстановления.|
|Поддержка других поставщиков облачных служб |Серия StorSimple 8000 с программным обеспечением с обновлением 1 или более поздней версии поддерживает Amazon S3 с облачными службами RRS, HP и OpenStack, а также Microsoft Azure. (Для управления устройствами по-прежнему требуется учетная запись хранения Microsoft Azure.) Дополнительные сведения см. в статье [Новые возможности в обновлении 1.2](storsimple-update1-release-notes.md#whats-new-in-update-12).|
|Непрерывность ведения бизнеса | В обновлении 1 или более поздней версии также доступно средство миграции, позволяющее пользователям устройств StorSimple серий 5000–7000 перенести свои данные на устройство StorSimple серии 8000.|
|Доступность на портале Azure для государственных учреждений. | Теперь обновление 1 или более поздней версии для StorSimple доступно на портале Azure для государственных учреждений. Дополнительные сведения см. в статье [Развертывание локального устройства StorSimple на портале для государственных учреждений](storsimple-deployment-walkthrough-gov.md).|
|Защита и доступность данных | Помимо локально избыточных хранилищ (LRS) и геоизбыточных хранилищ (GRS), устройство StorSimple серии 8000 с обновлением 1 или более поздней версии поддерживает хранилища, избыточные в пределах зоны (ZRS). Сведения о ZRS см. в [статье о способах обеспечения избыточности в службе хранилища Azure](https://azure.microsoft.com/documentation/articles/storage-redundancy/).|
|Поддержка критически важных приложений | С выходом обновления 2 для StorSimple вы можете назначить соответствующие тома как локально закрепленные. Эта возможность гарантирует, что данные, необходимые критически важным приложениям, не перемещаются в облако. Локально закрепленные тома не подвержены характерным для облака задержкам или проблемам со связью. Дополнительные сведения о локально закрепленных томах см. в статье [Использование службы диспетчера StorSimple для управления томами](storsimple-manage-volumes-u2.md).|
|Низкая задержка и высокая производительность | Обновление 2 для StorSimple позволяет создавать виртуальные устройства, использующие преимущества хранилища класса Premium Azure, заключающиеся в низких задержках и высокой производительности. Дополнительные сведения о виртуальных устройствах StorSimple класса Premium см. в статье [Развертывание виртуального устройства StorSimple в Azure и управление им](storsimple-virtual-device-u2.md).|

![значок видео](./media/storsimple-overview/video_icon.png) Посмотрите [этот видеобзор](https://www.youtube.com/watch?v=4MhJT5xrvQw&feature=youtu.be) функций и преимуществ устройств StorSimple серии 8000.

## Компоненты StorSimple

В решение Microsoft Azure StorSimple входят следующие компоненты:

- **Устройство Microsoft Azure StorSimple** — массив локального гибридного хранилища, который содержит твердотельные накопители (SSD) и жесткие диски (HDD), а также избыточные контроллеры и возможности автоматической отработки отказа. Контроллеры управляют уровнями хранилища, размещая недавно использовавшиеся (или активные) данные в локальном хранилище (на устройстве или локальных серверах), перемещая редко используемые данные в облако.
- **Виртуальное устройство StorSimple**, также известное как виртуальный модуль StorSimple, — это версия программного обеспечения устройства StorSimple, которая реплицирует архитектуру и большинство возможностей физического гибридного запоминающего устройства. Виртуальное устройство StorSimple выполняется на одном узле в виртуальной машине Azure. Виртуальные устройства класса Premium, использующие преимущества хранилища класса Premium Azure, доступны в обновлении 2 и более поздних версий.
- **Служба диспетчера StorSimple** — расширение классического портала Azure, которое позволяет управлять обычным или виртуальным устройством StorSimple в одном веб-интерфейсе. Вы можете использовать службу диспетчера StorSimple для создания служб и управления ими, просмотра устройств, политик архивации и каталога резервных копий и управления ими, просмотра оповещений, управления томами.
- **Windows PowerShell для StorSimple** — интерфейс командной строки, который можно использовать для управления устройством StorSimple. Windows PowerShell для StorSimple содержит функции, которые позволяют зарегистрировать устройство StorSimple, настроить сетевой интерфейс на устройстве, установить определенные типы обновлений, устранить неполадки с устройством в сеансе поддержки и изменить состояние устройства. Вы можете получить доступ к Windows PowerShell для StorSimple, подключившись к последовательной консоли, или с помощью удаленного взаимодействия Windows PowerShell.
- **Командлеты Azure PowerShell для StorSimple** — коллекция командлетов Windows PowerShell, которые позволяют автоматизировать задачи обновления и миграции из командной строки. Дополнительную информацию о командлетах Azure PowerShell для StorSimple см. в разделе [Справочная документация по командлетам](https://msdn.microsoft.com/library/dn920427.aspx).
- **Диспетчер моментальных снимков StorSimple** — оснастка консоли управления (MMC), которая использует группы томов и службу теневого копирования томов Windows для создания резервных копий, согласованных с приложениями. Кроме того, вы можете использовать диспетчер моментальных снимков StorSimple для создания расписания архивации и клонирования или восстановления томов. 
- **Адаптер StorSimple для SharePoint** — это средство, которое прозрачно распространяет хранилище и функцию защиты данных Microsoft Azure StorSimple на фермы SharePoint Server и при этом предоставляет возможности просмотра и управления StorSimple с портала администрирования SharePoint.

На приведенной ниже схеме представлено высокоуровневое представление архитектуры и компонентов Microsoft Azure StorSimple.

![Архитектура StorSimple](./media/storsimple-overview/overview-big-picture.png)

В следующих разделах каждый из этих компонентов описывается подробно и объясняется, каким образом решение упорядочивает данные и выделяет память, а также помогает управлять хранилищем и защищать данные. В последнем разделе представлены определения некоторых важных терминов и понятий, связанных с компонентами StorSimple и управлением ими.

## Устройство StorSimple

Устройство Microsoft Azure StorSimple — это локальный гибридный массив хранилища, который обеспечивает первичному хранилищу и iSCSI доступ к данным, хранящимся в нем. Оно управляет взаимодействием с облачным хранилищем и обеспечивает безопасность и конфиденциальность всех данных, хранящихся в решении Microsoft Azure StorSimple.

В устройство StorSimple входят твердотельные накопители (SSD) и жесткие диски (HDD), а также поддержка кластеризации и автоматической отработки отказа. Оно содержит общий процессор, общее хранилище и два зеркальных контроллера. Каждый контроллер предоставляет следующее:

- подключение к главному компьютеру;
- до шести сетевых портов для подключения к локальной сети (LAN);
- мониторинг оборудования;
- энергонезависимую память (NVRAM), которая сохраняет информацию даже в случае сбоев электропитания;
- кластерное обновление для управления обновлениями программного обеспечения на серверах в отказоустойчивом кластере, чтобы обновления не влияли на доступность службы;
- службу кластеров, которая работает как внутренний кластер, обеспечивая высокую доступность и уменьшая отрицательное влияние, которое может возникнуть при сбое или отключении HDD или SSD.

Постоянно работает только один контроллер. При сбое активного контроллера второй подключается автоматически.

Дополнительные сведения см. в статье [Компоненты оборудования StorSimple и их состояние](storsimple-monitor-hardware-status.md).

## Виртуальное устройство StorSimple

Вы можете использовать StorSimple для создания виртуального устройства, которое реплицирует архитектуру и возможности физического гибридного запоминающего устройства. Виртуальное устройство StorSimple (также известно как виртуальный модуль StorSimple) выполняется на одном узле в виртуальной машине Azure. (Виртуальное устройство можно создать только в виртуальной машине Azure. Его невозможно создать на устройстве StorSimple или на локальном сервере.)

Виртуальное устройство обладает следующими функциями:

- оно ведет себя как физическое устройство и предлагает интерфейс iSCSI для виртуальных машин в облаке. 
- Вы можете создать любое количество виртуальных устройств в облаке, а также включать и выключать их по мере необходимости. 
- Таким образом можно имитировать локальные среды в условиях аварийного восстановления, разработки и тестирования, а также извлекать из резервных копий отдельные компоненты. 

В рамках обновления 2 и более поздних версий виртуальное устройство StorSimple доступно в виде двух моделей: устройство 8010 (ранее оно называлось моделью 1100) и устройство 8020. Максимальная емкость устройства 8010 составляет 30 ТБ. Устройство 8020, которое использует хранилище класса Premium Azure, имеет максимальную емкость в 64 ТБ. (В хранилище класса Premium Azure данные хранятся на SSD, а в стандартном хранилище — на жестких дисках.) Обратите внимание, что для использования хранилища класса Premium требуется учетная запись хранения Premium Azure. Дополнительные сведения о хранилище класса Premium см. в статье [Хранилище Premium: высокопроизводительное хранилище для рабочих нагрузок виртуальной машины Azure](../storage/storage-premium-storage.md).

Дополнительные сведения о виртуальных устройствах StorSimple см. в статье [Развертывание виртуального устройства StorSimple в Azure и управление им](storsimple-virtual-device-u1.md).

## Служба диспетчера StorSimple

Microsoft Azure StorSimple предоставляет пользовательский веб-интерфейс (службу диспетчера StorSimple), которая позволяет централизованно управлять центром обработки данных и облачным хранилищем. Вы можете использовать службу диспетчера StorSimple для выполнения следующих задач.

- Настройка системных параметров для устройств StorSimple.
- Настройка параметров безопасности и управление ими для устройств StorSimple.
- Настройка облачных учетных данных и свойств.
- Настройка томов на сервере и управление ими.
- Настройка групп томов.
- Архивация и восстановление данных.
- Мониторинг производительности.
- Просмотр системных параметров и идентификация возможных проблем.

Вы можете использовать службу диспетчера StorSimple для выполнения всех задач администрирования за исключением тех, которые требуют простоя системы, например первоначальной настройки и установки обновлений.

Дополнительные сведения см. в статье [Использование службы диспетчера StorSimple для администрирования устройства StorSimple](storsimple-manager-service-administration.md).

## Windows PowerShell для StorSimple

Windows PowerShell для StorSimple предоставляет интерфейс командной строки, который можно использовать для создания службы Microsoft Azure StorSimple и управления ей, а также настройки и мониторинга устройств StorSimple. Это интерфейс командной строки на основе Windows PowerShell, в который входят выделенные командлеты для управления устройством StorSimple. Windows PowerShell для StorSimple содержит функции, которые позволяют выполнять следующие действия.

- Регистрация устройства.
- Настройка сетевого интерфейса на устройстве.
- Установка определенных типов обновлений.
- Устранение неполадок с устройством с помощью доступа к сеансу поддержки.
- Изменение состояния устройства.

Вы можете получить доступ к Windows PowerShell для StorSimple из последовательной консоли (на главном компьютере, подключенном непосредственно к устройству) или с помощью удаленного взаимодействия Windows PowerShell. Обратите внимание, что некоторые задачи Windows PowerShell для StorSimple, например первоначальную регистрацию устройства, можно выполнить только на последовательной консоли.

Дополнительные сведения см. в статье [Администрирование устройства с помощью Windows PowerShell для StorSimple](storsimple-windows-powershell-administration.md).

## Командлеты Azure PowerShell для StorSimple

Командлеты Azure PowerShell для StorSimple — это набор командлетов Windows PowerShell, которые позволяют автоматизировать задачи обслуживания и миграции из командной строки. Дополнительную информацию о командлетах Azure PowerShell для StorSimple см. в разделе [Справочная документация по командлетам](https://msdn.microsoft.com/library/dn920427.aspx).

## Диспетчер моментальных снимков StorSimple

Диспетчер моментальных снимков StorSimple — это оснастка консоли управления (MMC), которую можно использовать для создания согласованных резервных копий на момент времени для локальных и облачных данных. Оснастка выполняется на узле Windows Server. Вы можете использовать диспетчер моментальных снимков StorSimple для следующего.

- Настройка, архивация и удаление томов.
- Настройка групп томов для обеспечения согласованности архивированных данных с приложением.
- Управление политиками архивации, чтобы данные архивировались по заранее определенному расписанию и сохранялись в указанном расположении (локально или в облаке).
- Восстановление томов и индивидуальных файлов.

Резервные копии захватываются как моментальные снимки, которые записывают только изменения после того, как будет сделан последний снимок, и требуют гораздо меньше хранилища по сравнению с полными резервными копиями. Вы можете создать расписания архивации или немедленно выполнить архивацию. Кроме того, вы можете использовать диспетчер моментальных снимков StorSimple для установки политик хранения, которые управляют количеством сохраняемых моментальных снимков. Если впоследствии вам потребуется восстановить данные из резервной копии, в диспетчере моментальных снимков StorSimple можно будет выбрать снимок из каталога локальных или облачных снимков.

При возникновении сбоя или если нужно восстановить данные по другой причине, диспетчер моментальных снимков StorSimple восстанавливает их дополнительно. Восстановление данных не требует завершения работы всей системы при восстановлении файла, замене оборудования или переходе на другой сайт.

Дополнительные сведения см. в статье [Что такое диспетчер моментальных снимков StorSimple?](storsimple-what-is-snapshot-manager.md)

## Адаптер StorSimple для SharePoint

Microsoft Azure StorSimple содержит адаптер StorSimple для SharePoint — дополнительный компонент, который прозрачно расширяет хранилище StorSimple и функции защиты данных на фермы SharePoint Server. Адаптер работает с поставщиком удаленного хранилища BLOB-объектов (RBS) и функцией RBS SQL Server, позволяя перемещать BLOB-объекты на сервер, который архивируется системой Microsoft Azure StorSimple. Затем Microsoft Azure StorSimple сохраняет данные BLOB-объектов локально, в облаке, на основе использования.

Управление адаптером StorSimple для SharePoint производится на портале центрального администрирования SharePoint. Следовательно, управление SharePoint остается централизованным, и все хранилище переносится в ферму SharePoint.

Дополнительные сведения см. в разделе [Адаптер StorSimple для SharePoint](storsimple-adapter-for-sharepoint.md).

## Технологии управления хранилищем
 
Наряду с выделенным и виртуальным устройством и другим компонентами StorSimple в Microsoft Azure StorSimple используются следующие технологии программного обеспечения для предоставления быстрого доступа к данным и сокращения использования хранилища:

- [Автоматическое распределение по уровням хранилища](#automatic-storage-tiering) 
- [Тонкая подготовка](#thin-provisioning) 
- [Дедупликация и сжатие](#deduplication-and-compression) 

### Автоматическое распределение по уровням хранилища

Microsoft Azure StorSimple автоматически упорядочивает данные в логических уровнях на основе текущего использования, длительности и связи с другими данными. Наиболее активные данные хранятся локально, тогда как менее активные и неактивные данные автоматически переносятся в облако. На следующей схеме показан этот подход.
 
![Уровни хранилища StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Чтобы включить быстрый доступ, StorSimple сохраняет самые активные данные на SSD на устройстве StorSimple. На нем хранятся данные, которые используются время от времени на жестком диске устройства или на серверах в центре обработки данных. Оно перемещает неактивные данные, данные архивации и данные, которые сохраняются для архивации или соответствия в облако.

>[AZURE.NOTE] В обновлении 2 или более поздней версии можно указать том как локально закрепленный, при этом данные остаются на локальном устройстве и не перемещаются в облако.

StorSimple настраивает и переупорядочивает данные и назначения хранилища по мере изменения шаблонов использования. Например, некоторые сведения могут стать менее активными со временем. По мере уменьшения активности они мигрируют с SSD на жесткие диски, а затем в облако. Если те же данные снова становятся активными, выполняется обратная миграция на запоминающее устройство.

Процесс распределения хранилища по уровням происходит следующим образом:

1. Системный администратор настраивает учетную запись хранения облака Microsoft Azure.
2. Администратор использует последовательную консоль и службу диспетчера StorSimple (работающую на классическом портале Azure) для настройки устройства и файлового сервера, а также для создания томов и политик защиты данных. Для доступа к устройству StorSimple локальные компьютеры (например, файловый сервер) используют интерфейс малых вычислительных систем Интернета (iSCSI).
3. Изначально StorSimple хранит данные на быстром уровне SSD устройства.
4. По мере заполнения уровня SSD StorSimple выполняет дедупликацию и сжатие наиболее старых блоков данных и перемещает их на уровень HDD.
5. По мере заполнения уровня HDD StorSimple шифрует наиболее старые блоки данных и безопасно пересылает их на учетную запись хранения Microsoft Azure через HTTPS.
6. Microsoft Azure создает несколько реплик данных в центре обработки данных и в удаленном центре обработки данных, гарантируя, что данные можно будет восстановить при аварии. 
7. Если файловый сервер запрашивает данные, хранящиеся в облаке, StorSimple легко возвращает их и сохраняет копию на уровне SSD устройства StorSimple.

### Тонкая подготовка

Тонкая подготовка — это технология виртуализации, в которой доступное хранилище превышает физические ресурсы. Вместо того чтобы заранее резервировать достаточный объем хранилища, StorSimple использует тонкую подготовку для выделения минимального пространства, соответствующего текущим требованиям. Эластичный характер облачного хранилища упрощает этот подход, так как StorSimple может увеличить или уменьшить облачное хранилище для соответствия меняющимся требованиям.

>[AZURE.NOTE] Для локально закрепленных томов не осуществляется тонкая подготовка. Выделенное для исключительно локального тома хранилище подготавливается целиком при создании тома.

### Дедупликация и сжатие

Microsoft Azure StorSimple использует дедупликацию и сжатие данных для дальнейшего уменьшения требований к хранилищу.

Дедупликация уменьшает общий объем сохраненных данных, устраняя избыточность в наборе сохраненных данных. По мере изменения сведений StorSimple пропускает неизмененные данные, захватывая только изменения. Кроме того, StorSimple сокращает объем сохраненных данных, идентифицируя и удаляя ненужную информацию.

>[AZURE.NOTE] Данные на локально закрепленных томах не подвергаются дедупликации или сжатию. Однако резервные копии локально закрепленных томов дедуплицируются и сжимаются.

## Терминология StorSimple 

Перед развертыванием решения Microsoft Azure StorSimple рекомендуется ознакомиться со следующими терминами и определениями.

### Термины и определения

| Термин (аббревиатура или сокращение) | Описание |
| ------------------------------ | ---------------- |
| запись системы контроля доступа (ACR) | Запись, связанная с томом на устройстве Microsoft Azure StorSimple, которая определяет, какие узлы могут подключаться к нему. Это определение основано на содержащемся в ACR полном имени узлов iSCSI (IQN), которые подключаются к устройству StorSimple.|
| AES-256 | 256-разрядный алгоритм Advanced Encryption Standard (AES) шифрования данных при их перемещении в облако и из облака. |
| размер кластера (AUS) | Минимальный объем места на диске, может быть выделен для хранения файла в файловых системах Windows. Если размер файла не кратен размеру кластера, приходится использовать дополнительное пространство для хранения файла (до следующего размера, кратного размеру кластера), что приводит к потере пространства и фрагментации жесткого диска. <br>Рекомендуемый размер кластеров для томов Azure StorSimple составляет 64 КБ, поскольку это обеспечивает оптимальную работу алгоритмов дедупликации.|
| автоматическое распределение по уровням хранилища | Автоматическое перемещение менее активных данных с твердотельных накопителей на жесткие диски, а затем на уровень в облаке и последующее включение управления всем хранилищем из центрального пользовательского интерфейса.|
| каталог резервного копирования | Коллекция резервных копий, обычно связанных с типом использованного приложения. Эта коллекция отображается на странице каталога резервных копий в пользовательском интерфейсе службы диспетчера StorSimple.|
| файл каталога резервного копирования | Файл, содержащий список доступных моментальных снимков, которые в данный момент хранятся в резервной копии базы данных диспетчера моментальных снимков StorSimple. |
| политика архивации | Выбор томов, типа резервного копирования и расписания копирования, который позволяет создавать резервные копии по предопределенному расписанию.|
| большие двоичные объекты (BLOB-объекты) | Коллекция двоичных данных, хранящихся как единая сущность в системе управления базами данных. BLOB-объекты обычно представляют собой изображения, звук или другие объекты мультимедиа, при этом в BLOB-объектах иногда хранится двоичный исполняемый код.|
| Протокол проверки подлинности на основе вызова (CHAP). | Протокол, используемый для проверки подлинности однорангового соединения, на основании однорангового обмена паролем или секретом. CHAP может быть односторонним или двухсторонним. При использовании одностороннего CHAP целевое устройство проверяет подлинность инициатора. Для двустороннего CHAP требуется, чтобы целевое устройство проверило подлинность инициатора и инициатор проверил подлинность целевого устройства. | 
| клон | Дубликат тома. |
|Облако как уровень (CaaT) | Облачное хранилище, интегрированное в качестве уровня в архитектуру хранилища так, что все хранилище выглядит частью единой корпоративной сети хранения.|
| поставщик облачных услуг (CSP) | Поставщик облачных вычислений.|
| моментальный снимок облака | Копия данных тома на определенный момент времени, которая хранится в облаке. Облачный моментальный снимок аналогичен снимку, реплицированному в другой, внешней системе хранения. Облачные моментальные снимки особенно удобно использовать в сценариях аварийного восстановления.|
| ключ шифрования облачного хранилища | Пароль или ключ, используемый устройством StorSimple для доступа к зашифрованным данным, отправляемым с устройства в облако.|
| кластерное обновление | Управление обновлениями программного обеспечения в отказоустойчивом кластере, чтобы эти обновления не влияли на доступность службы или оказывали минимальное влияние на нее.|
| путь к данным | Набор функциональных единиц, выполняющих операции обработки взаимосвязанных данных.|
| деактивация | Необратимое действие, которое разрывает соединение между устройством StorSimple и соответствующей облачной службой. Облачные моментальные снимки устройства, которые остаются после выполнения этого процесса, можно клонировать или использовать для аварийного восстановления.|
| зеркальное отображение диска | Репликация логических томов диска на отдельные жесткие диски в режиме реального времени для обеспечения постоянной доступности.|
| динамическое зеркальное отображение диска | Репликация логических томов диска на динамических дисках.|
| динамические диски | Формат тома диска, который использует диспетчер логических дисков (LDM) для хранения и управления данными на нескольких физических дисках. Динамические диски можно увеличивать для получения дополнительного пространства.|
| Корпус с дополнительными жесткими дисками (EBOD) | Дополнительный корпус устройства Microsoft Azure StorSimple, содержащий дополнительные жесткие диски.|
| Грубая подготовка | Традиционная подготовка хранилища, при которой пространство в хранилище выделяется на основании ожидаемых потребностей (которые обычно превышают текущие потребности). См. также термин *тонкая подготовка*.|
| жесткий диск (HDD) | Диск, в которым для хранения данных используются вращающиеся диски.|
| гибридное облачное хранилище | Архитектура хранилища, в котором используются локальные и внешние ресурсы, включая облачное хранилище.|
| интерфейс малых вычислительных систем Интернета (iSCSI) | Стандарт сетей хранения данных на основе протокола IP для связывания хранилищ или оборудования хранения данных.|
| инициатор iSCSI | Программный компонент, позволяющий главному компьютеру под управлением Windows подключиться к внешней сети хранилищ на основе iSCSI.|
| полное имя iSCSI (IQN) | Уникальное имя, идентифицирующее цель или инициатора iSCSI.|
| цель iSCSI | Программный компонент, предоставляющий централизованные дисковые подсистемы iSCSI в сетях хранения данных.|
| динамическая архивация | Подход к хранению данных, при котором архивные данные доступны постоянно (например, они не хранятся удаленно на ленте). Динамическая архивация используется в Microsoft Azure StorSimple.|
|локально закрепленный том | том, который находится на устройстве и никогда не перемещается в облако. |
| локальный моментальный снимок | Копия данных тома, которые хранятся на устройстве Microsoft Azure StorSimple, на определенный момент времени.|
| Microsoft Azure StorSimple | Мощное решение, состоящее из устройства хранения центра обработки данных и программного обеспечения, которое позволяет организациям использовать облачное хранилище так, как если бы оно было хранилищем центра обработки данных, а также упростить защиту данных и управление данными, одновременно снизив затраты. Это решение объединяет основное хранилище, архив, резервное копирование и аварийное восстановление (DR) путем прозрачной интеграции с облаком. Объединяя хранилище SAN и управление данными облака на платформе корпоративного класса, устройства StorSimple позволяют добиться высокой скорости, простоты и надежности в решении всех задач, связанных с хранилищем.|
| Блоки питания и охлаждения (БПО) | Компоненты оборудования устройства StorSimple, состоящие из источников питания и вентиляторов, отсюда и название "блок питания и охлаждения". В основном корпусе устройства используется два БПО мощностью 764 Вт, а в корпусе EBOD два БПО мощностью 580 Вт.|
| основной корпус | Основной корпус устройства StorSimple, содержащий контроллеры прикладной платформы.|
| Целевое время восстановления (RTO) | Максимальное время, которое должно пройти до полного восстановления бизнес-процесса или системы после аварии.| 
|SCSI с последовательным подключением (SAS) | Тип жесткого диска (HDD).|
| ключ шифрования данных службы | Ключ, который выделяется каждому новому устройству StorSimple, регистрирующемуся в службе диспетчера StorSimple. Данные конфигурации, передаваемые между службой диспетчера StorSimple и устройством, шифруются с помощью открытого ключа и могут быть расшифрованы только на устройстве с помощью закрытого ключа. Ключ шифрования данных службы позволяет службе получить этот закрытый ключ для расшифровки.|
| регистрационный ключ службы | Ключ, который позволяет зарегистрировать устройство StorSimple в службе диспетчера StorSimple, после чего оно появляется на классическом портале Azure для выполнения дальнейших действий по управлению.|
| Интерфейс малых вычислительных систем (SCSI) | Набор стандартов для физически связанных компьютеров и передачи данных между ними.|
| твердотельный накопитель (SSD) | Диск, на котором отсутствуют движущиеся части, например, флеш-накопитель.|
| запись хранения Azure | Набор учетных данных для доступа, связанный с вашей учетной записью хранения для данного поставщика облачных служб.| 
| Адаптер StorSimple для SharePoint| Компонент Microsoft Azure StorSimple, который прозрачно расширяет хранилище StorSimple и функции защиты данных на фермы SharePoint Server.|
| Служба диспетчера StorSimple | Расширение классического портала Azure, которое позволяет управлять локальными и виртуальными устройствами Azure StorSimple.|
| Диспетчер моментальных снимков StorSimple | Оснастка консоли управления Microsoft (MMC) для управления операциями резервного копирования и восстановления данных Microsoft Azure StorSimple.|
| создать резервную копию | Функция, которая позволяет пользователю создать интерактивную резервную копию тома. Это альтернативный способ создания резервной копии тома вручную в отличие от автоматического резервного копирования посредством заданной политики.|
| тонкая подготовка | Метод оптимизации эффективности использования доступного пространства в системах хранения данных. При тонкой подготовке пространство распределяется среди нескольких пользователей на основании минимального пространства, необходимого каждому пользователю в любой момент времени. См. также термин *грубая подготовка*.|
| распределение по уровням | Объединение данных в логические группы на основе текущего использования, времени существования и связи с другими данными. StorSimple распределяет данные по уровням автоматически. |
| том | Логические области хранилища, представленные в виде дисков. Тома StorSimple соответствуют томам, смонтированным узлом, включая тома, обнаруженные при использовании iSCSI и устройства StorSimple.|
 | контейнер томов | Группа томов и параметры, которые применяются к ним. Все тома на устройстве StorSimple группируются в контейнеры томов. Параметры контейнера тома включают учетные записи хранилища, параметры шифрования данных, отправляемых в облако с соответствующими ключами шифрования, и используемую пропускную способность для операций с облаком.|
| группа томов | В диспетчере моментальных снимков StorSimple группа томов — это совокупность томов, настроенная для облегчения обработки резервного копирования.|
| служба теневого копирования томов (VSS)| Служба операционной системы Windows Server, которая повышает согласованность с приложениями путем взаимодействия с приложениями, поддерживающими VSS, для координации создания добавочных моментальных снимков. VSS обеспечивает временное отключение приложений во время создания моментальных снимков.|
| Windows PowerShell для StorSimple | Интерфейс командной строки на основе Windows PowerShell для взаимодействия с устройством StorSimple и управления им. Данный интерфейс не только поддерживает некоторые основные функции Windows PowerShell, но и включает дополнительные командлеты, предназначенные специально для управления устройством StorSimple.|


## Дальнейшие действия

Подробнее об [обеспечении безопасности StorSimple](storsimple-security.md).




 

 

<!---HONumber=AcomDC_0302_2016-->