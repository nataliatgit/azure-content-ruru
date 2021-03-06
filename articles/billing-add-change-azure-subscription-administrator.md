<properties
	pageTitle="Добавление или изменение ролей администратора Azure | Microsoft Azure"
	description="Инструкции по добавлению или изменению соадминистратора Azure, администратора службы или администратора учетной записи"
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor="meerak"
	tags="billing"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="genli"/>

# Добавление или изменение ролей администратора Azure

В Microsoft Azure существует три роли администратора.

| Роль администратора | Ограничение | Описание
| ------------- | ------------- |---------------|
|Администратор учетной записи | Один на учетную запись Azure |Это лицо, которое оформило или приобрело подписку Azure и получило доступ к [центру учетных записей](https://account.windowsazure.com/Home/Index) и возможность выполнять разные задачи управления. Сюда относится возможность создавать, отменять подписки, менять тарифный план для подписки и изменять администратора службы.
| Администратор службы | Один на подписку Azure |Этот пользователь авторизован управлять службами на [портале Azure](https://manage.windowsazure.com/). По умолчанию для новой подписки администратор учетных записей также является администратором службы.|
|Соадминистратор|200 на подписку|Это лицо имеет те же права доступа, что и администратор службы, но не может менять связь подписок с каталогами Azure.|

> [AZURE.NOTE] Контроль доступа на основе ролей Azure Active Directory позволяет назначать пользователям сразу несколько ролей. Дополнительные сведения см. в статье [Контроль доступа на основе ролей в Azure Active Directory](./active-directory/role-based-access-control-configure.md).

## Добавление соадминистратора для подписки

1. Войдите на [классический портал Azure](https://manage.windowsazure.com/).

2. В области навигации выберите **Параметры**> **Администраторы**> **Добавить**. </br>

	![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)

3. Введите адрес электронной почты лица, которое нужно добавить в качестве соадминистратора, а затем выберите подписку, доступ к которой нужно предоставить соадминистратору.</br>

	![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

Следующий адрес электронной почты можно добавить в качестве соадминистратора.

* **Учетная запись Майкрософт** (ранее Windows Live ID) </br> Вы можете использовать учетную запись Майкрософт для входа во все клиентские продукты Microsoft и облачные службы, включая Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone и Xbox LIVE.
* **Учетная запись организации**</br> Учетная запись организации — это учетная запись, созданная в каталоге Azure Active Directory. Адрес учетной записи организации выглядит приблизительно так: user@&lt;your домен&gt;.onmicrosoft.com

## Ограничения

 * Каждая подписка связана с каталогом Azure AD (также известным как каталог по умолчанию). Чтобы найти каталог по умолчанию, с которым связана подписка, перейдите на [Классический портал Azure](https://manage.windowsazure.com/) и выберите **Параметры** > **Подписки**. Проверьте идентификатор подписки, чтобы найти каталог по умолчанию.

 * Если выполнен вход с учетной записью Майкрософт, в качестве соадминистраторов вы можете добавить только другие учетные записи Майкрософт или пользователей каталога по умолчанию.

 * Если вы выполняете вход с использованием учетной записи организации, вы можете добавлять другие учетные записи организации в своей организации в качестве соадминистраторов. Например, abby@contoso.com может добавить bob@contoso.com в качестве администратора службы или соадминистратора, но не может добавить john@notcontoso.com, если john@noncontoso.com не является пользователем каталога по умолчанию. Пользователи, выполнившие вход с использованием учетной записи организации, могут и далее добавлять пользователей учетной записи Майкрософт в качестве администратора служб или его помощника.

 * Теперь в Azure можно войти с учетной записью организации, и ниже приведены изменения в требованиях к учетным записям администратора службы и администратора.

	| Способ входа в систему| Добавить учетную запись Майкрософт или пользователей каталога по умолчанию в качестве соадминистратора или администратора службы? |Добавить учетную запись организации в той же организации в качестве соадминистратора или администратора службы? |Добавить учетную запись организации в другой организации в качестве соадминистратора или администратора службы?
	| ------------- | ------------- |---------------|---------------|
	|Учетная запись Майкрософт |Да|Нет|Нет|
	|Учетная запись организации|Да|Да|Нет|

## Изменение администратора службы для подписки
Только администратор учетной записи может изменить администратора службы для подписки.

1. Выполните вход на [портал управления учетными записями](https://account.windowsazure.com/subscriptions), воспользовавшись администратором учетной записи.

2. Выберите подписку, которую требуется изменить.

3. В правой части щелкните **Изменить сведения о подписке**. </br>

	![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)

4. В поле **АДМИНИСТРАТОР СЛУЖБЫ** введите адрес электронной почты нового администратора службы. </br>

	![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## Изменение администратора учетной записи

Для передачи учетной записи Azure другой учетной записи обратитесь к статье [Передача подписки Azure](billing-subscription-transfer.md).

## Дальнейшие действия

* Чтобы подробнее узнать о том, как осуществляется доступ к ресурсам в Microsoft Azure, обратитесь к статье [Основные сведения о доступе к ресурсам в Azure](./active-directory/active-directory-understanding-resource-access.md).

* Дополнительные сведения о связи Azure Active Directory с подпиской Azure см. в разделе [Как подписки Azure связаны с Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated directory.md)

* Дополнительные сведения о связи Azure Active Directory с подпиской Azure см. в разделе [Назначение ролей администратора в Azure Active Directory](./active-directory/active-directory-assign-admin-roles.md)

<!---HONumber=AcomDC_0218_2016-->