<properties
	pageTitle="Общие сведения о регистрации устройств в Azure Active Directory | Microsoft Azure"
	description="Регистрация устройств в Azure Active Directory лежит в основе всех сценариев условного доступа с устройств. Служба регистрации устройств Azure Active Directory предоставляет регистрируемому устройству идентификатор, который позволяет аутентифицировать его при входе пользователя."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/24/2015"
	ms.author="femila"/>

# Общие сведения о регистрации устройств в Azure Active Directory

Регистрация устройств в Azure Active Directory лежит в основе всех сценариев условного доступа с устройств. Служба регистрации устройств Azure Active Directory предоставляет регистрируемому устройству идентификатор, который позволяет аутентифицировать его при входе пользователя. Аутентифицированное устройство и его атрибуты затем можно использовать для принудительного соблюдения политик условного доступа в приложениях, размещенных в облаке и локально. При использовании решения для управления мобильными устройствами, такого как Intune, атрибуты устройства в Azure Active Directory обновляются путем добавления дополнительных данных о нем. Это позволяет создавать правила условного доступа, чтобы обеспечить соблюдение стандартов безопасности и нормативных требований к организации доступа с устройств. Служба регистрации устройств реализована в Azure Active Directory. Она поддерживает устройства iOS, Android и Windows. В отдельных сценариях использования службы регистрации устройств в Azure Active Directory могут действовать особые требования и поддерживаться другие платформы. Далее в этом разделе приведена более подробная информация о конкретных сценариях, доступных на сегодняшний день.

## Сценарии, обеспечиваемые службой регистрации устройств Azure Active Directory

Службу регистрации устройств Azure AD можно рассматривать как основу для реализации различных сценариев. В общем случае она поддерживает устройства iOS, Android и Windows. В отдельных сценариях использования службы регистрации устройств Azure AD могут действовать особые требования и поддерживаться другие платформы. К таким сценариям относятся перечисленные ниже. Условный доступ к приложениям, размещенным локально. Для зарегистрированных устройств можно применять политики доступа к приложениям, настроенным для использования служб федерации Active Directory в Windows Server 2012 R2. Узнать больше о настройке условного доступа для локальной среды можно в разделе «Настройка локального условного доступа с помощью регистрации устройств в Azure Active Directory».

Условный доступ к приложениям Office 365 с использованием Microsoft Intune. ИТ-администраторы могут создавать политики условного доступа с устройств для защиты корпоративных ресурсов, разрешая при этом информационным работникам обращаться к службам с совместимых устройств. Дополнительные сведения см. в разделе «Политики условного доступа к Office 365 с устройств».

##Настройка регистрации устройств в Azure Active Directory

Для службы регистрации устройств Azure Active Directory доступны указанные ниже параметры. Включение регистрации устройств в Azure AD на портале Azure.

Устройства Windows обнаруживают службу по известным записям DNS. Вам необходимо настроить записи DNS организации так, чтобы устройства с Windows 7 и Windows 8.1 могли обнаруживать и использовать службу.

Просмотреть и включить или выключить зарегистрированные устройства можно с помощью портала администрирования в Azure Active Directory.

## Включение регистрации устройств в Azure Active Directory
В следующем разделе описывается, как включить службу регистрации устройств Azure Active Directory для каталога.
Включение службы регистрации устройств Azure Active Directory
-------------------------------------------------------------
1. Войдите на портал Azure как администратор.
1. На панели слева выберите **Active Directory**.
1. На вкладке **Каталог** выберите требуемый каталог.
1. Перейдите на вкладку **Настройка**.
1. Перейдите к разделу **Устройства**.
1. Выберите значение **ВСЕ** для пункта **ПОЛЬЗОВАТЕЛИ МОГУТ ПРИСОЕДИНИТЬ УСТРОЙСТВА К РАБОЧЕЙ ОБЛАСТИ**.
1. Выберите максимальное число устройств, которое требуется авторизовать для одного пользователя.

>[AZURE.NOTE]Для регистрации в Microsoft Intune или службе управления мобильными устройствами для Office 365 требуется присоединение к рабочей области. Если вы настроили одну из этих служб, будет выбран пункт «ВСЕ», а кнопка «НЕТ» отключена.


По умолчанию двухфакторная проверка подлинности для службы не включена. Однако ее рекомендуется использовать при регистрации устройства.

* Прежде чем делать обязательной двухфакторную проверку подлинности для службы, необходимо настроить поставщик двухфакторной проверки подлинности в Azure Active Directory, а также настроить учетные записи пользователей для работы с Multi-Factor Authentication. См. раздел «Использование Multi-Factor Authentication со службами федерации Active Directory».

* Если вы используете службы федерации Active Directory с Windows Server 2012 R2, в них необходимо настроить модуль двухфакторной проверки подлинности. См. раздел «Использование Multi-Factor Authentication со службами федерации Active Directory».

## Настройка обнаружения службы регистрации устройств Azure Active Directory
Устройства с Windows 7 и Windows 8.1 будут пытаться обнаружить сервер регистрации устройств, объединяя имя учетной записи пользователя с хорошо известным именем сервера регистрации устройств. Вам нужно создать запись DNS CNAME, которая указывает на запись A, связанную с вашей службой регистрации устройств Azure Active Directory. Запись CNAME должна использовать хорошо известный префикс enterpriseregistration, за которым следует суффикс имени участника-пользователя, используемый учетными записями пользователей в вашей организации. Если ваша организация использует несколько суффиксов имени участника-пользователя, следует создать несколько записей CNAME в DNS.

Например, если в вашей организации используется два суффикса имени участника-пользователя, @contoso.com и @region.contoso.com, вы создадите следующие записи DNS.
 
| Запись | Тип | Адрес |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com | CNAME | enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com | CNAME | enterpriseregistration.windows.net |

## Просмотр объектов устройств в Azure Active Directory и управление ими
1. На портале администрирования Azure можно просматривать, блокировать и разблокировать устройства. Заблокированное устройство лишается доступа к приложениям, которые настроены для доступа только с зарегистрированных устройств.
1. Войдите на портал Microsoft Azure как администратор.
1. На панели слева выберите **Active Directory**.
1. Выберите свой каталог.
1. Перейдите на вкладку **Пользователи**. Затем выберите пользователя, устройства которого хотите просмотреть.
1. Выберите вкладку **Устройства**.
1. Выберите **Зарегистрированные устройства** из раскрывающегося меню.
1. Теперь вы можете просмотреть, заблокировать или разблокировать зарегистрированные устройства пользователя. 

## Дополнительные разделы

Регистрировать присоединенные к домену устройства Windows 7 и Windows 8.1 можно с помощью службы регистрации устройств Azure AD. В следующем разделе приводятся дополнительные сведения о необходимых условиях и действиях для настройки регистрации устройств на устройствах Windows 7 и Windows 8.1.

- [Автоматическая регистрация в Azure Active Directory присоединенных к домену устройств Windows](active-directory-conditional-access-automatic-device-registration.md) 
- [Настройка автоматической регистрации присоединенных к домену устройств Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [Настройка автоматической регистрации присоединенных к домену устройств Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows8_1.md)

<!---HONumber=AcomDC_1203_2015-->