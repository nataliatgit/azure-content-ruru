<properties
   pageTitle="Центр безопасности Azure: часто задаваемые вопросы | Microsoft Azure"
   description="Эта статья содержит ответы на часто задаваемые вопросы о Центре безопасности Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/23/2016"
   ms.author="terrylan"/>

# Центр безопасности Azure: часто задаваемые вопросы

Данная статья содержит ответы на часто задаваемые вопросы о Центре безопасности Azure — службе, которая помогает вам выявлять и предотвращать угрозы, а также принимать ответные меры благодаря более полной информации о состоянии ресурсов Microsoft Azure и контролю над их безопасностью.

> [AZURE.NOTE] Сведения в этом документе относятся к предварительной версии Центра безопасности Azure.

## Общие вопросы

### Что такое Центр безопасности Azure?
Центр безопасности Azure помогает вам выявлять и предотвращать угрозы, а также принимать ответные меры благодаря более полной информации о состоянии ресурсов Azure и контролю над их безопасностью. Он включает встроенные функции мониторинга безопасности и управления политиками для подписок, помогает выявлять угрозы, которые в противном случае могли бы оказаться незамеченными, и взаимодействует с широким комплексом решений по обеспечению безопасности.

### Как включить Центр безопасности Azure?
Центр безопасности Azure включается с помощью подписки Microsoft Azure и доступен на [портале Azure](https://azure.microsoft.com/features/azure-portal/). ([Войдите на портал](https://portal.azure.com), выберите **Обзор** и прокрутите экран до пункта **Центр безопасности**.) На панели мониторинга могут сразу появиться рекомендации по обеспечению безопасности. Это связано с тем, что служба может оценивать состояние безопасности определенных элементов управления по их конфигурации в Azure. Чтобы активировать весь набор функций мониторинга, рекомендаций и предупреждений, [включите сбор данных](#data-collection).

## Выставление счетов

### Как выставляются счета за использование Центра безопасности Azure?
См. статью [Стоимость Центра безопасности](https://azure.microsoft.com/pricing/details/security-center/).

## Сбор данных

### Как включить сбор данных?<a name=data-collection></a>
Включить сбор данных для подписки Azure вы можете в политике безопасности. Чтобы включить сбор данных, [войдите на портал Azure](https://portal.azure.com), выберите **Обзор**, а затем пункты **Центр безопасности** и **Политика безопасности**. Установите для параметра **Сбор данных** значение **Вкл.** и настройте учетные записи хранения, в которых нужно собирать данные (см. вопрос "[Где хранятся мои данные?](#where-is-my-data-stored)"). Если функция **сбора данных** включена, она автоматически собирает сведения о конфигурации и событиях безопасности со всех поддерживаемых виртуальных машин в подписке.

### Что происходит при включении сбора данных?
Сбор данных включается с помощью агента мониторинга Azure и расширения "Мониторинг безопасности Azure". Расширение "Мониторинг безопасности Azure" сканирует систему на наличие конфигураций, связанных с безопасностью, и передает их в [службу трассировки событий для Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Кроме того, для любого действия операционная система вызывает события журнала событий. Агент мониторинга Azure считывает записи журнала событий и трассировки ETW и копирует их в вашу учетную запись хранения для анализа. Это учетная запись хранения, настроенная в политике безопасности. Дополнительные сведения об учетной записи хранения см. в вопросе "[Где хранятся мои данные?](#where-is-my-data-stored)".

### Влияет ли агент мониторинга или расширение "Мониторинг безопасности" на работу серверов?
Агент и расширение потребляют номинальное количество системных ресурсов и не должны существенно влиять на производительность.

### Как отключить сбор данных, если он больше не требуется?
Отключить **сбор данных** для подписки вы можете в политике безопасности. ([Войдите на портал Azure](https://portal.azure.com), выберите **Обзор**, а затем пункты **Центр безопасности** и **Политика безопасности**.) При выборе подписки открывается новая колонка, в которой вы можете отключить сбор данных. В верхней ленте выберите параметр **Удалить агентов**, чтобы удалить агентов с существующих виртуальных машин.

### Где хранятся мои данные?<a name=where-is-my-data-stored></a>
Для каждого региона, в котором у вас есть виртуальные машины, выберите учетную запись хранения, где хранятся данные, собираемые с этих виртуальных машин. Это позволяет хранить данные в одной географической области, чтобы обеспечить их конфиденциальность и независимость. Выберите учетную запись хранения для подписки в разделе "Политика безопасности". ([Войдите на портал Azure](https://portal.azure.com), выберите **Обзор**, а затем пункты **Центр безопасности** и **Политика безопасности**.) При выборе подписки открывается новая колонка. Щелкните **Выбрать учетную запись хранения**, чтобы выбрать регион. В целях безопасности собранные данные логически изолируются от других данных клиентов.

Дополнительные сведения о хранилище Azure и учетных записях хранения см. в статье [Документация по хранилищу](https://azure.microsoft.com/documentation/services/storage/) и [Учетные записи хранения Azure](../storage/storage-create-storage-account.md).

## Работа с Центром безопасности Azure

### Что такое политика безопасности?
Политика безопасности определяет набор средств контроля, которые рекомендуются для ресурсов в указанной подписке. В Центре безопасности Azure вы можете настраивать политики для подписок Azure в соответствии с требованиями безопасности вашей компании, типом приложений и конфиденциальностью данных в каждой подписке.

Например, требования к безопасности ресурсов, используемых для разработки или тестирования, могут отличаться от тех, которые используются для рабочих приложений. Аналогично: приложения с контролируемыми данными, такими как персональные данные (PII), могут требовать более высокого уровня безопасности. Политики безопасности, включенные в Центре безопасности Azure, позволяют получать рекомендации по обеспечению безопасности и осуществлять мониторинг. Дополнительные сведения о политиках безопасности см. в разделе [Наблюдение за работоспособностью системы безопасности в Центре безопасности Azure](security-center-monitoring.md).

### Кто может изменять политику безопасности?
Политики безопасности настраиваются для каждой подписки. Для изменения политики безопасности требуются права владельца или участника этой подписки.

Дополнительные сведения о настройке политики безопасности см. в статье [Настройка политик безопасности в Центре безопасности Azure](security-center-policies.md).

### Что такое рекомендация по безопасности?
Центр безопасности Azure анализирует состояние безопасности ресурсов Azure. При обнаружении потенциальной уязвимости системы безопасности создаются рекомендации. Рекомендации помогают выполнить процедуру настройки необходимого средства контроля. Примеры

- Подготовка защиты от вредоносных программ помогает обнаруживать и устранять вредоносные программы.
- Настройка [групп безопасности сети](../virtual-network/virtual-networks-nsg.md) и правил управления трафиком для виртуальных машин
- Подготовка брандмауэра веб-приложения для защиты от атак на веб-приложения
- Развертывание отсутствующих обновлений системы
- Обращение к конфигурациям операционной системы, не соответствующих рекомендуемым базовым показателям

Здесь отображаются только рекомендации, включенные в политиках безопасности.

### Как увидеть текущее состояние безопасности ресурсов Azure?
Элемент **Работоспособность ресурсов** в колонке **Центр безопасности** показывает общий уровень защищенности вашей среды с детализацией по виртуальным машинам, веб-приложениям и другим ресурсам. Каждый ресурс имеет индикатор, указывающий на обнаружение потенциальных проблем безопасности. При выборе элемента "Работоспособность ресурсов" отображаются ресурсы и указывается, где необходимо вмешательство и какие проблемы могут существовать.

### Что инициирует оповещение системы безопасности?
Центр безопасности Azure автоматически собирает, анализирует и объединяет данные журнала из ресурсов Azure, сети и решений партнеров, таких как программы защиты от вредоносных программ и брандмауэры. При обнаружении угроз создается оповещение системы безопасности. Например, определяется следующее.

- Обращение взломанных виртуальных машин к опасному IP-адресу
- Обнаружение сложных вредоносных программ с использованием отчетов об ошибках Windows
- Грубые атаки на виртуальные машины
- Оповещения системы безопасности, отправленные встроенными партнерскими решениями обеспечения безопасности, такими как защита от вредоносных программ или брандмауэры веб-приложений

### Как обрабатываются разрешения в Центре безопасности Azure?
Центр обеспечения безопасности Azure поддерживает доступ на основе ролей. Дополнительные сведения об управлении доступом на основе ролей (RBAC) в Azure см. в статье [Контроль доступа на основе ролей в Azure Active Directory](../active-directory/role-based-access-control-configure.md).

Когда пользователь открывает Центр безопасности Azure, он видит только те рекомендации и оповещения, которые относятся к доступным ему ресурсам. Это означает, что пользователь будет видеть элементы, относящиеся только к тем ресурсам, где ему назначена роль владельца, участника или читателя подписки или группы ресурсов, к которым относится ресурс.

Для изменения политики безопасности требуются права владельца или участника подписки.

### Какие типы виртуальных машин будут поддерживаться?
Поддерживаются [виртуальные машины, созданные с помощью диспетчера ресурсов и классической модели развертывания](../azure-classic-rm.md), включая виртуальные машины, входящие в кластеры Azure Service Fabric.

Рекомендации в разделе "Контроль доступа" сейчас касаются только виртуальных машин, созданных с помощью классической модели развертывания. Группы безопасности сети в настоящее время работают только с виртуальными машинами, созданными с помощью диспетчера ресурсов.

### Поддерживаются ли виртуальные машины Linux?
Центр безопасности Azure предлагает базовые функции мониторинга для виртуальных машин Linux (только Ubuntu версий 12.04, 14.04, 14.10 и 15.04). Впоследствии будут добавлены функции мониторинга работоспособности и сбора или анализа данных, а также поддержка других дистрибутивов Linux.

<!---HONumber=AcomDC_0224_2016-->