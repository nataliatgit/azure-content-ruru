<properties
	pageTitle="Предварительная версия доменных служб Azure Active Directory: функции | Microsoft Azure"
	description="Функции доменных служб Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Доменные службы Azure AD *(Предварительная версия)*

## Функции
В предварительной версии доменных служб Azure AD доступны следующие функции.

- **Простое развертывание:** доменные службы Azure AD для клиента Azure AD можно включить всего несколькими щелчками мыши. Независимо от того, является ли клиент Azure AD облачным или синхронизирован с локальным каталогом, управляемый домен можно подготовить быстро.

- **Поддержка присоединения к домену:** вы можете легко присоединить к домену компьютеры в виртуальной сети Azure, в которой доступны доменные службы Azure AD. Присоединение к домену для клиента Windows операционных систем сервера происходит незаметно для доменов, поддерживаемых доменными службами Azure AD. Также можно использовать средства автоматического присоединения к домену для таких доменов.

- **Один экземпляр домена для каждого каталога Azure AD:** вы можете создать один домен Active Directory для каждого каталога Azure AD.

- **Создание доменов с пользовательскими именами:** с помощью доменных служб Azure AD вы можете создавать домены с пользовательскими именами, например contoso.local. Это включает в себя как проверяемые, так и непроверяемые доменные имена. Также можно создать домен с суффиксом домена (т. е. *. onmicrosoft.com), предоставляемым каталогом Azure AD.

- **Интеграция с Azure AD:** для доменных служб Azure AD не нужно настраивать репликацию или управлять ею. Учетные записи пользователей, членство в группах и учетные данные пользователя (пароли) из вашего каталога Azure AD автоматически доступны в службах домена Azure AD. Новые пользователи, группы или изменения атрибутов в клиенте Azure AD или в локальном каталоге автоматически синхронизируются с доменными службами Azure AD.

- **Проверка подлинности NTLM и Kerberos:** с поддержкой проверки подлинности NTLM и Kerberos можно развертывать приложения, использующие встроенную проверку подлинности Windows.

- **Использование корпоративных учетных данных и паролей:** пароли для пользователей клиента Azure AD подходят для доменных служб Azure AD. Это означает, что пользователи в организации смогут использовать свои корпоративные учетные данные в домене — для присоединения компьютеров к домену, для входа в систему в интерактивном режиме или через удаленный рабочий стол, для проверки подлинности у контроллера домена и т. д.

- **Поддержка привязки LDAP и чтения LDAP:** для проверки подлинности пользователей в доменах, поддерживаемых службами домена Azure AD, можно использовать приложения, использующие привязки LDAP. Кроме того, приложения, использующие операции чтения LDAP для запроса атрибутов пользователя или компьютера из каталога, также смогут работать с доменными службами Azure AD.

- **Групповая политика:** можно использовать один встроенный объект групповой политики (GPO) для каждого контейнера пользователя или компьютера для обеспечения соответствия учетных записей пользователей, а также компьютеров, присоединенных к домену, политикам безопасности.

- **Доступно в различных регионах Azure:** на странице [служб Azure по регионам](https://azure.microsoft.com/regions/#services/) можно узнать, в каких регионах Azure доступны доменные службы Azure AD.

- **Высокий уровень доступности:** доменные службы AD Azure обеспечивают высокий уровень доступности для вашего домена. Это обеспечивает большее время непрерывной работы службы и отказоустойчивость. Встроенный мониторинг предоставляет средства автоматического восстановления после сбоев путем развертывания новых экземпляров для замены неисправных экземпляров и для обеспечения непрерывной работы домена.

- **Использование знакомых средств управления:** вы можете использовать знакомые средства управления Windows Server Active Directory, такие как Центр администрирования Active Directory или Active Directory PowerShell для администрирования доменов, предоставляемых доменными службами Azure AD.

<!---HONumber=AcomDC_0128_2016-->