<properties
   pageTitle="Синхронизация Azure AD Connect: предотвращение случайного удаления | Microsoft Azure"
   description="В этом разделе описывается функция предотвращения случайного удаления Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="andkjell"/>

# Синхронизация Azure AD Connect: предотвращение случайного удаления
В этом разделе описывается функция предотвращения случайного удаления Azure AD Connect.

При установке Azure AD Connect функция предотвращения случайного удаления будет включена по умолчанию и настроена на запрет экспорта более 500 операций удаления. Эта функция предназначена для защиты от случайных изменений конфигурации и изменений в локальном каталоге, которые могут повлиять на большое количество пользователей и других объектов.

Распространенные сценарии включают следующие:

- изменения [фильтрации](active-directory-aadconnectsync-configure-filtering.md), когда целое [подразделение](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) или [домен](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) остаются невыбранными;
- удаление всех объектов в подразделении;
- подразделение переименовывается так, что все объекты в нем становятся исключенными из области синхронизации.

Значение по умолчанию 500 можно изменить с помощью PowerShell командой `Enable-ADSyncExportDeletionThreshold`. Это значение необходимо задать в соответствии с размером вашей организации. Поскольку планировщик синхронизации будет запускаться каждые 3 часа, то это значение — количество удалений, происходящих в течение 3 часов.

Если эта функция включена и в Azure AD должно быть экспортировано слишком много операций удаления, экспорт будет остановлен и вы получите следующее сообщение электронной почты:

![Сообщение электронной почты "Предотвращение случайного удаления"](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *(Контактное лицо по техническим вопросам), здравствуйте! В (время) служба синхронизации удостоверений обнаружила, что число удалений превысило указанное в настройках пороговое значение удалений для компании (название организации). В рамках этого запуска синхронизации удостоверений для удаления было отправлено всего (число) об. Это число соответствует пороговому значению удаления объектов, равному (число), или превышает его. Нам необходимо получить от вас подтверждение, что эти удаления следует обработать, прежде чем мы продолжим. Дополнительные сведения об указанной в этом сообщении ошибке см. в разделе "Предотвращение случайных удалений".*

Если эта ситуация оказалась непредвиденной, выясните причину и выполните все действия для ее устранения. Чтобы увидеть, какие объекты должны быть удалены, выполните следующие действия.

1. Запустите **Службу синхронизации** из меню «Пуск».
2. Выберите **Соединители**.
3. Выберите соединитель типа **Azure Active Directory**.
4. В разделе **Действия** справа выберите **Пространство поиска соединителя**.
5. Во всплывающем окне в разделе **Область** выберите **Отключенные с...** и укажите момент времени в прошлом. Нажмите **Найти**. Так вы получите список всех объектов, которые должны быть удалены. По щелчку на объекте можно получить дополнительные сведения об этом объекте. Также можно щелкнуть **Настройка столбца** для добавления дополнительных атрибутов, отображаемых в сетке.

![Пространство поиска соединителя](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Если удаление необходимо, сделайте следующее.

1. Чтобы временно отключить эту защиту и выполнить операции удаления, запустите следующий командлет PowerShell: `Disable-ADSyncExportDeletionThreshold`. В ответ на запрос учетных данных укажите имя учетной записи и пароль глобального администратора Azure AD. ![Учетные данные](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
2. С выбранным соединителем Azure Active Directory укажите действие **Запустить** и затем выберите **Экспорт**.
3. Чтобы повторно включить защиту, запустите командлет PowerShell: `Enable-ADSyncExportDeletionThreshold`.

## Дальнейшие действия
Узнайте больше о настройке [службы синхронизации Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0128_2016-->