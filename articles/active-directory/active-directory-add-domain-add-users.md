<properties
	pageTitle="Назначение пользователей пользовательскому домену в Azure Active Directory | Microsoft Azure"
	description="Инструкции по заполнению пользовательского домена в Azure Active Directory учетными записями пользователей."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="curtand;jeffsta"/>

# Назначение пользователей пользовательскому домену

Добавив пользовательский домен в Azure Active Directory, вам нужно добавить учетные записи пользователей этого домена, чтобы затем начать проверку их подлинности.

## Пользователи, синхронизируемые из каталога в корпоративной сети

Если вы уже настроили связь между локальным каталогом Active Directory и Azure Active Directory, учетные записи будут добавлены во время синхронизации. Дополнительные сведения о синхронизации Azure Active Directory с локальным каталогом Active Directory см. в статье [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

## Пользователи добавляются и управляются в облаке

Изменение домена для существующей учетной записи пользователя:

1.  Откройте классический портал Azure под учетной записью глобального администратора или администратора пользователей.

2.  Откройте свой каталог.

3.  Перейдите на вкладку **Пользователи**.

4.  Выберите пользователя из списка.

5.  Измените домен для пользователя и выберите параметр **Сохранить**.

То же самое можно сделать с помощью [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) или [API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## Выбор пользовательского домена при создании пользователя

1.  Откройте классический портал Azure под учетной записью глобального администратора или администратора пользователей.

2.  Откройте свой каталог.

3.  Перейдите на вкладку **Пользователи**.

4.  На панели команд нажмите **Добавить**.

5.  При добавлении имени пользователя выберите пользовательский домен из списка доменов.

6.  Щелкните **Сохранить**.

## Дальнейшие действия

- [Использование пользовательских доменных имен для упрощения входа пользователей в систему](active-directory-add-domain.md)
- [Добавление фирменной символики компании на страницах входа и панели доступа](active-directory-add-company-branding.md)
- [Добавление и проверка пользовательского доменного имени в Azure Active Directory](active-directory-add-domain-add-verify-general.md)
- [Изменение регистратора DNS для пользовательского доменного имени](active-directory-add-domain-change-registrar.md)
- [Удаление пользовательского доменного имени в Azure Active Directory](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0211_2016-->