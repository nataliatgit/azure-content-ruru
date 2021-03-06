<properties
	pageTitle="Регистрация приложения версии 2.0 | Microsoft Azure"
	description="Как зарегистрировать приложение в корпорации Майкрософт, чтобы использовать вход и доступ к службам Майкрософт с помощью конечной точки версии 2.0."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Как зарегистрировать приложение с использованием конечной точки версии 2.0

Для создания приложения, которое поддерживает единый вход MSA b Azure AD вход, сначала необходимо зарегистрировать приложение в Майкрософт. На данном этапе вы не сможете использовать любые существующие приложения с Azure AD или учетными записями Майкрософт, необходимо будет создать новое приложение.

> [AZURE.NOTE]
	Не все сценарии и компоненты Azure Active Directory поддерживаются конечной точкой версии 2.0. Чтобы определить, следует ли вам использовать конечную точку версии 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).

## Посетите портал регистрации приложения Майкрософт
Для начала перейдите на страницу [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com). Это новый портал регистрации приложений, где вы можете управлять своими приложениями Майкрософт.

Войти с помощью личной, рабочей или учебной учетной записи Майкрософт. Если у вас нет учетной записи, зарегистрируйтесь для получения личной учетной записи. Вперед, это не займет много времени.

Готово? Теперь вы должны увидеть список своих приложений Майкрософт, который, вероятно, пуст. Изменим это.

Щелкните **Добавить приложение** и присвойте приложению имя. Портал назначит приложению глобальный уникальный идентификатор, который вы используете в коде в дальнейшем. Если приложение содержит серверный компонент, которому требуются маркеры доступа для вызова интерфейсов API (например, Office, Azure или собственного веб-API), вам необходимо также создать **секрет приложения**. <!-- TODO: Link for app secrets -->

Добавьте платформы, которые будет использовать приложение.

- Для веб-приложений укажите **универсальный код ресурса (URI) перенаправления** для отправки сообщений о входе.
- Для мобильных приложений скопируйте автоматически созданный для вас универсальный код ресурса (URI) по умолчанию.

При необходимости можно настроить внешний вид страницы входа в разделе "Профиль". Нажмите кнопку **Сохранить** перед продолжением.

> [AZURE.NOTE] При создании приложения с помощью [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com) оно будет зарегистрировано в главном клиенте учетной записи, которая используется для входа на портал. Это означает, что нельзя зарегистрировать приложение в клиенте Azure AD с помощью личной учетной записи Майкрософт. Если требуется явно зарегистрировать приложение в конкретном клиенте, войдите с помощью учетной записи, первоначально созданной в этом клиенте.

## Создание приложения быстрого запуска
Теперь, когда у вас есть приложение Майкрософт, вы можете изучить один из кратких учебников по версии 2.0. Вот несколько рекомендаций.

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

<!---HONumber=AcomDC_0224_2016-->