<properties
   pageTitle="Общие сведения о диспетчере ресурсов Azure | Microsoft Azure"
   description="Описывает, как использовать диспетчер ресурсов Azure для развертывания, контроля ресурсов в Azure и управления доступом к ним."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/02/2016"
   ms.author="tomfitz"/>

# Общие сведения о диспетчере ресурсов Azure

Обычно инфраструктура приложения состоит из ряда компонентов, например из виртуальной машины, учетной записи хранения и виртуальной сети или веб-приложения, базы данных, сервера базы данных и служб сторонних поставщиков. Эти компоненты не отображаются как отдельные сущности, вместо этого они воспринимаются как связанные и взаимозависимые части одной сущности. Их нужно развертывать, контролировать и отслеживать в виде группы. Диспетчер ресурсов Azure позволяет работать с ресурсами решения как с группой. Вы можете развертывать, обновлять или удалять все ресурсы решения в рамках одной скоординированной операции. Для развертывания вы используете шаблон, который можно использовать для разных сред, в том числе для тестовой, промежуточной и рабочей. Диспетчер ресурсов предоставляет функции безопасности, аудита и добавления тегов, помогающие управлять ресурсами после развертывания.

## Преимущества использования диспетчера ресурсов

Диспетчер ресурсов предоставляет несколько преимуществ.

- Вы можете развертывать и отслеживать все ресурсы вашего решения как единую группу, вместо того чтобы работать с ними по отдельности.
- Вы можете повторно развертывать решение на протяжении всего цикла разработки и гарантировать, что ресурсы развертываются в согласованном состоянии.
- Вы можете использовать декларативные шаблоны для определения развертывания.
- Вы можете определять зависимости между ресурсами, так чтобы они разворачивались в правильном порядке.
- Вы можете применить контроль доступа ко всем службам, так как контроль доступа на основе ролей (RBAC) изначально интегрирован в платформу управления.
- Вы можете применять к ресурсам теги для логической организации всех ресурсов в вашей подписке.
- Вы можете уточнять счета для своей организации, просмотрев сведенные затраты для всей группы или для группы ресурсов, совместно использующей один и тот же тег.  

Диспетчер ресурсов предоставляет новый способ развертывания ваших решений и управления ими. Если вы использовали более раннюю модель развертывания и хотите узнать о произошедших изменениях, см. статью [Общие сведения о развертывании диспетчера ресурсов и классическом развертывании](resource-manager-deployment-model.md).

## Руководство

Следующие рекомендации помогут вам использовать все преимущества диспетчера ресурсов при работе с вашими решениями.

1. Определите и разверните инфраструктуру с помощью декларативного синтаксиса в шаблонах диспетчера ресурсов, а не с помощью безусловных команд.
2. Определите все шаги развертывания и настройки в шаблоне. При настройке решения вы не должны выполнять какие-либо действия вручную.
3. Используйте безусловные команды для управления ресурсами, например для запуска и остановки приложения или компьютера.
4. Объедините ресурсы с одинаковым жизненным циклом в группу ресурсов. Для упорядочивания всех остальных ресурсов используйте теги.

## Группы ресурсов

Группа ресурсов представляет собой контейнер, содержащий связанные ресурсы для приложения. Группа ресурсов может содержать все ресурсы для приложения или только те ресурсы, которые логически сгруппированы вместе. Можно выбрать способ распределения ресурсов для групп ресурсов, который наиболее оптимален для вашей организации.

Существуют некоторые важные факторы, которые необходимо учитывать при определении группы ресурсов:

1. Все ресурсы в группе должны совместно использовать один и тот же жизненный цикл. Развертывание, обновление и удаление производится сразу для всех ресурсов. Если один ресурс, например сервер базы данных, должен присутствовать в другом цикле развертывания, его следует поместить в другую группу ресурсов.
2. Каждый ресурс может существовать только в одной группе ресурсов.
3. Ресурс можно добавить в группу ресурсов или удалить из нее в любое время.
4. Ресурс можно перемещать из одной группы ресурсов в другую. Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).
4. Группа ресурсов может содержать ресурсы, которые находятся в разных регионах.
5. Группу ресурсов можно использовать, чтобы определить область действия управления доступом для административных действий.
6. Ресурс можно связать с ресурсом в другой группе, если эти два ресурса должны взаимодействовать, но имеют разные жизненные циклы (например, несколько приложений, подключенных к базе данных). Дополнительные сведения см. в статье [Привязка ресурсов в диспетчере ресурсов Azure](resource-group-link-resources.md).

## Поставщики ресурсов

Поставщик ресурсов — это служба, которая предоставляет ресурсы для развертывания и управления посредством диспетчера ресурсов. Каждый поставщик ресурсов предоставляет операции REST API для работы с ресурсами. Например, если вы хотите развернуть хранилище ключей Azure для хранения ключей и секретов, вам придется работать с поставщиком ресурсов **Microsoft.KeyVault**. Этот поставщик ресурсов предлагает тип ресурса с именем **vaults** для создания хранилища ключей, тип ресурса с именем **vaults/secrets** для создания секрета в хранилище ключей, а также набор [операций REST API](https://msdn.microsoft.com/library/azure/dn903609.aspx).

Для развертывания инфраструктуры и управления ею необходимо знать определенные сведения о поставщиках ресурсов — например, типы ресурсов, которые предлагает тот или иной поставщик, номера версий операций REST API, поддерживаемые операции и схему, используемую при выборе значения типа ресурсов, который нужно создать. Сведения о поставщиках поддерживаемых ресурсов см. в статье [Поддержка диспетчера ресурсов для служб, регионов и версий API](resource-manager-supported-services.md).

## Развертывание шаблона

С помощью диспетчера ресурсов можно создать простой шаблон (в формате JSON), определяющий развертывание и конфигурацию приложения. Этот шаблон, который также называется шаблоном диспетчера ресурсов, предоставляет декларативный способ определения развертывания. С помощью шаблона можно повторно развернуть приложение на протяжении всего его жизненного цикла и гарантировать, что ресурсы развертываются в согласованном состоянии.

В шаблоне вы определяете инфраструктуру для своего приложения, способ настройки этой инфраструктуры и способ публикации в ней кода приложения. Вам не нужно беспокоиться о порядке развертывания, так как диспетчер ресурсов Azure анализирует зависимости, чтобы обеспечить создание ресурсов в правильном порядке. Дополнительные сведения см. в статье [Определение зависимостей в шаблонах диспетчера ресурсов Azure](resource-group-define-dependencies.md).

Вам не нужно определять всю инфраструктуру в одном шаблоне. Во многих случаях целесообразно разделять требования к развертыванию между несколькими наборами шаблонов, предназначенными для определенных задач. Эти шаблоны можно многократно использовать для различных решений. Чтобы развернуть конкретное решение, создайте главный шаблон, который связывает все необходимые шаблоны. Дополнительные сведения см. в статье [Использование связанных шаблонов в диспетчере ресурсов Azure](resource-group-linked-templates.md).

Кроме того, шаблон можно использовать для внесения обновлений в инфраструктуру. Например, можно добавить новый ресурс в приложение и добавить правила конфигурации для уже развернутых ресурсов. Если шаблон указывает создание нового ресурса, но этот ресурс уже существует, диспетчер ресурсов Azure выполняет обновление вместо создания нового ресурса. Диспетчер ресурсов Azure обновляет существующий актив до того состояния, который бы имел новый ресурс. Или можно дать диспетчеру ресурсов инструкцию удалять все ресурсы, которые не указаны в шаблоне. Чтобы понять отличия параметров при развертывании, см. статью [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](resource-group-template-deploy.md).

В шаблоне можно указать параметры, чтобы сделать развертывание настраиваемым и гибким. Например, можно передать значения параметров, которые подстраивают развертывание под тестовую среду. Задавая параметры, можно использовать один и тот же шаблон для развертывания во всех средах приложения.

Диспетчер ресурсов предоставляет расширения для сценариев, когда необходимы дополнительные операции, такие как установка определенного программного обеспечения, не включенного в программу установки. Если вы уже используете службу управления конфигурацией, такую как DSC, Chef или Puppet, можете продолжить работу с ней с помощью расширений.

При создании решения из Marketplace оно автоматически включает в себя шаблон развертывания. Вам не нужно создавать шаблон с нуля, поскольку можно начать с шаблона для своего решения и настроить его в соответствии с конкретными потребностями.

Наконец, шаблон становится частью исходного кода для вашего приложения. Можно включить его в репозиторий исходного кода и обновлять по мере развития приложения. Шаблон можно изменить в Visual Studio.

Дополнительную информацию об определении шаблона см. в разделе [Создание шаблонов диспетчера ресурсов Azure](./resource-group-authoring-templates.md).

Сведения об использовании шаблонов для развертывания см. в статье [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](resource-group-template-deploy.md).

Рекомендации по структурированию шаблонов см. в статье [Рекомендации по разработке шаблонов диспетчера ресурсов Azure](best-practices-resource-manager-design-templates.md).

Инструкции по развертыванию решения в различных средах см. в статье [Среды разработки и тестирования в Microsoft Azure](solution-dev-test-environments.md).

## Теги

Диспетчер ресурсов предоставляет возможность добавления тегов, которая позволяет классифицировать ресурсы в соответствии с вашими требованиями к управлению или выставлению счетов. Использование тегов может потребоваться, когда имеется сложная коллекция ресурсов и групп ресурсов и требуется визуализировать эти активы наиболее оптимальным для вас образом. Например, можно снабдить тегами ресурсы, которые выполняют в организации одну и ту же роль или относятся к одному отделу. Без тегов пользователи в организации могут создать несколько ресурсов, которые впоследствии будет очень трудно идентифицировать. То же касается и управления такими ресурсами. Например, вам нужно удалить все ресурсы для конкретного проекта. Если к этим ресурсам не добавлены проектные теги, вам придется искать ресурсы вручную. Добавление тегов может позволить сократить затраты при работе с подпиской.

Один тег могут иметь ресурсы, находящиеся в разных группах ресурсов. Можно создать собственную классификацию тегов, чтобы все пользователи в организации использовали одинаковые теги и никто из них не мог случайно указать немного отличающиеся теги (например, "отдел" вместо "отделения").

Дополнительные сведения о тегах см. в статье [Использование тегов для организации ресурсов Azure](./resource-group-using-tags.md). Вы также можете создать [настроенную политику](#manage-resources-with-customized-policies), которая будет требовать добавлять теги к ресурсам во время развертывания.

## управление доступом;

Диспетчер ресурсов позволяет управлять доступом к определенным действиям внутри вашей организации. Он по умолчанию интегрирует OAuth и управление доступом на основе ролей (RBAC) в платформу управления и применяет такое управление доступом ко всем службам в группе ресурсов. Вы можете добавить пользователей в заранее определенную платформу и относящиеся к конкретному ресурсу роли, а также применить эти роли к подписке, группе ресурсов или ресурсу для ограничения доступа. Например, можно воспользоваться преимуществами предварительно определенной роли с именем "Участник баз данных SQL", которая позволяет пользователям управлять базами данных, но не серверами баз данных или политиками безопасности. Вы добавляете пользователей организации, которым требуется такой тип доступа, в роль "Участник баз данных SQL" и применяете роль к подписке, группе ресурсов или ресурсу.

Диспетчер ресурсов автоматически регистрирует действия пользователя для аудита. Дополнительные сведения о работе с журналами аудита см. в статье [Операции аудита с помощью диспетчера ресурсов](resource-group-audit.md).

Дополнительные сведения о контроле доступа на основе ролей см. в статье [Контроль доступа на основе ролей Azure Active Directory](./active-directory/role-based-access-control-configure.md). Статья [RBAC: встроенные роли](./active-directory/role-based-access-built-in-roles.md) содержит список встроенных ролей и разрешенных действий. К встроенным ролям относятся общие роли, такие как владелец, читатель и участник, а также роли для конкретной службы, например участник виртуальной машины, участник виртуальной сети и диспетчер безопасности SQL (и это только незначительная часть существующих ролей).

Можно также явно блокировать критически важные ресурсы, чтобы запретить пользователям удалять или изменять их. Дополнительные сведения см. в статье [Блокировка ресурсов с помощью диспетчера ресурсов Azure](resource-group-lock-resources.md).

Рекомендации см. в статье [Вопросы безопасности при работе с диспетчером ресурсов Azure](best-practices-resource-manager-security.md).

## Настраиваемые политики управления ресурсами

Диспетчер ресурсов позволяет создавать настраиваемые политики для управления ресурсами. Типы создаваемых политик могут охватывать самые разные сценарии, включая применение соглашения об именовании ресурсов, ограничение типов и экземпляров развертываемых ресурсов, ограничение регионов, в которых могут размещаться ресурсы, а также требование присваивать ресурсам значение тега для упорядочивания выставления счетов по отделам. Политики создаются для того, чтобы снижать затраты и поддерживать согласованность в подписке. Дополнительные сведения см. в статье [Применение политик для управления ресурсами и контроля доступа](resource-manager-policy.md).

## Уровень согласованного управления

Диспетчер ресурсов предоставляет полностью совместимые операции в рамках Azure PowerShell, интерфейса командной строки Azure для Mac, Linux и Windows, портала Azure и REST API. Можно использовать тот интерфейс, который подходит вам лучше всего, и быстрого переключаться между интерфейсами безо всякой путаницы. На портале даже отображаются уведомления о действиях, выполняемых за пределами портала.

Сведения о PowerShell см. в разделах [Использование Windows PowerShell с диспетчером ресурсов](./powershell-azure-resource-manager.md) и [Командлеты диспетчера ресурсов Azure](https://msdn.microsoft.com/library/azure/dn757692.aspx).

(Информацию об интерфейсе командной строки Azure см. в разделе [Использование интерфейса командной строки Azure для Mac, Linux и Windows со средствами управления ресурсами Azure](./xplat-cli-azure-resource-manager.md).)

Сведения о API REST см. в разделе [Справочник по API REST диспетчера ресурсов Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx).

Сведения об использовании портала см. в статье [Управление ресурсами Azure с помощью портала Azure](azure-portal/resource-group-portal.md).

Диспетчер ресурсов Azure поддерживает общий доступ к ресурсам независимо от источника (CORS). Доступ CORS позволяет вызывать REST API диспетчера ресурсов или REST API служб Azure из веб-приложения, которое расположено в другом домене. Без поддержки CORS веб-браузер запрещает приложению, расположенному в одном домене, обращаться к ресурсам, расположенным в другом домене. Диспетчер ресурсов включает доступ CORS для всех запросов с учетными данными, прошедшими проверку подлинности.

## Дальнейшие действия

- Сведения о создании шаблонов см. в статье [Разработка шаблонов](./resource-group-authoring-templates.md).
- Инструкции по развертыванию созданных шаблонов см. в статье [Развертывание шаблонов](resource-group-template-deploy.md).
- Сведения о том, какие функции можно использовать в шаблоне, см. в статье [Функции шаблонов](./resource-group-template-functions.md).
- Рекомендации по разработке шаблонов см. в статье [Рекомендации по разработке шаблонов диспетчера ресурсов Azure](best-practices-resource-manager-design-templates.md).

Краткий обзор диспетчера ресурсов также представлен в этом видео:

[AZURE.VIDEO azure-resource-manager-overview]

<!---HONumber=AcomDC_0204_2016-->