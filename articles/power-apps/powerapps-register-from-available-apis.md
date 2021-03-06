<properties
	pageTitle="Создание и настройка API, управляемых корпорацией Майкрософт, и API, управляемых ИТ-службами, в PowerApps Enterprise | Microsoft Azure"
	description="Дополнительные сведения об API, доступных в PowerApps, и их регистрации на портале Azure"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="01/21/2016"
   ms.author="guayan"/>

# Регистрация API, управляемого корпорацией Майкрософт, или API, управляемого ИТ-службой
Существуют API, **управляемые корпорацией Майкрософт**, и API, **управляемые ИТ-службой**. При включении PowerApps Enterprise API, управляемые корпорацией Майкрософт, автоматически становятся доступными. Управление памятью, подключениями, доверием и многими другими параметрами осуществляется автоматически. В качестве следующего шага введите пользовательские параметры, такие как учетная запись в Twitter и пароль.

API, управляемые ИТ-службами, позволяют контролировать и отслеживать все параметры, включая память, подключение, доверие и многие другие. Помимо прочего, к API, управляемым ИТ-службами, относятся API, способные подключаться к локальной системе, такой как SQL Server и SharePoint Server.

Для использования API, **управляемых корпорацией Майкрософт** или **ИТ-службами**, необходимо зарегистрировать API на портале Azure. После регистрации вы сможете использовать API в своих приложениях. Доступны следующие параметры:

- Регистрация предварительно созданных API, управляемых корпорацией Майкрософт, и API, управляемых ИТ-службой.
- Регистрация веб-приложения, приложения API или мобильного приложения, размешенного в [среде службы приложений](powerapps-register-api-hosted-in-app-service.md).
- Регистрация собственного API Swagger с помощью [определения API Swagger 2.0](powerapps-register-existing-api-from-api-definition.md).

В этой статье рассматривается **регистрация предварительно созданных API, управляемых корпорацией Майкрософт, и API, управляемых ИТ-службами**.

#### Предварительные требования для начала работы

- Регистрация в службе [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Создание [среды службы приложений](powerapps-get-started-azure-portal.md).


## Просмотр доступных API, управляемых корпорацией Майкрософт
API, **управляемые корпорацией Майкрософт**, предоставляются вместе с PowerApps Enterprise и также размещаются корпорацией Майкрософт. Во многих сценариях API, управляемые корпорацией Майкрософт, идеально подходят для ваших приложений. Например, если приложение отправляет твит, загружает файл в OneDrive или отображает данные из файла Excel, API, управляемые корпорацией Майкрософт, станут хорошим выбором.

В число дополнительных преимуществ входят следующие.

- Вы получаете все API, управляемые корпорацией Майкрософт, и можете зарегистрировать собственный экземпляр. 
- Ресурсы, включая сети, память или конфигурации безопасности, отслеживаются автоматически. Например, если для отображения данных Excel в приложении требуется больше памяти, она будет добавлена автоматически. 
- Отношение доверия между приложением и API, например между Office и Twitter, создается автоматически. 


#### API, управляемые корпорацией Майкрософт

API | Описание | Ссылка на инструкции
--- | --- | ---
![][31] | **Dropbox**<br/><br/> Получение, обновление, удаление элементов и многое другое. | [**Приступая к работе**](powerapps-create-api-dropbox.md)
![][32] | **DynamicsCRM Online**<br/><br/> Получение, обновление, удаление элементов и многое другое. | [**Приступая к работе**](powerapps-create-api-crmonline.md)
![][33] | **Excel**<br/><br/> Получение, обновление, удаление элементов и многое другое. | [**Приступая к работе**](powerapps-create-api-excel.md)
![][34] | **Google Диск**<br/><br/> Получение, обновление, удаление элементов и многое другое. | [**Приступая к работе**](powerapps-create-api-googledrive.md)
![][35] | **Microsoft Translator**<br/><br/> Перевод текста, определение языков и многое другое. | [**Приступая к работе**](powerapps-create-api-microsofttranslator.md)
![][36] | **Office 365 Outlook**<br/><br/> Управление электронной почтой. | [**Приступая к работе**](powerapps-create-api-office365-outlook.md)
![][37] | **Пользователи Office 365**<br/><br/> Доступ к профилям пользователей, их руководителям, отчетам и многое другое. | [**Приступая к работе**](powerapps-create-api-office365-users.md)
![][38] | **OneDrive**<br/><br/> Получение, обновление, удаление элементов и многое другое. | [**Приступая к работе**](powerapps-create-api-onedrive.md)
![][39] | **Salesforce**<br/><br/> Получение, обновление, удаление элементов и многое другое. | [**Приступая к работе**](powerapps-create-api-salesforce.md)
![][40] | **SharePoint Online**<br/><br/> Получение, обновление, удаление элементов и многое другое. | [**Приступая к работе**](powerapps-create-api-sharepointonline.md)
![][43] | **Twitter**<br/><br/> Отправка и поиск твитов, просмотр подписчиков и многое другое. | [**Приступая к работе**](powerapps-create-api-twitter.md)


## Просмотр доступных API, управляемых ИТ-службами
API, **управляемые ИТ-службами**, находятся под вашим контролем и управлением. Они не выполняются в среде, управляемой корпорацией Майкрософт. В некоторых случаях для нужд приложений подходит использование API в вашей собственной среде, управляемой ИТ-службой. Допустим, ваше приложение использует API Twitter и вместо ключа Майкрософт вам нужно указать ключ вашей организации для доступа к Twitter. В этом случае лучше настроить API Twitter как API, управляемый ИТ-службой. В другом примере приложение использует API SQL Server для подключения к локальной базе данных. В среде, управляемой ИТ-службой, вы можете настроить виртуальную сеть или использовать Express Route для подключения к локальной системе. Выбор за вами.

В число дополнительных преимуществ входят следующие.

- Ресурсы, включая сеть, память и конфигурации безопасности, отслеживаются вами. Например, если для отображения данных Excel в приложении требуется больше памяти, вы можете указать, сколько памяти нужно добавить в среду. 
- Вы сами настраиваете отношения доверия между приложениями и API и управляете их безопасностью. Например, вы можете настроить API Office 365 как API, управляемый корпорацией Майкрософт (выбрать автоматическое отношение доверия), или использовать API Office 365 из вашей собственной среды (создать собственное отношение доверия). 
- Кроме того, **все** API, управляемые корпорацией Майкрософт, могут управляться ИТ-службами. Например, при желании вы можете создать свой собственный экземпляр Office 365 и получить над ним полный контроль. После этого API Office 365, управляемый ИТ-службой, и API Office 365, управляемый корпорацией Майкрософт, можно будет использовать в одной и той же среде. Все зависит от потребностей вашего приложения.
- При подключении к локальным системам или использовании API поиска Bing безопасностью, проверкой подлинности, лицензированием и т. д. вы управляете сами.


#### API, управляемые ИТ-службами
> [AZURE.NOTE] Помните, что **все** API, управляемые корпорацией Майкрософт, могут, помимо этого, управляться ИТ-службами. Перечисленные ниже API управляются только ИТ-службами и не могут управляться корпорацией Майкрософт.

API | Описание | Ссылка на инструкции
--- | --- | ---
![][30] | **Поиск Bing**<br/><br/>Внедрение результатов поиска, добавление функции поиска и многое другое. | [**Приступая к работе**](powerapps-create-api-bingsearch.md)
![][42] | **SQL Server**<br/><br/>Получение, обновление, удаление элементов и многое другое. | [**Приступая к работе**](powerapps-create-api-sqlserver.md)
![][41] | **SharePoint Server**<br/><br/>Получение, обновление, удаление элементов и многое другое. | [**Приступая к работе**](powerapps-create-api-sharepointserver.md)


#### Зачем регистрировать собственные экземпляры

Использовать готовые API, управляемые корпорацией Майкрософт, очень удобно. С другой стороны, регистрация собственных экземпляров как API, управляемых ИТ-службами, обладает множеством преимуществ. На высоком уровне рекомендуем создавать API, управляемые ИТ-службами, если вам нужно следующее.

- Полное управление API, включая управление доступом пользователей, безопасность при подключении к другим системам, ограничение на число вызовов API, мониторинг и дополнительные функции, такие как политики, и многое другое.
- Доступ к локальным данным, поскольку среда службы приложений поддерживает виртуальные сети.
- Настройка для бизнес-пользователей API, который они не могут использовать напрямую.

В представленной ниже таблице сравниваются возможности API, управляемых корпорацией Майкрософт, и API, управляемых ИТ-службами.

| Функция | Управляется корпорацией Майкрософт | Управляется ИТ-службами |
| ---------- | ----------------- | ------------ |
| Ограничение на число вызова API | Определяется корпорацией Майкрософт | Определяется вами (с помощью политик) |
| Подключение к SaaS с использованием собственного ключа | Не поддерживается | Поддерживаются |
| Доступ пользователей к API | Включен для всех | Полное управление на уровне пользователей и групп AAD |
| Мониторинг API | Не поддерживается | Поддерживаются |
| Политики API | Не поддерживается | Поддерживаются |
| Доступ пользователей к подключению | Только просмотр | Полное управление на уровне пользователей и групп AAD |
| Управление подключениями | Только просмотр | Полноценное управление |


## Регистрация API, управляемых корпорацией Майкрософт, или API, управляемых ИТ-службой

1. Войдите на [портал Azure](https://portal.azure.com/) с помощью рабочей учетной записи (*имя\_пользователя*@*компания*.com). Будет выполнен автоматический вход в подписку вашей компании.
2. Выберите **Обзор**, **PowerApps**, а затем выберите **Управление API**:  
![][17]
3. В разделе "Управление API" щелкните **Добавить**:  
![][18]  
4. В разделе **Добавление API** введите свойства API:  

	- В поле **Имя** введите имя API. Обратите внимание на то, что введенное имя включается в URL-адрес API среды выполнения. Оно должно быть понятным и уникальным в пределах вашей организации.
	- В поле **Источник** выберите параметр **Из доступных API**:  
	![][19]
5. Выберите **API**, а затем API, который нужно зарегистрировать:  
![][20]
6. Выберите определенный API и добавьте настраиваемые свойства.
7. Нажмите кнопку **ДОБАВИТЬ**, чтобы завершить эти действия.

> [AZURE.TIP] API регистрируется в среде службы приложений. После этого API сможет использоваться другими приложениями, которые находятся в той же среде службы приложений.


## Сводка и дальнейшие действия

В этом разделе было показано, как зарегистрировать собственный экземпляр доступных API, включенных в PowerApps. Ниже приведены некоторые связанные разделы и ресурсы для подробного изучения PowerApps.


- [Настройка свойств API](powerapps-configure-apis.md)
- [Предоставление пользователям доступа к API](powerapps-manage-api-connection-user-access.md)
- [Создание собственных приложений PowerApps](https://powerapps.microsoft.com/tutorials/)


<!--References-->

[17]: ./media/powerapps-register-from-available-apis/registered-apis-part.png
[18]: ./media/powerapps-register-from-available-apis/add-api-button.png
[19]: ./media/powerapps-register-from-available-apis/add-api-blade.png
[20]: ./media/powerapps-register-from-available-apis/add-api-select-from-marketplace-blade.png
[30]: ./media/powerapps-register-from-available-apis/bingsearch.png
[31]: ./media/powerapps-register-from-available-apis/dropbox.png
[32]: ./media/powerapps-register-from-available-apis/dynamicscrmonline.png
[33]: ./media/powerapps-register-from-available-apis/excel.png
[34]: ./media/powerapps-register-from-available-apis/googledrive.png
[35]: ./media/powerapps-register-from-available-apis/microsofttranslator.png
[36]: ./media/powerapps-register-from-available-apis/office365outlook.png
[37]: ./media/powerapps-register-from-available-apis/office365users.png
[38]: ./media/powerapps-register-from-available-apis/onedrive.png
[39]: ./media/powerapps-register-from-available-apis/salesforce.png
[40]: ./media/powerapps-register-from-available-apis/sharepointonline.png
[41]: ./media/powerapps-register-from-available-apis/sharepointserver.png
[42]: ./media/powerapps-register-from-available-apis/sqlserver.png
[43]: ./media/powerapps-register-from-available-apis/twitter.png

<!----HONumber=AcomDC_0224_2016-->
