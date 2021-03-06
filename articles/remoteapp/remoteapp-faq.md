<properties 
	pageTitle="Часто задаваемые вопросы об Azure RemoteApp | Microsoft Azure" 
	description="Получите ответы на часто задаваемые вопросы об Azure RemoteApp." 
	services="remoteapp" 
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="remoteapp" 
	ms.workload="compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/25/2016" 
	ms.author="elizapo"/>

# Azure RemoteApp: вопросы и ответы
Ниже приведены вопросы, которые нам задавали о Azure RemoteApp. У вас есть другие вопросы? Посетите [форумы по RemoteApp](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp) и задайте нам интересующий вас вопрос или оставьте комментарий ниже.

## Что такое Azure RemoteApp? ##


- **Что такое Azure RemoteApp?** RemoteApp — это служба Azure, которая помогает предоставлять защищенный удаленный доступ к приложениям со многих пользовательских устройств. Узнайте больше об [Azure RemoteApp](remoteapp-whatis.md).
- **Какие варианты развертывания доступны?** Существует два типа коллекций RemoteApp: облачные и гибридные. Выбор необходимого типа зависит от ряда факторов, например от того, нужна ли вам возможность присоединения к домену. Все возможные варианты описаны [здесь](remoteapp-collections.md).

## Советы по использованию Azure RemoteApp ##
- **Через какой промежуток времени происходит отключение? Сколько может длиться простой, прежде чем будет выполнен автоматический выход?** 4 часа. Вы автоматически выйдете из Azure RemoteApp, если вы или ваши пользователи будете неактивны 4 часа подряд. Сведения о других параметрах по умолчанию см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md).
- **Можно ли попробовать эту службу бесплатно?** Да. Бесплатная пробная версия доступна в течение 30 дней. По окончании пробного периода вы можете перейти на платную учетную запись (которую можно использовать в рабочей среде) или перестать использовать службу. Начните использовать бесплатную пробную версию, посетив сайт [portal.azure.com](http://portal.azure.com), где можно создать новый экземпляр RemoteApp. Бесплатная пробная версия позволяет создать 2 экземпляра RemoteApp с 10 пользователями на экземпляр. Помните, что пробный период составляет 30 дней.
## Сведения о подписке Azure RemoteApp ##

- **Каковы ограничения службы?** Сведения о параметрах по умолчанию и ограничениях службы Azure RemoteApp см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md). Сообщите нам, если у вас есть дополнительные вопросы.
- **Сколько должно быть пользователей?** Минимум 20 пользователей. Еще раз для ясности: МИНИМУМ 20. Счет будет выставляться за 20 пользователей. 
- **Сколько стоит RemoteApp?** Ознакомьтесь с [ценами на Azure RemoteApp](../../../pricing/details/remoteapp/).
- **Может ли один тип коллекции стоить больше, чем другой?** Да, может. Это зависит от требований к коллекции. Для гибридной коллекции требуется подключение Azure RemoteApp к вашей локальной сети. Если вы используете существующую виртуальную сеть или Express Route, то вам не придется нести дополнительные расходы. Однако если вы используете новую виртуальную сеть Azure и шлюз или Express Route, то вам придется платить за [VPN-шлюз](../../../pricing/details/vpn-gateway) или [Express Route](../../../pricing/details/expressroute/). Эти расходы (подробно описанные по ссылкам) необходимо нести дополнительно к ежемесячным расходам на Azure RemoteApp.

## Коллекции: что поддерживается, какие коллекции следует использовать и другие вопросы
- **Поддерживаются ли настраиваемые бизнес-приложения?** Да. Чтобы использовать настраиваемое приложение в Azure RemoteApp, создайте [настраиваемый образ шаблона](remoteapp-create-custom-image.md) и отправьте его в коллекцию RemoteApp.
- **Будут ли настраиваемые бизнес-приложения работать в Azure RemoteApp?** Лучший способ узнать это — протестировать приложения. Изучите [требования к совместимости приложений](http://www.microsoft.com/download/details.aspx?id=18704) и посетите [Центр совместимости удаленных рабочих столов](http://www.rdcompatibility.com/compatibility/default.aspx).
- **Какой метод развертывания (облачное или гибридное) лучше подходит для моей организации?** Гибридные коллекции обеспечивают наиболее полные возможности, если вам необходима полная интеграция с единым входом (SSO) и защищенным подключением к локальной сети. Облачные коллекции предоставляют гибкий и простой способ изолировать развертывание с использованием нескольких методов проверки подлинности. Узнайте больше о [вариантах развертывания](remoteapp-whatis.md).
- **У нас есть база данных SQL или другая база данных в локальной среде или Azure. Какой тип развертывания следует использовать?** Это зависит от расположения базы данных SQL или тыловой базы данных. Если база данных находится в частной сети, используйте гибридную коллекцию. Если база данных доступна из Интернета и к ней могут подключаться клиенты, вы можете использовать облачную коллекцию.
- **Поддерживаются ли сопоставление дисков, USB и последовательный порт, совместное использование буфера обмена и перенаправление принтеров?** Azure RemoteApp поддерживает все эти функции. Совместное использование буфера обмена и перенаправление принтеров включены по умолчанию. Дополнительные сведения о перенаправлении можно получить [здесь](remoteapp-redirection.md). 


## Образы шаблонов
- **Можно ли использовать облако или имеющуюся виртуальную машину в качестве шаблона коллекции RemoteApp?** Да! Вы можете создать образ на основе виртуальной машины Azure, использовать один из образов, включенных в подписку, или создать настраиваемый образ. Ознакомьтесь с [типами образов RemoteApp](remoteapp-imageoptions.md).


## Параметры сети
- **Для гибридной коллекции необходима виртуальная сеть. Можно ли использовать имеющуюся виртуальную сеть?** Можно, если это виртуальная сеть Azure. Дополнительные сведения см. в разделе "Шаг 1. Настройка виртуальной сети" в [инструкциях по гибридным коллекциям](remoteapp-create-hybrid-deployment.md).
- **Можно ли использовать виртуальную сеть вместе с облачной коллекцией?** Можно. Дополнительные сведения об этом см. в статье [Создание облачной коллекции](remoteapp-create-cloud-deployment.md). Особое внимание обратите на раздел "Шаг 1".

## Параметры проверки подлинности



- **Как насчет проверки подлинности? Какие методы поддерживаются?** Облачная коллекция поддерживает учетные записи Майкрософт и Azure Active Directory, которые также являются учетными записями Office 365. Гибридная коллекция поддерживает только учетные записи Azure Active Directory, которые синхронизированы (например, с помощью средства синхронизации [Microsoft Azure Active Directory](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)) из развертывания Windows Server Active Directory. В частности, они могут быть синхронизированы с помощью параметра "Синхронизация паролей" или служб федерации Active Directory (AD FS). Вы также можете настроить службу [Multi-Factor Authentication (MFA)](../../services/multi-factor-authentication/).

>[AZURE.NOTE]Пользователи Azure Active Directory должны относиться к клиенту, связанному с вашей подпиской. (Вы можете просмотреть и изменить связанную подписку на вкладке **Параметры** на портале. Дополнительные сведения см. в [статье, посвященной изменению клиента Azure Active Directory, используемого в RemoteApp](remoteapp-changetenant.md).)

- **Почему не удается предоставить доступ к учетной записи Azure Active Directory?** Пользователи Azure Active Directory должны относиться к каталогу, связанному с вашей подпиской. Вы можете просматривать и изменять этот каталог на вкладке "Параметры" на портале. Дополнительные сведения см. в [статье об изменении клиента Azure Active Directory, используемого службой RemoteApp.](remoteapp-changetenant.md)

## Клиенты: какие устройства можно использовать для доступа к Azure RemoteApp?
Подробные сведения о клиентах, в том числе инструкции по установке различных клиентов, см. в статье [Доступ к приложениям в Azure RemoteApp](remoteapp-clients.md).

- **Какие устройства или операционные системы поддерживаются клиентскими приложениями?** Во-первых, это компьютеры и планшеты. 
	- Windows 10 (предварительная версия клиента)
	- Windows 8.1 и Windows 8
	- Windows 7 с пакетом обновления 1
	- Mac OS X
	- Windows RT
	- Планшеты под управлением Android
	- Устройства iPad и телефоны.
	- iPhone
	- Телефоны под управлением Android
	- Windows Phone
 
	[Скачайте](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) клиент RemoteApp.
- **Поддерживает ли Azure RemoteApp тонкие клиенты?** Да, поддерживаются следующие тонкие клиенты Windows Embedded.
	- Windows Embedded Standard 7
	- Windows Embedded 8 Standard
	- Windows Embedded 8.1 Industry Pro
	- Windows 10 IoT Enterprise

- **Какая версия Windows Server поддерживается для узла сеансов удаленных рабочих столов?** Windows Server 2012 R2.

##Поддержка и обратная связь


- **Какой план поддержки предусматривается для RemoteApp?** Поддержка по выставлению счетов и управлению подпиской предоставляется бесплатно. Техническая поддержка доступна по [планам обслуживания Azure](../../../support/plans/). Вы также можете получить поддержку от сообщества на [форуме Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp). 
- **Как отправить отзыв?** Посетите [форум отзывов](https://feedback.azure.com/forums/247748-azure-remoteapp/).
- **От кого можно узнать больше об Azure RemoteApp?** Помимо [форума](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp), который отлично подходит, чтобы задавать вопросы, вы можете присоединиться к еженедельному [вебинару "Спросите экспертов"](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html), где мы обсуждаем любые темы, связанные с RemoteApp.
- **Существует ли документация по RemoteApp?** Хорошо, что вы спросили. Помимо справочных материалов в соответствующем разделе портала (достаточно щелкнуть значок **?** на любой странице портала), доступны следующие статьи, посвященные RemoteApp.
	- **Начало работы.**
		- [Что такое RemoteApp?](remoteapp-whatis.md)
		- [Что входит в образы шаблонов RemoteApp?](remoteapp-images.md)
		- [Как работает лицензирование?](remoteapp-licensing.md)
		- [Как RemoteApp и Office работают вместе?](remoteapp-o365.md)
		- [Как работает перенаправление в RemoteApp](remoteapp-redirection.md)?
	- **Развертывание.**
		- [Создание настраиваемого образа шаблона](remoteapp-create-custom-image.md)
		- [Создание гибридной коллекции](remoteapp-create-hybrid-deployment.md)
		- [Создание облачной коллекции](remoteapp-create-cloud-deployment.md)
		- [Настройка Azure Active Directory для RemoteApp](remoteapp-ad.md)
		- [Публикация приложения в RemoteApp](remoteapp-publish.md)
	- **Управление.**
		- [Добавление пользователей](remoteapp-user.md)
		- [Рекомендации по настройке и использованию RemoteApp](remoteapp-bestpractices.md)	

	Видео! У нас также есть несколько видеороликов, посвященных RemoteApp. Некоторые из них содержат вводные сведения ([Введение в Azure RemoteApp](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)), а другие посвящены развертыванию ([Облачное развертывание](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) и [Гибридное развертывание](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Смотрите сами!

 
### Помогите нам помочь вам 
Вы знаете, что можете не только оценивать эту статью и оставлять комментарии ниже, но и вносить в нее изменения? Чего-то не хватает? Что-то неправильно? Что-то изложено непонятно? Чтобы внести изменения, прокрутите страницу вверх и щелкните **Изменить на GitHub**. Мы просмотрим ваши предложения, и, когда мы их одобрим, вы увидите изменения и улучшения здесь.

<!---HONumber=AcomDC_0302_2016-->