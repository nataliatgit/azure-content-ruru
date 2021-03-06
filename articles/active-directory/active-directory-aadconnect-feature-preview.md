<properties
   pageTitle="Функции, предварительные версии Azure AD Connect | Microsoft Azure"
   description="В этом разделе более подробно описаны функции Azure AD Connect, находящиеся на этапе предварительной версии."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="02/16/2016"
   ms.author="andkjell;billmath"/>

# Дополнительная информация о функциях в предварительной версии
В этом разделе описывается, как использовать функции, которые сейчас доступны в предварительной версии.

## Обратная запись групп
Обратная запись группы в дополнительных функциях позволит вам выполнять обратную запись **групп Office 365** в лес с установленным Exchange. Это группа, управление которой всегда происходит в облаке. При наличии локальной установки Exchange вы можете записать эти группы обратно в локальную среду, чтобы пользователи с локальными почтовыми ящиками Exchange могли отправлять и получать сообщения электронной почты из этих групп.

Дополнительные сведения о группах Office 365 и работе с ними см. [здесь](http://aka.ms/O365g).

Эта группа будет представлена в виде группы рассылки в локальной службе AD DS. На локальном сервере Exchange должна быть установлена версия Exchange 2013 с накопительным пакетом обновления 8 (выпущен в марте 2015 года) или Exchange 2016, чтобы он мог распознавать этот новый тип группы.

**Примечания на период действия предварительной версии**

- Сейчас, в предварительной версии, атрибут адресной книги не заполняется. Без этого атрибута группа не будет отображаться в глобальном списке адресов. Для заполнения этого атрибута проще всего использовать командлет Exchange PowerShell `update-recipient`.
- Только леса со схемой Exchange являются допустимыми целевыми объектами для групп. Если схема Exchange не будет обнаружена, обратную запись группы не удастся включить.
- В настоящее время поддерживаются развертывания организаций Exchange только с одним лесом. При наличии нескольких локальных организаций Exchange вам потребуется локальное решение GALSync, чтобы эти группы отображались в других лесах.
- Функция обратной записи группы в настоящее время не обрабатывает группы безопасности или группы рассылки.

>[AZURE.NOTE] Для обратной записи групп требуется подписка Azure AD Premium.

## Обратная запись пользователей
> [AZURE.IMPORTANT] Предварительная версия функции обратной записи пользователей была временно удалена в августовском обновлении (2015 г.) для Azure AD Connect. Если вы включили ее, следует отключить эту функцию.

Обратная запись пользователей находится на этапе ранней предварительной версии. Его можно использовать только в том случае, если Azure AD является источником для всех объектов-пользователей и локальный каталог Active Directory пуст перед включением этой функции (развертывание с нуля).

>[AZURE.WARNING] Эту функцию следует проверять только в тестовой среде и нельзя использовать в каталоге Azure AD, используемом в рабочей среде.

.

>[AZURE.NOTE] Для обратной записи пользователей требуется подписка Azure AD Premium.

## Дальнейшие действия
Продолжите [выборочную установку Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->