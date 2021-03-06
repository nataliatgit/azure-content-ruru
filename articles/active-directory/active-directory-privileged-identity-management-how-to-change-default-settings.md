<properties
   pageTitle="Управление привилегированными пользователями Azure: изменение и просмотр параметров по умолчанию для роли"
   description="Узнайте, как изменять параметры по умолчанию для привилегированных пользователей с помощью расширения для управления привилегированными пользователями Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Управление привилегированными пользователями Azure: изменение и просмотр параметров активации по умолчанию для роли

## Изменение и просмотр активации роли по умолчанию
1. На панели мониторинга щелкните роль, которую необходимо настроить в таблице ролей.
2. Щелкните **Параметры**.
3. Задайте длительность активации по умолчанию в часах, передвинув ползунок или введя количество часов в текстовое поле.
4. Щелкните **Включить** или **Выключить**, чтобы отправлять или, наоборот, не отправлять уведомления об активации администраторам.
5. Щелкните **Включить** или **Выключить**, чтобы разрешить или запретить администраторам вводить сведения о билетах в запросы на активацию.
6. Щелкните **Включить** или **Выключить**, чтобы требовать или не требовать многофакторную проверку подлинности для запроса на активацию. Дополнительные сведения об использовании MFA при управлении привилегированными пользователями см. в статье [Как сделать использование MFA обязательным](active-directory-privileged-identity-management-how-to-require-mfa.md).
7. Щелкните **Включить** или **Выключить**, чтобы разрешить глобальным администраторам быть временными администраторами.
8. Щелкните **Сохранить**.

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0204_2016-->