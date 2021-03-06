<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с Evidence.com | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в приложении Evidence.com."
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="asmalser"/>


# Руководство. Интеграция Azure Active Directory с Evidence.com

Это руководство продемонстрирует, как настроить единый вход между Azure Active Directory (AAD) и Evidence.com. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:
	
* действующая подписка Microsoft Azure;
* подписка Evidence.com с включенной функцией единого входа (если единый вход на основе SAML не активирован, отправьте запрос по электронной почте на адрес earlyaccess@evidence.com).

После выполнения этого руководства пользователи Azure Active Directory, которых вы назначите для Evidence.com, смогут выполнять единый вход в приложение с помощью панели доступа AAD.

## Добавление Evidence.com в каталог

В этом разделе описано, как добавить Evidence.com в Azure Active Directory в качестве интегрированного приложения.

**Чтобы включить интеграцию приложений для Evidence.com, выполните следующие действия.**

1.	На [классическом портале Azure](https://manage.windowsazure.com) в области навигации слева щелкните **Active Directory**.

2.	Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3.	Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

4.	Нажмите **Добавить приложение**, чтобы открыть коллекцию приложений, а затем щелкните **Добавить приложение из коллекции**.

5.	В поле поиска введите **Evidence.com**.

6.	В области результатов выберите **Evidence.com** и нажмите кнопку **Завершить**, чтобы добавить приложение.


## Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Evidence.com с помощью своей учетной записи Azure Active Directory, используя федерацию на основе протокола SAML.

**Чтобы настроить единый вход, выполните следующие действия:**

1.	Добавив Evidence.com на портале управления Azure, нажмите кнопку **Настроить единый вход**. 
 
2.	На следующей странице выберите **Единый вход Azure AD**, а затем нажмите кнопку **Далее**.

3.	На экране «Настройка URL-адреса приложения» введите URL-адрес, по которому пользователи будут входить в клиент Evidence.com (например, https://yourtenant.evidence.com), а затем нажмите кнопку **Далее**.

4.	Щелкните ссылку **Загрузить сертификат** и сохраните сертификат на локальный диск. Этот сертификат и URL-адреса метаданных (идентификатор сущности, URL-адрес единого входа и URL-адрес выхода) понадобятся для настройки единого входа на веб-узле Evidence.com.

5.	В отдельном окне браузера войдите в клиент Evidence.com с правами администратора и перейдите к вкладке **Admin** (Администрирование).
      
6.	Щелкните **Agency Single Sign On** (Единый вход в агентство).
 
7.	Выберите параметр **SAML Based Single Sign On** (Единый вход на основе SAML).
 
8.	Скопируйте значения **URL-адреса издателя**, **единого входа** и **единого выхода**, которые указаны на классическом портале Azure, и вставьте их в соответствующие поля в интерфейсе Evidence.com.

9.	Откройте сертификат, который вы загрузили на шаге 4, в текстовом редакторе, например Notepad.exe. Скопируйте содержимое файла в поле **Security Certificate** (сертификат безопасности).

10. Сохраните конфигурацию в Evidence.com.
 
11.	На классическом портале Azure установите флажок **Подтвердите, что вы настроили единый вход, как описано выше**. Когда вы установите этот флажок, текущий сертификат начнет действовать для этого приложения.
 
12.	На странице подтверждения единого входа нажмите кнопку **Завершить**.


## Создание тестового пользователя Evidence.com

Чтобы пользователи Azure AD могли выполнять вход, нужно подготовить их для доступа в приложении Evidence.com. В этом разделе описано, как создавать учетные записи пользователей Azure AD в Evidence.com.

**Чтобы подготовить учетную запись пользователя в Evidence.com, выполните следующие действия.**

1.	В окне веб-браузера войдите на корпоративный веб-сайт Evidence.com в качестве администратора.

2.	Перейдите к вкладке **Admin** (Администрирование).

3.	Щелкните **Add User** (Добавить пользователя).

4.	Нажмите кнопку **Add** (Добавить).

5.  Параметр **Email Address** (Адрес электронной почты) для нового пользователя должен совпадать с именем пользователя в Azure AD, которому вы хотите предоставить доступ. Возможно, в вашей организации в качестве имени пользователя не используется адрес электронной почты. Тогда зайдите на классическом портале Azure в раздел **Evidence.com > Атрибуты > Единый вход** и измените параметр nameidenitifer (идентификатор имени, отправляемый приложению Evidence.com) так, чтобы передавался именно адрес электронной почты.


## Назначение пользователей для Evidence.com

Чтобы подготовленные пользователи AAD увидели Evidence.com на своей панели доступа, им нужно назначить доступ на портале управления Azure.

**Чтобы назначить пользователей для Evidence.com, выполните следующие действия.**

1.	На странице быстрого запуска для Evidence.com на классическом портале Azure щелкните **Назначить пользователей для Evidence.com**.
 
2.	В меню **Показать** выберите, как вы будете назначать доступ в Evidence.com — пользователю или группе, и нажмите кнопку с галочкой.
 
3.	В списке **Пользователи** выберите пользователей, которых нужно включить в группу имеющих доступ к Evidence.com.
 
4.	В нижнем колонтитуле страницы нажмите кнопку **Назначить**.

<!---HONumber=AcomDC_0224_2016-->