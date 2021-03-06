<properties 
	pageTitle="Приступая к работе со службой Azure Multi-Factor Authentication" 
	description="Выберите подходящее решение многофакторной проверки подлинности, выяснив, что является объектом защиты и где находятся пользователи. После этого выберите облако, сервер Multi-Factor Authentication или службы AD FS." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="03/03/2016" 
	ms.author="billmath"/>

#Выбор решения многофакторной безопасности

Так как существует несколько вариантов использования Azure Multi-Factor Authentication, наиболее подходящая версия определяется на основе нескольких факторов. Вот эти факторы:

-	[Что я пытаюсь защитить?](#what-am-i-trying-to-secure)
-	[Где находятся пользователи?](#where-are-the-users-located)

В разделах этой статьи представлены рекомендации по определению каждого из них.

## Что я пытаюсь защитить

Чтобы выяснить, какое решение многофакторной проверки подлинности вам нужно, сначала определите, что вы пытаетесь защитить с помощью второго метода проверки подлинности. Это приложение в службе Azure? Или это система удаленного доступа, к примеру? Определив объект, который необходимо защитить, мы четко увидим, где именно нужно активировать многофакторную проверку подлинности.


Что вы пытаетесь защитить| Multi-Factor Authentication в облаке|Сервер Multi-Factor Authentication 
------------- | :-------------: | :-------------: |
Собственные приложения корпорации Майкрософт|* |* |
Приложения SaaS в коллекции приложений|* |* |
Приложения IIS, опубликованные через прокси приложения Azure AD|* |* |
Приложения IIS, опубликованные не через прокси приложения Azure AD | |* |
Удаленный доступ, например VPN или RDG| |* |



## Где находятся пользователи

На основе информации о местонахождении пользователей можно определить, какое решение нам нужно: облачная многофакторная проверка подлинности или локальная с использованием сервера Multi-Factor Authentication.



Местонахождение пользователей| Решение
------------- | :------------- | 
Azure Active Directory| Multi-Factor Authentication в облаке|
Azure AD и локальная служба AD с использованием федерации в AD FS| Доступны оба варианта — Multi-Factor Authentication в облаке и сервер Multi-Factor Authentication 
Azure AD и локальная служба AD с использованием DirSync, Azure AD Sync, Azure AD Connect без синхронизации паролей|Доступны оба варианта — Multi-Factor Authentication в облаке и сервер Multi-Factor Authentication 
Azure AD и локальная служба AD с использованием DirSync, Azure AD Sync, Azure AD Connect с синхронизацией паролей|Multi-Factor Authentication в облаке
Локальная служба Active Directory|Сервер Multi-Factor Authentication

В приведенной ниже таблице сравниваются возможности службы Multi-Factor Authentication в облаке и сервера Multi-Factor Authentication.

 | Multi-Factor Authentication в облаке | Сервер Multi-Factor Authentication
------------- | :-------------: | :-------------: |
Уведомление от мобильного приложения в качестве второго фактора | ● | ● |
Код подтверждения мобильного приложения в качестве второго фактора | ● | ●
Телефонный вызов в качестве второго фактора | ● | ● 
Одностороннее SMS в качестве второго фактора | ● | ●
Двустороннее SMS в качестве второго фактора | | ● 
Маркеры оборудования в качестве второго фактора | | ● 
Пароли приложений для клиентов, не поддерживающих Multi-Factor Authentication | ● |  
Администраторский контроль над методами проверки подлинности | (Общедоступная предварительная версия) | ● 
Режим ПИН-кода | | ●
Предупреждение о мошенничестве | ● | ●
Отчеты Multi-Factor Authentication | ● | ● 
Разовый обход | | ● 
Настраиваемые приветствия для телефонных вызовов | ● | ● 
Настройка идентификатора вызывающей стороны для телефонных звонков | ● | ● 
Надежные IP-адреса | ● | ● 
Запоминание данных MFA для доверенных устройств (предварительная версия) | ● |  
Условный доступ | ● | ● 
Кэш | | ● 

Определившись с вариантом (облачная многофакторная проверка подлинности или локальный сервер Multi-Factor Authentication), можно начинать настройку и использование службы Azure Multi-Factor Authentication. **Выберите значок, соответствующий вашему сценарию.**

<center> [![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md) &#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &#160;&#160;&#160;&#160;&#160; </center>

<!---HONumber=AcomDC_0309_2016-->