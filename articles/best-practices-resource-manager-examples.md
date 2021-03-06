<properties
	pageTitle="Контекстные примеры рекомендаций по реализации шаблонов"
	description="Демонстрация примеров шаблонов диспетчера ресурсов Azure, иллюстрирующих рекомендации."
	services="azure-resource-manager"
	documentationCenter=""
	authors="mmercuri"
	manager="georgem"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="mmercuri"/>

# Контекстные примеры рекомендаций по реализации шаблонов

В этом разделе приведены 7 контекстных примеров реализации шаблонов диспетчера ресурсов Azure. Обзор принципов, демонстрируемых в этих примерах, см в статье. [Рекомендации по разработке шаблонов диспетчера ресурсов Azure](best-practices-resource-manager-design-templates.md).

Данная тема является частью другого технического документа. Чтобы ознакомиться с полным текстом этого документа, загрузите файл [Рекомендации по работе с шаблонами ARM мирового класса](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates — Considerations and Proven Practices.pdf).

## Перемещение шаблона уровня возможностей в шаблон уровня сквозных решений

Схема разработки шаблона уровня возможностей была предоставлена ранее. Один из вопросов, который можно задать, — различаются ли рекомендации при использовании данного шаблона уровня возможностей самого по себе или в рамках шаблона уровня сквозных решений.

Например, если бы существовал технологически ориентированный шаблон, развертывающий SQL Server как одну из возможностей, какими были бы рекомендации (при их наличии) для его использования независимым образом или в рамках более широкого шаблона уровня сквозных решений, которые может использовать этот сервер SQL Server для поддержки веб-приложения.

При рассмотрении этого сценария важно оценить ряд ресурсов, которые скорее всего будут задействованы. Для надежной реализации шаблон уровня возможностей не будет состоять всего лишь из одной учетной записи хранения и одной виртуальной машины с одним экземпляром SQL Server. Надежный шаблон уровня возможностей будет развертывать несколько виртуальных машин с развернутым SQL Server для обеспечения высокой доступности. Для некоторых возможностей, например служб Analysis Services, в топологии также скорее всего будет развернута служба Active Directory.

Две основные рекомендации в этом сценарии включают жизненный цикл использования SQL Server и управление доступом на основе ролей (RBAC), которое необходимо применить к нему. В частности, будет ли SQL Server обновлен и удален вместе с остальной частью решения, или его жизненный цикл будет отличаться от решения или других частей решения. Если жизненный цикл будет другим, будет рекомендовано поместить его в другую группу ресурсов.

Еще один нюанс заключается в том, как вы хотите применить RBAC в шаблоне решения уровня возможностей SQL Server. В зависимости от способа применения RBAC в топологии вы можете выбрать различные группы ресурсов для согласования с нужной спецификой. RBAC можно применить на уровне ресурсов, но при заданном количестве ресурсов для шаблона решения уровня возможностей SQL Server следует рассмотреть отличительную группу ресурсов, к которой применено RBAC.

Еще один нюанс заключается в оценке шаблона решения уровня возможностей SQL Server для определения, создает ли он в настоящее время определенные ресурсы сам или позволяет вам «принести собственные ресурсы». В модели «принести собственные ресурсы» (BYOR) в шаблоне решения уровня возможностей будет разрешено повторно использовать уже существующие ресурсы. Типичными примерами этих ресурсов являются учетная запись хранения, виртуальная сеть или группы доступности. Если подход BYOR отсутствует в вашем шаблоне уровня возможностей, его можно изменить с использованием подхода, определенного ранее в этом документе, для шаблонов необязательных ресурсов. В этом случае шаблон уровня сквозных решений будет основан на общем шаблоне ресурсов с такими общими ресурсами, а шаблон уровня возможностей будет расширен для поддержки этих ресурсов как необязательных. Это позволяет улучшить создаваемый шаблон решения уровня возможностей, так как теперь его можно использовать независимо или в составе композиции.

Оценивая, следует ли передавать учетную запись хранения из шаблона уровня сквозных решений, следует также повторно оценить RBAC. В частности, требуется ли обеспечить применение RBAC к данному конкретному ресурсу? Если это так и для ресурса ожидается такое применение при передаче, определенный уровень доверия применяется не только к блоку решения, но и к любому пользователю, который при необходимости желает обеспечить это доверие для шаблона уровня возможностей, когда он используется независимо. Если RBAC критически важно, следует решить, сделать ли шаблон необязательным в рамках шаблона решения уровня возможностей или потребовать его создания с обязательным RBAC изнутри шаблона решения уровня возможностей.

Если принято решение разместить их в разных группах ресурсов, можно также воспользоваться ссылками на ресурсы для определения отношений между ресурсами, даже если эти ресурсы охватывают несколько групп ресурсов.

## Создание шаблона уровня сквозных решений с несколькими шаблонами уровня возможностей

Этот пример во многом является супермножеством предыдущего. В этом сценарии у организации есть несколько шаблонов уровня возможностей для набора технологий обработки данных, таких как Kafka, Apache Hadoop, Apache Spark и Apache Storm, которые она намерена объединить в едином блоке решения. Полученная в результате композиция будет использовать эти шаблоны решений уровня возможностей, а также общее хранилище и виртуальную сеть с назначениями конкретных подсетей.

За пределами конкретных шаблонов уровня возможностей для решения будут необходимы дополнительные ресурсы, даже если это просто скрипты для соединения шаблонов уровня возможностей между собой и их настройки.

В данном случае определено наличие общей виртуальной сети и общей учетной записи хранилища. Чтобы справиться с этим, следует добавить указанные ресурсы в шаблон общих ресурсов в шаблоне уровня сквозных решений и обеспечить поддержку подхода «принести собственный ресурс» в шаблонах уровня возможностей. Если это не так, можно изменить шаблоны уровня возможностей для адаптации к условиям, как описано в предыдущем примере.

Для дополнительных ресурсов, которые будут добавлены, потребуется следовать супермножеству схемы, используемой при создании отдельного шаблона уровня возможностей. В данном случае будут добавлены шаблон общих ресурсов, шаблоны необязательных ресурсов, шаблоны узлов и конфигурация желаемого состояния (скрипты, Chef, Puppet, Powershell DSC) для новых ресурсов. Там, где существуют зависимости, нужно будет по возможности оптимизировать использование неявных ссылок против dependsOn для устранения потенциала изолированных зависимостей, которые могут повлиять на параллелизм (и скорость) развертывания. Также следует учитывать жизненный цикл этих ресурсов, рекомендации по RBAC и зависимости, чтобы определить, следует ли размещать их в разных группах ресурсов.

При добавлении общих ресурсов, например общей учетной записи хранения, следует также оценить, требуется ли для нее блокировка ресурса, так как это поможет избежать случайных операций удаления.

При добавлении новых ресурсов также следует проверить, можно ли ресурсы, добавляемые в шаблон уровня сквозных решений, изолировать как шаблоны уровня возможностей сами по себе. Если да, настоятельно рекомендуется это сделать для содействия дальнейшей декомпозиции, которая может предоставить преимущества для повторного использования и тестирования.

При интеграции в блоках вашего решения далее рекомендуется, как изложено в предыдущем примере, определить, отличается ли жизненный цикл отдельных шаблонов решений уровня возможностей от жизненного цикла более широкого решения и обуславливают ли какие-либо требования RBAC необходимость их разделения на отдельные группы ресурсов.

Наконец, необходимо решить, требуется ли возможность определять и запрашивать ссылки между ресурсами. Если сделать это, задействование ссылок на ресурсы позволит выполнять это по всему шаблону сквозных решений, даже при охвате нескольких групп ресурсов.

## Создание шаблона уровня сквозных решений со схемой частичного включения/выключения

Этот сценарий представляет собой вариант предыдущего. В этом случае клиент извлекает данные из локальной системы через фиксированные промежутки времени в течение дня. Имеется конвейер данных для обработки таких входящих данных и хранилище реляционных данных, где данные всегда доступны для запросов. Поскольку облачная система реализована в виде оплаты по мере использования, клиенту желательно поддерживать конвейер данных работающим только во время тех интервалов, когда данные предоставляются для обработки.

В составе конвейера данных есть SQL Server, который получает обработанные данные и делает их доступными для запросов. Клиент указал, что, хотя было бы желательно включать и выключать элементы конвейера, обеспечивающие прием и обработку, по фиксированному расписанию, SQL Server должен быть доступным постоянно.

В этом сценарии выделяются явные различия в жизненном цикле и потенциально ряд дополнительных вопросов, которые клиент еще не рассматривал, но их следует оценить.

Как было сказано, развернутый SQL Server будет поддерживаться в рабочем состоянии, в то время как другие ресурсы будут создаваться и удаляться. Изначально они будут развертываться вместе, но затем другие элементы шаблона будут уничтожены и созданы в другом жизненном цикле. Их можно изолировать по разным группам ресурсов или оставить в той же группе ресурсов с применением блокировки к ресурсам SQL Server. Поскольку SQL Server скорее всего будет представлен большим набором ресурсов, как описано в предыдущих примерах, выделить его в собственную группу ресурсов будет уместно.

Другим аспектом является то, что если клиент выразил желание включать и выключать остальную часть конвейера данных по расписанию, он может не учитывать несогласованное поведение систем отчетности. Доставка данных от третьих сторон по расписанию не всегда выполняется точно, поскольку подключение может быть недоступно в течение некоторого периода времени, часы на локальных или облачных серверах могут смещаться, изменения времени могут происходить или не происходить ожидаемым образом и т. д. Следует оценить, нужно ли использовать механизм приема также по схеме включения и выключения и, если так, превышает ли его жизненный цикл таковой у компонентов обработки.

При использовании управляемой службы, такой как Azure Data Factory или Event Hub, это не создает большой проблемы, поскольку их модели работы и связанный подход к оплате позволяют обеспечить готовность к приему данных и помещению их в хранилище. Если используется другая технология, например Kafka, которая развернута на виртуальной машине, может потребоваться рассмотреть жизненный цикл, по которому вы делаете доступными ее и связанные учетные записи хранения, необходимые для приема. В результате этого ресурсы приема и обработки могут быть размещены в разных группах ресурсов в зависимости от их жизненного цикла.

## Поддержка различных сред в пределах подписки

Для эффективной работы многим организациям требуется масштабирование, изоляция выставления счетов, изоляция отчетности и географическая изоляция. Чтобы удовлетворить эти потребности, при проектировании служб для Azure они исторически использовали секционирование подписок.

Диспетчер ресурсов снижает ограничения на количество ресурсов заданного типа, которые можно развернуть в подписке, а также включает группы ресурсов, RBAC и аудит. Эта технология позволяет организациям использовать группы ресурсов для секционирования, чтобы удовлетворить свои требования и уменьшить объем необходимого секционирования подписки.

В этом разделе рассматриваются требования для таких типов сред и приводятся рекомендации по доставке сред, которые отвечают требованиям организаций, с использованием ARM.

### Рекомендации по изоляции

В этом разделе подробно рассматриваются распространенные предпосылки заказчиков для изоляции среды, выставляемых счетов и географии.

#### Изоляция среды

Владельцы служб хотят изолировать свои различные среды. Изоляция каждой среды позволяет рабочим группам более точно контролировать доступ к средам. Среды разработки могут быть более открыты для доступа к ним. Но по мере перемещения среды ближе к рабочей версии уменьшается количество пользователей, будь то люди или системные учетные записи, используемые для автоматизации. Это обеспечивает соответствие требованиям и минимизирует общий риск.

#### Изоляция выставляемых счетов — разработка и запуск службы

Для точного отражения стоимости проданных товаров (COGS) и операционных расходов (OpEx) владельцам бизнеса требуется отделять затраты на исследование и построение службы от затрат на ее выполнение.

В упомянутом ранее супермножестве изоляции среды целью будет консолидация разработки и тестирования для отдельного и/или агрегированного выставления счетов в первом случае и независимость производственного процесса в последнем.

#### Изоляция выставляемых счетов — добавление прозрачности и подотчетности к затратам на потребление служб

Изоляция выставляемых счетов также используется для повышения прозрачности затрат, связанных с потреблением платформы определенными группами, и внедрения соответствующих уровней учета.

Хотя облачная система обладает гибкостью и позволяет оплачивать услуги по мере использования, некоторые разработчики использовали необлачную модель, в которой оборудование закупалось и поступало во владение. В необлачной модели есть физические ограничения на количество «машин», которые можно включить, и существует меньше стимулов для уменьшения масштаба или отключения ресурсов, когда они не используются. Закупка такого выделенного оборудования во многих случаях осуществлялась не разработчиками, которые его применяли.

Изолируя подписки и назначая их подотчетность определенным группам пользователей, владельцы получат преимущества такого типа секционирования подписок, который поможет добиться желаемого поведения.

#### Изоляция на основе географии — развертывание систем, ориентированных на определенное географическое местоположение и подчиненных соответствующей юрисдикции.

Иногда услуги, предназначенные для конкретного региона, необходимо развертывать с учетом соблюдения стандартов и законодательства соответствующего региона.

Хотя служба может быть глобальной, развертываемые в ее пределах системы или предоставляемые услуги в определенных географических регионах могут регулироваться требованиями к операционному персоналу. В частности, работа таких служб может предусматривать трудоустройство только граждан определенной страны или нескольких стран и/или прохождение проверки биографических данных сотрудников.

Географическая изоляция также предоставляет преимущества использования новых служб и возможностей платформы. В некоторых географических регионах, например в Китае, может быть доступна только часть служб платформы и/или развертывание этих служб может быть отложено.

Географическая изоляция предоставляет группам возможность развивать свои службы, пользуясь преимуществами новых или улучшенных служб и возможностей платформы там, где они доступны.

### Соблюдение правовых и этических норм

Службы могут доставляться в разные географические регионы и в компании из различных отраслей Приложения компаний часто содержат конфиденциальные данные или процессы. Также существуют соответствующие правовые нормы, призванные одновременно защищать и проверять эти данные.

#### Разделение ролей и обязанностей

Разделение ролей и обязанностей — это ключевое требование к внутренним службам для соответствия внутренней политике. Многим коммерческим службам также требуется это разделение для соответствия требованиям государственного и отраслевого регулирования. Иногда необходимо ограничить авторизованным ролям доступ к службам и их основным ресурсам. Многие службы создали систему формирования шаблонов для реализации двух возможностей — управления доступом на основе ролей и аудита.

#### Варианты использования управления доступом на основе ролей (RBAC)

Если ситуация требует соблюдения правовых и этических норм, необходимо ограничить доступ к определенным ресурсам.

Например, если речь идет о различных конфиденциальных данных, которые требуют соблюдения правовых и этических норм (например, информация о здоровье, финансовые данные, налоговые записи и т. п.), следует ограничить круг лиц, которые могут иметь доступ к таким данным, просматривать их или обрабатывать. Список этих лиц должен включать только сотрудников, которым такой доступ необходим для деятельности в головной организации.

RBAC предоставляет отдельному лицу, системе или группе доступ к определенным ресурсам при установленных условиях.

#### Аудит

Помимо ограничения доступа посредством RBAC, организациям требуется также аудит доступа к ресурсам и взаимодействия с ними.

### Реализация с помощью диспетчера ресурсов Azure

Ранее организации использовали для достижения этих целей секционирование подписок. Хотя это решение можно использовать, оно не решает все проблемы. Поскольку создание подписки фактически является коммерческой деятельностью, API управления службами не раскрывал механизм их автоматического создания или удаления, и подписки необходимо было создавать вручную. Получаемое в результате количество подписок могло существенно увеличиваться. Для очень больших служб, таких как собственные коммерческие службы Майкрософт количество подписок могло дойти до тысячи. Такая ситуация часто приводила к разработке собственных схем создания и управления подписками для организации.

Благодаря диспетчеру ресурсов развертывание нескольких сред в пределах одной подписки существенно упростилось. Он ослабляет ранее фиксированные ограничения на ресурсы, которые существовали в предыдущей модели, что значительно снижает необходимость в секционировании из-за ограничений на ресурсы.

Среды можно размещать в группах ресурсов, к которым применены определенные правила RBAC, что дает возможность реализовать изоляцию среды. В ситуациях, когда требуется географическая изоляция, этой изоляции можно достичь с помощью групп ресурсов. Поскольку группы ресурсов могут охватывать географические регионы, можно достичь определенной изоляции для одного или нескольких регионов.

К ресурсам и группам ресурсов можно применять теги, которые могут использоваться для накопления выставляемых счетов и сводных представлений, обеспечивая изоляцию выставления счетов. С помощью тегов можно определить тип среды (исследование, образование, разработка, тестирование, производство), учетную организацию или лицо («Отдел кадров», «Финансы», «Иван Кузнецов», «Яна Иванова»).

Требование аудита реализуется в рамках базового набора возможностей диспетчера ресурсов Azure «из коробки» и может быть доступно для просмотра в центральном расположении.

Учетные записи конечных пользователей будут зарегистрированы в Azure Active Directory, где будет осуществляться проверка их подлинности и управление доступом на основе ролей к среде и ресурсам.

#### Оптимизация по плотности

Хотя ограничения на ресурсы в диспетчере ресурсов Azure ослаблены, они по-прежнему существуют. Помимо создания самих сред, следует также рассмотреть достижение плотности сред в пределах подписок. Доставка среды предусматривает доставку определенной мощности лицу или организации, и следует оценить, какие «размеры футболок» следует доставлять. В частности, определите варианты для заказчиков масштаба S, M, L и XL в плане требуемых ресурсов.

Можно выбрать использование различных подписок для разных масштабов услуг с целью достижения большей плотности. Например, может быть возможность размещения в заданной подписке 1000 сред небольшого масштаба, 500 среднего, 100 крупного и 10 особо крупного масштаба. Поскольку наличие нескольких подписок не приводит к дополнительным затратам, может быть удобно изолировать разные масштабы услуг по разным подпискам для обеспечения максимальной плотности. При этом можно поддерживать число подписок относительно небольшим и простым в управлении.

Один из ключевых вопросов, которые следует рассмотреть, — нужно ли разрешить заказчику увеличивать или изменять «размер футболки» и, если да, как это следует организовать.

Один подход заключается в том, чтобы заказчик мог приобрести дополнительную емкость в пределах имеющейся у него группы ресурсов. Это можно легко реализовать технически, но это окажет влияние на плотность. Вместо строго определенных размеров услуг для всех клиентов такая возможность вводит некоторый уровень вариативности, добавляющий накладных расходов при оптимизации по плотности. Если каждая среда малого масштаба имеет размер X, можно легко предварительно рассчитать, сколько таких сред поместится в подписке для оптимальной плотность. Если же разрешить клиентам настраивать размер среды, результатом будет непредсказуемое число вариантов и количеств сред — X, X + 1, X + 2 и т. д. На таком уровне вариативности будет достигаться меньшая плотность, поскольку необходимо будет отложить часть емкости в пределах подписки для адаптации к различиям.

По возможности это менее чем идеально в качестве общего подхода, поскольку обеспечивает меньшую плотность и требует больше издержек на управление. Для сред большего размера такой вариант может быть более приемлемым. Поскольку в одной подписке крупных и особо крупных сред будет размещено меньше, вы можете выделить меньшее их число для обеспечения роста.

Другой подход предусматривает удаление среды заказчика с текущим размером и создание новой среды другого размера. Хотя это не уместно в некоторых сценариях, это подходит для сред, используемых временно, например сред разработки и тестирования.

Следующий простейший подход — предоставить заказчику возможность приобрести среду большего размера, а затем организовать миграцию в эту среду собственными силами. Например, заказчик, у которого развернута система SQL Server в среде небольшого масштаба, может приобрести среду среднего масштаба и будет сам отвечать за перенос данных и пользовательское состояние.

Альтернативный подход — предоставить управляемую услугу, где будет поддерживаться переход от одного размера к другому. Это явно сложнее, но в зависимости от рабочих нагрузок и заказчиков это может быть именно тем, к чему будет стремиться ваша организация.

## Доставка сред с дополнительными ограничениями политики заказчиков

В некоторых организациях имеются дополнительные требования и политики для развертываемых сред. В частности, их политики могут ограничивать порты, к которым предоставляется внешний доступ, а также требовать мониторинга входящего и исходящего трафика среды. По соображениям возможности поддержки и стоимости также могут быть установлены ограничения на ресурсы, которые может создавать, обновлять или удалять конечный пользователь. Для организации, предоставляющей доступ к среде, зачастую также требуется обеспечить доступ к подписке службе поддержки.

Как надмножество предыдущего сценария это потребует добавления некоторых ресурсов, у которых будут дополнительные ограничения на то, кто может или не может создавать ресурсы заданного типа.

Возможность пользователя создавать, обновлять или удалять те или иные ресурсы может быть ограничена средствами управления доступом на основе ролей. Примером можно назвать организацию, которой требуется определенная сеть VNET и потенциально подсети, которые конечный клиент не мог бы обновлять или удалять.

Можно установить блокировки ресурсов, которые будут доступны только для чтения или не могут быть удалены. С помощью RBAC можно разрешить пользователям или участникам службы выполнять определенные действия в отношении ресурса или группы ресурсов.

Если организации требуется, чтобы определенный трафик, например трафик между уровнями приложения, сначала проходил через посредника, такого как виртуальное сетевое устройство, следует использовать определенные пользователем маршруты.

Виртуальное устройство — это просто виртуальная машина, которая запускает приложение, используемое для обработки сетевого трафика определенным образом, например, как брандмауэр или устройство NAT. Ряд сторонних производителей предоставляют виртуальные сетевые устройства в Azure, а организация может также установить собственные.

Подход «Принести собственное устройство» позволяет организации повторно использовать существующий код, который может использоваться в ее локальных средах. Эта виртуальная машина виртуального устройства должна иметь возможность получать входящий трафик, предназначенный не ей. Чтобы разрешить виртуальной машине получать трафик, направленный в другие назначения, в ней необходимо включить IP-пересылку.

Как и в предыдущих примерах жизненный цикл ресурсов и ограничения RBAC следует рассмотреть в рамках вашей стратегии групп ресурсов.

## Защита ресурсов от внутренних недобросовестных сторон

Одна из проблем для организации может состоять в защите ресурсов и предоставляющих их шаблонов от недобросовестных сторон.

Примером этого может быть банк, желающий гарантировать, чтобы злоумышленный разработчик программного обеспечения или член ИТ-персонала не вносил модификаций и не извлекал ключевую информацию, которая приведет к попаданию данных к недобросовестной стороне в преступных целях.

Типичный корпоративный сценарий предусматривает небольшую группу доверенных операторов, имеющих доступ к критически важным секретам в пределах развернутых рабочих нагрузок, и более широкую группу персонала по разработке и эксплуатации, которые могут создавать или обновлять развертываемые виртуальные машины.

Для координации и хранения секретных данных и сертификатов виртуальных машин можно использовать хранилище ключей Azure с ARM.

Рекомендуется поддерживать отдельные шаблоны ARM для создания хранилищ (которые будут содержать материал ключей) и развертывания виртуальных машин (с URI-ссылками на ключи, содержащиеся в хранилищах).

Секреты, содержащиеся в хранилище ключей, находятся под полным контролем RBAC доверенного оператора. Если доверенный оператор покинет компанию или перейдет внутри нее в новую группу, у него больше не будет доступа к ключам, созданным им в хранилище.

Шаблоны ARM для развертывания содержат только URI-ссылки на секретные данные, что означает, что фактические секретные данные не содержатся в коде, конфигурации или репозиториях исходного кода. Это уменьшает возможности для фишинга и ограничивает способность недобросовестных сторон вносить изменения.

Как уже говорилось ранее в этом документе, не существует глобальных хранилищ ключей. Поскольку хранилища ключей всегда являются региональными, секретные данные всегда обладают локальностью (и независимостью) вместе с виртуальными машинами.

Пример реализации такого подхода был представлен в разделе «Секретные данные и сертификаты» ранее в этом документе.

## Обеспечение модели «Принести собственную подписку»

Корпоративные ИТ-службы, системные интеграторы и поставщики облачных служб могут задействовать модель «принести собственную подписку» в отношении заказчиков. В частности, организация предоставляет услугу конечному клиенту и определенным образом использует подписку на Azure этого клиента.

Существует несколько вариантов этого подхода, в каждом из которых требования слегка различаются, как описано ниже.

### Включение доступа третьих сторон для мониторинга ресурсов в учетной записи

Организации с приложением мониторинга может потребоваться доступ только для чтения к подписке клиента для получения данных, используемых в этом приложении. Это потребует доступа только для чтения для текущего периода времени. Доступ должен находиться под контролем клиента, предоставляя ему возможность прекратить доступ, если связь с поставщиком службы мониторинга будет поставлена под угрозу.

#### Реализация с помощью диспетчера ресурсов Azure

Подробные сведения о реализации этого сценария содержатся в «Руководстве разработчиков по проверке подлинности с помощью API диспетчера ресурсов Azure», которое находится [здесь](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/). Этот документ содержит пошаговые инструкции по реализации, а также пример кода.

### Предоставление доступа третьей стороне для однократного развертывания программного обеспечения

В другом примере организация может развернуть и настроить версию программного обеспечения в учетной записи заказчика, что потребует доступа для записи на период времени развертывания.

#### Реализация с помощью диспетчера ресурсов Azure

Подход во многом аналогичен предыдущему примеру.

В зависимости от конкретных потребностей установленной системы, назначаемая участнику службы конкретная роль должна разрешить только минимальный уровень доступа, необходимый для выполнения установки, а затем этот доступ быть немедленно отозван после завершения установки.

### Предоставление третьей стороне доступа к использованию клиентских подписок для хранения данных

В другом примере организации может потребоваться выполнять программное обеспечение в собственной среде, но использовать учетную запись клиента для хранения. Это предоставляет клиенту постоянный контроль над своими данными и позволяет ему использовать другие технологии на платформе, например машинное обучение Azure или HDInsight, по собственному усмотрению, не увеличивая стоимость и расходы на выставление счетов для корпоративной ИТ-службы, системного интегратора или CSV, предоставляющих такую возможность. Для этого организации требуется постоянный доступ к учетной записи хранения, при этом клиент будет обладать контролем и доступом к данным аудита доступа к этой информации.

#### Реализация с помощью диспетчера ресурсов Azure

Это реализовано с помощью той же схемы, как и в других примерах. Участнику службы предоставляется доступ к ресурсу хранилища. Поскольку в этом сценарии роль требует доступа к учетной записи хранения для чтения и записи, для достижения такого уровня доступа участнику службы будет назначена роль «Участник» (Contributor).

Так как в этом сценарии участвует как основная, так и третья сторона с общей учетной записью хранения, возникает также необходимость предотвратить случайное удаление этой учетной записи. Для этого аспекта сценария к учетной записи хранения следует применить блокировку ресурса.

### Включение управление службой посредством третьей стороны

В другом примере организации требуется развертывать, отслеживать и управлять программным обеспечением в подписке клиентов. Могут существовать ограничения на клиента в плане изменений, которые он может (или, более явно, не может) вносить в среду, где развернуто программное обеспечение.

#### Реализация с помощью диспетчера ресурсов Azure

Это соответствует надмножеству шаблона, определенного в начале этого раздела. В частности, используемому третьей стороной участнику службы предоставляется полный доступ к ресурсам в пределах группы ресурсов.

Кроме того, поскольку существуют ограничения на клиента, пользователям или группам со стороны клиента будут предоставлены соответствующие права на использование среды. Это можно сделать с помощью шаблонов, как определено ранее в этом разделе.

Наконец, может быть необходимость предотвратить случайное удаление некоторых ресурсов. В таком случае следует также рассмотреть блокировки ресурсов, которые требуют такой защиты.

## Дальнейшие действия

- Сведения о создании шаблонов см. в статье [Разработка шаблонов](resource-group-authoring-templates.md).
- Рекомендации по безопасности диспетчера ресурсов Azure см. в разделе [Вопросы безопасности для диспетчера ресурсов Azure](best-practices-resource-manager-security.md).
- Дополнительные сведения о предоставлении доступа к состоянию в шаблоны и из них см. в разделе [Предоставление доступа к состоянию в шаблонах диспетчера ресурсов Azure](best-practices-resource-manager-state.md).

<!---HONumber=AcomDC_1223_2015-->