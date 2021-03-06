<properties
    pageTitle="Что такое лицензирование Microsoft Azure Active? | Microsoft Azure"
    description="Описание процедуры и принципа работы лицензирования Microsoft Azure Active Directory, а также рекомендации по началу работы, включая сведения о лицензировании Office 365, Microsoft Intune, а также выпусков Azure Active Directory Premium и Basic."
    services="active-directory"
	  keywords="Лицензирование Azure AD"
    documentationCenter=""
    authors="curtand"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="02/01/2016"
    ms.author="curtand"/>

# Что такое лицензирование Microsoft Azure Active?

##Описание
Azure Active Directory (Azure AD) — это служба идентификации (IDaaS) корпорации Майкрософт, которая одновременно является и решением, и платформой. Служба Azure AD предлагается в различных функциональных и технических версиях. Вы можете использовать бесплатную версию Azure AD Free, которая доступна в любой службе Майкрософт, включая Office 365, Dynamics, Microsoft Intune и Azure (в этом режиме Azure AD не начисляет плату за использование), и платные версии Azure AD, включая Enterprise Mobility Suite (EMS), Azure AD Premium и Basic, а также Azure Multi-Factor Authentication (MFA). Как и многие онлайн-службы Майкрософт, большинство платных версий Azure AD доставляются на правах доступа «на пользователя», как в Office 365, Microsoft Intune и Azure AD. В этих случаях приобретение службы представляет собой одну или несколько подписок, каждая из которых включает заранее приобретенное количество лицензий в клиенте. Права пользователя «на пользователя» предоставляются путем назначения лицензий, создания связи между пользователем и продуктом, включения компонентов службы для пользователя и использования одной из предоплаченных лицензий.

[Попробуйте Azure AD Premium.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

> [AZURE.NOTE] Портал администрирования Azure AD является частью классического портала Azure. Хотя использование Azure AD не требует приобретения продуктов Azure, для доступа к этому порталу необходима активная подписка Azure или [пробная подписка Azure](https://azure.microsoft.com/pricing/free-trial/).

Развернутый обзор возможностей службы Azure AD см. в статье [Что такое Azure AD](active-directory-whatis.md). [Дополнительные сведения об уровнях обслуживания Azure AD](https://azure.microsoft.com/support/legal/sla/)

> [AZURE.NOTE]  Подписки Azure с оплатой по мере пользования бывают разными: также отображаясь в вашем каталоге, они позволяют создавать ресурсы Azure и сопоставлять их со способом оплаты. В этом случае к подписке НЕ применяется счетчик лицензий. Связывание пользователей с подпиской и доступ пользователей к управлению ресурсами подписки реализуются путем предоставления им разрешений для работы с ресурсом Azure, сопоставленным с подпиской.


##Как работает лицензирование Azure AD?

Службы Azure AD, основанные на лицензиях или объеме обслуживания, работают путем активации подписки в клиенте службы или каталоге Azure AD. После активации подписки администраторы каталога или службы могут управлять возможностями службы, а лицензированные пользователи могут их использовать.

Когда вы покупаете или активируете Enterprise Mobility Suite, Azure AD Premium или Azure AD Basic, в каталоге обновляется подписка, в том числе срок ее действия и предоплаченные лицензии. Сведения о подписке, в том числе состояние, события следующего цикла и количество назначенных или доступных лицензий, можно просмотреть на классическом портале Azure AD на вкладке «Лицензии» конкретного каталога. Здесь также можно управлять назначениями лицензий.

Каждая подписка состоит из одного или нескольких планов обслуживания. Каждому плану соответствует входящий в комплект поставки функциональный уровень обслуживания, например Azure AD, Azure MFA, Microsoft Intune, Exchange Online или SharePoint Online. Для управления лицензиями Azure AD НЕ требуется управление уровнями плана обслуживания. Этим Azure AD отличается от Office 365, в которой используется режим расширенной конфигурации для управления доступом к службам, входящим в комплект поставки. В Azure AD для включения функций и управления отдельными разрешениями используется конфигурация службы.

Обычно управление информацией о подписке Azure AD осуществляется на классическом портале Azure на вкладке «Лицензии» конкретного каталога. Подписки Azure AD, за исключением Azure AD Premium, НЕ отображаются на портале Office.

> [AZURE.IMPORTANT] Подписки Azure AD Premium и Basic, а также Enterprise Mobility Suite ограничены подготовленными каталогами/клиентами. Подписки нельзя разделять между каталогами или использовать их для предоставления прав пользователям в других каталогах. Подписки можно перемещать между каталогами, но для этого нужно отравить запрос в службу поддержки или отменить подписку и повторно купить ее, если она приобретена напрямую.

> При приобретении Azure AD или Enterprise Mobility Suite по программе корпоративного лицензирования активация подписки происходит автоматически, если соглашение включает в себя другие службы Microsoft Online, например Office 365.

Платные функции Azure AD распространяются на весь каталог. Примеры. Назначение приложениям на основе групп, включаемое в конкретном приложении, которым вы управляете. Дополнительные возможности управления группами и самостоятельное управление группами доступны в конфигурации каталога или в конкретной группе. Расширенные отчеты о безопасности находятся на вкладке «Отчеты». Обнаружение облачных приложений отображается на портале Azure на вкладке «Идентификация».

###Назначение лицензий
Для получения подписки необходимо просто настроить платные возможности, но для использования платных функций Azure AD необходимо правильно распределить лицензии среди пользователей. Вообще, любому пользователю, который должен иметь доступ к платной функции Azure AD или который управляется с помощью этой платной функции, необходимо назначить лицензию. Назначение лицензий — это сопоставление пользователя с приобретенной службой, например Azure AD Premium, Basic или Enterprise Mobility Suite.

Назначением лицензий пользователям очень легко управлять. Для этого можно назначить группе право создавать правила назначений на портале администрирования Azure AD или назначить лицензии напрямую соответствующим пользователям с помощью портала, PowerShell или API. Если назначить лицензию группе, лицензия будет назначена всем участникам группы. Если в группу добавить пользователей или удалить их из нее, им будет назначена или для них будет отменена соответствующая лицензия. При назначении группы могут использоваться любые доступные функции управление группой; такое назначение также согласуется с назначением приложений на основе групп. Используя этот подход, вы можете настраивать правила таким образом, чтобы лицензии автоматически назначались всем пользователям в каталоге, проверять, все ли пользователи с соответствующими должностями имеют лицензии, или даже делегировать принятие решений другим менеджерам в организации.

Если вы используете назначение лицензий на основе группы, пользователи, для которых не указано расположение использования, унаследуют расположение каталога во время назначения. Администратор может изменить это расположение в любое время. Если автоматическое назначение не удалось выполнить из-за ошибки, сведения о пользователе в рамках этого типа лицензии будут отображать это состояние.

##Начало работы с Azure AD

Начать работу с Azure AD легко. Вы всегда можете создать свой каталог в ходе регистрации для использования бесплатной пробной версии Azure. [Узнайте больше о регистрации в качестве организации](sign-up-organization.md). Следующая информация поможет проверить, правильно ли ваш каталог согласован с другими службами Майкрософт, которые вы используете или планируете использовать, а также соответствует ли он вашим целями приобретения службы.

Вот несколько рекомендаций. Если вы уже используете организационные службы Майкрософт, у вас уже есть каталог Azure AD. В этом случае продолжайте использовать этот каталог и для других служб, чтобы все службы могли использовать основное управление удостоверениями, включая подготовку и гибридный единый вход. Пользователи смогут воспользоваться функциями единого входа и преимуществами усовершенствованных возможностей в разных службах. Если вы решите приобрести платную службу Azure AD для своих сотрудников, рекомендуется использовать для этого тот же каталог. Если вы планируете использовать Azure AD для различных групп пользователей (партнеров, клиентов и т. д.), если вам нужно оценить службы Azure AD и вы хотите сделать это отдельно от рабочей службы или же если вам нужно настроить тестовую среду для служб, рекомендуется сначала создать новый каталог на классическом портале Azure. [Узнайте больше о создании нового каталога Azure AD на классическом портале Azure](active-directory-licensing-directory-independence.md). Новый каталог будет создан с вашей учетной записью в качестве внешнего пользователя с разрешениями глобального администратора. После входа на классический портал Azure с помощью данной учетной записи вы увидите этот каталог и получите доступ ко всем задачам администрирования каталога. Рекомендуется создать локальную учетную запись с соответствующими привилегиями для управления другими службами Майкрософт (которые не доступны через классический портал Azure). [Узнайте больше о создании учетных записей пользователей в Azure AD](active-directory-create-users.md).

> [AZURE.NOTE] Azure AD поддерживает «внешних пользователей», которые являются учетными записями пользователей в экземпляре Azure AD, созданными с помощью учетной записи Майкрософт (MSA) или удостоверения Azure AD из другого каталога. Пока мы работаем над внедрением этой возможности во все организационные службы Майкрософт, эти учетные записи не поддерживаются в некоторых интерфейсах служб. Так, например, портал администрирования Office 365 пока что не поддерживает этих пользователей. Поэтому внешние пользователи с учетными записями Майкрософт не смогут получить доступ к порталу администрирования Office 365, а внешние пользователи из других каталогов Azure AD будут игнорироваться. Если у пользователя есть только локальная учетная запись, он может получать доступ только к каталогам Azure AD или Office 365, в которых эта учетная запись изначально создана.

Как было сказано, существуют разные платные версии Azure AD. Эти версии незначительно отличаются в отношении доступности для приобретения.


| Продукт | EA/КОРПОРАТИВНОЕ ЛИЦЕНЗИРОВАНИЕ | Откройте | 	CSP | 	Права на использование MPN | 	Прямая покупка | Пробная версия |
|---|---|---|---|---|---|---|
| Enterprise Mobility Suite |	X |	X |	X |	X | |	 	X |
| Azure AD Premium | X | X | X | | X | X |
| Azure AD Basic | X | X | X | X | <br /> | <br /> |

###Выбор одной или нескольких пробных версий
 В любом случае вы можете активировать пробную подписку Azure AD Premium или Enterprise Mobility Suite, выбрав конкретную пробную версию на вкладке лицензий в каталоге. Пробная версия содержит 30-дневную подписку со 100 лицензиями.

![Планы пробной лицензии Azure Active Directory](./media/active-directory-licensing-what-is/trial_plans.png)

![Планы пробной лицензии Enterprise Mobility Suite](./media/active-directory-licensing-what-is/EMS_trial_plan.png)

![Активация планов пробных лицензий](./media/active-directory-licensing-what-is/active_license_trials.png)

###Назначение лицензий
После активации подписки следует назначить лицензию себе и обновить браузер, чтобы убедиться, что вы видите все функции. Следующим шагом является назначение лицензий пользователям, которым требуется доступ к платным функциям Azure AD. Как уже упоминалось в разделе «Назначение лицензий», для этого рекомендуется определить группу, представляющую нужную аудиторию, и назначить ей лицензию. Таким образом, при добавлении пользователей в группу или удалении их из группы на протяжении ее жизненного цикла лицензии будут назначаться или удаляться соответственно.

Чтобы назначить лицензию группе или отдельным пользователям, выберите план лицензии, которую вы хотите назначить, и щелкните **Назначить** на панели команд.

![Активация планов пробных лицензий](./media/active-directory-licensing-what-is/assign_licenses.png)

В диалоговом окне назначения для выбранного плана можно указать пользователей, а затем добавить их в столбец **Назначить** справа. Можно пролистать список пользователей или выполнить поиск определенных пользователей с помощью функции поиска (значок лупы в верхней правой части сетки пользователя). Чтобы назначить группы, выберите "Группы" в меню **Отобразить**, а затем нажмите кнопку с галочкой справа, чтобы обновить отображенные назначения.

![Назначение лицензий группам](./media/active-directory-licensing-what-is/assign_licenses_to_groups.png)

Теперь вы можете найти или пролистать группы и добавить их в столбец **Назначить** таким же образом. Вы можете использовать эту функцию для назначения комбинации пользователей и групп с помощью одной операции. Чтобы завершить процесс назначения, нажмите кнопку с галочкой в нижнем правом углу страницы.

![Сообщение о ходе выполнения назначения лицензии](./media/active-directory-licensing-what-is/license_assignment_progress_message.png)

После назначения лицензий группе ее участники наследуют лицензии в течение 30 минут; обычно это происходит в пределах 1–2 минут.

При назначении лицензий Azure AD могут возникнуть ошибки назначения, но это случается относительно редко. Потенциальные ошибки представлены следующими случаями. - Конфликт назначений — пользователю была ранее назначена лицензия, несовместимая с текущей. В таком случае для назначения новой лицензии нужно удалить предыдущую. - Нехватка доступных лицензий — количество пользователей в группе назначения превышает количество доступных лицензий. Состояние назначения пользователей будет отображать ошибку назначения из-за отсутствия лицензий.

###Просмотр назначенных лицензий

Сводное представление назначенных лицензий, включая доступные и назначенные, а также следующее событие жизненного цикла подписки, находится на вкладке **Лицензии**.

![Просмотр количества назначенных лицензий](./media/active-directory-licensing-what-is/view_assigned_licenses.png)

Подробный список назначенных пользователей и групп, включая состояние назначения и путь (прямой или унаследованный от одной или нескольких групп), доступен при переходе к плану лицензирования.

![Подробное отображение лицензий, назначенных для плана лицензирования](./media/active-directory-licensing-what-is/assigned_licenses_detail.png)

Удаление лицензий выполнить так же просто, как назначение. Чтобы удалить лицензию, которая назначается пользователю напрямую или с помощью группы назначения, выберите **Удалить**, добавьте пользователя или группу в список удаления и подтвердите действие. Или вы можете открыть тип лицензии, выбрать определенного пользователя или группу и выбрать **Удалить** на панели команд. Чтобы отменить наследование пользователем лицензии от группы, просто удалите пользователя из группы.

###Расширение пробных версий

Клиенты могут самостоятельно расширять пробные версии на портале Office 365. Администратор клиента может перейти на [портал Office](https://portal.office.com/#Billing) (доступ зависит от разрешений для портала Office) и выбрать пробную версию Azure AD Premium. Перейдите по ссылке **Расширить пробную версию** и следуйте указаниям. Необходимо будет ввести данные кредитной карты, но оплата не будет взиматься.

![Расширение пробной версии лицензии на портале Office](./media/active-directory-licensing-what-is/extend_license_trial.png)

Клиенты могут также запросить расширение пробной версии, отправив запрос в службу поддержки. Администратор клиента может перейти на [страницу службы поддержки](http://aka.ms/extendAADtrial) портала Office 365 (доступ зависит от разрешений для страницы службы поддержки Office). На этой странице администратор должен выбрать «Подписки и пробные версии» в разделе «Функции», а также «Вопросы, связанные с пробной версией» в разделе «Симптом». Теперь администратор может ввести соответствующую информацию.

![Расширение пробной версии лицензии с помощью запроса в службу поддержки](./media/active-directory-licensing-what-is/alternate_office_aad_trial_extension.png)

## Дальнейшие действия

Теперь вы можете настроить и использовать некоторые функции Azure AD Premium.

- [Самостоятельный сброс пароля](active-directory-manage-passwords.md)
- [Самостоятельное управление группами](active-directory-accessmanagement-self-service-group-management.md)
- [Работоспособность Azure AD Connect](active-directory-aadconnect-health.md)
- [Назначение группы приложениям](active-directory-manage-groups.md)
- [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
- [Прямое приобретение лицензий Azure AD Premium](http://aka.ms/buyaadp)

<!---HONumber=AcomDC_0204_2016-->