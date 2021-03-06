<properties
	pageTitle="Предварительная версия Azure Active Directory B2C: регистрация приложения | Microsoft Azure"
	description="Как зарегистрировать приложение для использования Azure Active Directory B2C."
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/25/2016"
	ms.author="swkrish"/>


# Предварительная версия Azure Active Directory B2C: регистрация приложения

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Предварительные требования

Чтобы создать приложение с поддержкой регистрации и входа пользователей, его сначала нужно зарегистрировать с использованием клиента Azure Active Directory B2C. Дополнительные сведения о получении собственного клиента см. в статье [Предварительная версия: создание клиента Azure AD B2C](active-directory-b2c-get-started.md). Если вы выполнили все шаги в этой статье, то к вашей начальной панели будет прикреплена колонка функций B2C.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Переход в колонку функций B2C

В колонку функций B2C можно перейти из портала Azure или классического портала Azure.

### 1\. Переход из портала Azure

Если колонка функций B2C закреплена на начальной панели, то она отобразится сразу же после того, как вы войдете на [портал Azure](https://portal.azure.com/) в качестве глобального администратора клиента B2C.

Также можно перейти в колонку, щелкнув **Обзор** и затем **Azure AD B2C** в левой области навигации на [портале Azure](https://portal.azure.com/).

Кроме того, можно перейти к колонке напрямую, открыв адрес [https://portal.azure.com/{клиент}.onmicrosoft.com/?#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/](https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/), где **{клиент}** нужно заменить именем, использованным во время создания клиента (например contosob2c). Можно создать закладку для этой ссылки для дальнейшего использования.

    > [AZURE.IMPORTANT]
    You need to be a Global Administrator of the B2C tenant to be able to access the B2C features blade. A Global Administrator from any other tenant or a user from any tenant cannot access it.

### 2\. Доступ через классический портал Azure

Войдите на [классический портал Azure](https://manage.windowsazure.com/) под именем администратора подписки. Это та же рабочая или учебная учетная запись либо учетная запись Майкрософт, которая использовалась для регистрации в Azure. Перейдите к расширению Active Directory слева и щелкните свой клиент B2C. На вкладке **Быстрый запуск** (первая открывшаяся вкладка) щелкните **Управление настройками B2C** в разделе **Администрирование**. Откроется колонка функций B2C в новом окне или на вкладке браузера.

Кроме того, ссылка **Управление настройками B2C** есть и на вкладке **Настройка** (в разделе **Администрирование B2C**).

## Регистрация приложения

1. В колонке функций B2C на портале Azure щелкните **Приложения**.
2. Нажмите **+Добавить** в верхней части колонки.
3. Введите **Название** приложения, которое будет служить его описанием для пользователей. Например, введите "Приложение Contoso B2C".
4. Если вы создаете веб-приложение, переведите переключатель **Включить веб-приложение или веб-API** в положение **Да**. **URL-адреса ответа** — это конечные точки, куда Azure AD B2C будет возвращать все маркеры, запрашиваемые вашим приложением. Например, введите `https://localhost:44321/`. Если приложение включает в себя серверный компонент (API), который необходимо защитить, то имеет смысл создать и скопировать **секрет приложения**, нажав кнопку **Создать ключ**.

    > [AZURE.NOTE]
    **Секрет приложения** — это важные учетные данные безопасности.

5. Если вы создаете мобильное приложение, переведите переключатель **Включить собственный клиент** в положение **Да**. Скопируйте **URI перенаправления**, автоматически созданный по умолчанию.
6. Чтобы зарегистрировать приложение, щелкните **Создать**.
7. Щелкните приложение, которое вы только что создали, и скопируйте глобальный уникальный **идентификатор приложения**, который вы позже будете использовать в коде.

## Создание приложения быстрого запуска

Теперь, когда у вас есть приложение, зарегистрированное для использования Azure AD B2C, вы можете изучить один из кратких учебников, чтобы подготовить его к работе и запустить. Вот несколько рекомендаций.

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=AcomDC_0302_2016-->