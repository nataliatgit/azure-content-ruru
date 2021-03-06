<properties
	pageTitle="Включение прокси приложения Azure AD | Microsoft Azure"
	description="Описывается, как настроить и запустить прокси приложения Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="kgremban"/>

# Включение прокси приложения Azure AD
> [AZURE.NOTE] Прокси приложения — это функция, которая доступна только после обновления до выпуска Premium или Basic службы Azure Active Directory. Дополнительные сведения см. в статье [Выпуски Azure Active Directory](active-directory-editions.md).

Прокси-приложение Microsoft Azure AD позволяет публиковать приложения, такие как сайты SharePoint, Outlook Web Access и приложения на основе IIS, в частной сети и обеспечивает безопасный доступ для пользователей вне сети. Сотрудники могут войти в приложения дома на собственных устройствах и пройти проверку подлинности через этот облачный прокси.

Прокси-приложение работает путем установки в сети компактной службы Windows, называемой соединителем. Соединитель поддерживает исходящее подключение из сети прокси-службе. Когда пользователи обращаются к опубликованному приложению, прокси использует это подключение для предоставления доступа к приложению.

Этот раздел содержит сведения о включении прокси приложения Microsoft Azure AD для облачного каталога в Azure AD, установке соединителя прокси приложения в частной сети и регистрации соединителя в подписке клиента Microsoft Azure AD.

## Необходимые условия для прокси приложения
Прежде чем можно будет включить и использовать службы прокси приложения служб, потребуется:

- [Подписка Basic или Premium](active-directory-editions.md) Microsoft Azure AD, а также каталог Azure AD, для которого вы являетесь глобальным администратором.
- Сервер под управлением Windows Server 2012 R2 или Windows 8.1 или более поздней версии, на котором можно установить соединитель прокси приложения. Сервер должен быть способен отправлять HTTPS-запросы в службу прокси приложения в облаке, и у него должно быть подключение HTTPS к приложениям, которые вы собираетесь опубликовать.
- Если в сетевом пути используется брандмауэр, убедитесь, что он разрешает HTTPS-запросы (TCP), которые исходят из соединителя прокси приложения. Соединитель использует эти порты и поддомены, которые являются частью домена верхнего уровня: msappproxy.net и servicebus.windows.net. Обязательно откройте **все** следующие порты для **исходящего** трафика:

Номер порта | Описание
--- | ---
80 | Чтобы разрешить исходящий трафик HTTP для проверки безопасности.
443 | Чтобы включить аутентификацию пользователя в Azure AD (требуется только для процесса регистрации соединителя).
10100–10120 | Чтобы включить HTTP-ответы бизнес-системы, отправляемые на прокси-сервер.
9352, 5671 | Для обеспечения связи между соединителя и службой Azure для входящих запросов.
9350 | (Необязательно) Чтобы повысить производительность для входящих запросов.
8080 | Чтобы включить последовательность начальной загрузки соединителя и автоматическое обновление соединителя.
9090 | Для регистрации соединителя (требуется только для процесса регистрации соединителя).
9091 | Чтобы включить автоматическое обновление сертификатов доверия соединителя.

Если брандмауэр инициирует трафик в соответствии с отправляющими его пользователями, откройте эти порты для трафика, поступающего от служб Windows, которые работают как сетевая служба. Кроме того, не забудьте включить порт 8080 для NT AUTHORITY\\SYSTEM.


## Шаг 1. Включение прокси приложения в Azure AD
1. Войдите на классический портал Azure как администратор.
2. Перейдите к Active Directory и выберите каталог, в котором необходимо включить прокси приложения.
3. Щелкните **Настройка**, прокрутите список вниз до раздела "Прокси-приложение" и задайте для переключателя "Включить службы прокси-приложений для этого каталога" значение **Включено**.

	![Включение прокси приложения](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

4. Щелкните **Загрузить сейчас** в нижней части экрана. Вы перейдете на страницу скачивания. Прочтите и примите условия лицензионного соглашения, затем щелкните **Загрузить**, чтобы сохранить файл установщика Windows (EXE-файл) для соединителя прокси приложения.

## Шаг 2. Установка и регистрация соединителя
1. Запустите `AADApplicationProxyConnectorInstaller.exe` на подготовленном сервере (см. выше необходимые условия для прокси приложения).
2. Следуйте указаниям мастера установки.
3. Во время установки будет предложено зарегистрировать соединитель прокси приложения клиента Azure AD.

  - Укажите учетные данные глобального администратора Azure AD. Ваш клиент глобального администратора может отличаться от учетных данных Microsoft Azure.
  - Убедитесь, что администратор, который регистрирует соединитель, находится в том каталоге, где включены службы прокси приложения. Например, если домен клиента — contoso.com, у администратора должен быть псевдоним admin@contoso.com или другой псевдоним в этом домене.
  - Если на сервере, на который устанавливается соединитель Azure AD, конфигурация усиленной безопасности Internet Explorer **включена**, экран регистрации может быть заблокирован. В этом случае следуйте указаниям в сообщении об ошибке, чтобы разрешить доступ. Убедитесь, что конфигурация усиленной безопасности Internet Explorer отключена.
  - Если зарегистрировать соединитель не удается, см. раздел [Устранение неполадок прокси-приложения](active-directory-application-proxy-troubleshoot.md).  

4. После завершения установки на сервер добавляются две новые службы, как показано ниже. Это служба соединителя, которая обеспечивает возможность подключения, и служба автоматического обновления, которая периодически проверяет наличие новых версий соединителя и обновляет его, если это необходимо. Щелкните **Готово** в окне установки, чтобы завершить установку.

	![Службы соединителей прокси приложения](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. Теперь вы готовы к [публикации приложений с помощью прокси приложения](active-directory-application-proxy-publish.md).

Для обеспечения высокого уровня доступности необходимо развернуть по крайней мере один дополнительный соединитель. Чтобы развернуть дополнительный соединитель, повторите шаги 2 и 3, приведенные выше. Каждый соединитель необходимо зарегистрировать отдельно.

Если вы хотите удалить соединитель, удалите службу соединителя и службу обновления, а затем обязательно перезагрузите компьютер, чтобы полностью удалить службу.


## См. также
У прокси приложения гораздо больше возможностей:

- [Опубликуйте приложения с помощью прокси-сервера приложений.](active-directory-application-proxy-publish.md)
- [Публикация приложений с помощью доменного имени](active-directory-application-proxy-custom-domains.md)
- [Включение единого входа](active-directory-application-proxy-sso-using-kcd.md)
- [Включение условного доступа](active-directory-application-proxy-conditional-access.md)
- [Работа с приложениями, поддерживающими утверждения](active-directory-application-proxy-claims-aware-apps.md)
- [Устранение неполадок с прокси приложения](active-directory-application-proxy-troubleshoot.md)

## Узнайте больше о прокси приложения
- [Ознакомьтесь с блогом о прокси приложения](http://blogs.technet.com/b/applicationproxyblog/)
- [Смотрите наши видео на Channel 9!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Дополнительные ресурсы
- [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)
- [Регистрация организации в Azure](sign-up-organization.md)
- [Удостоверение Azure](fundamentals-identity.md)
- [Публикация приложений с помощью прокси-сервера приложений](active-directory-application-proxy-publish.md)

<!---HONumber=AcomDC_0211_2016-->