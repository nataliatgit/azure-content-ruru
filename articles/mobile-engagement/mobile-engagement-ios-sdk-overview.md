<properties
	pageTitle="Обзор пакета SDK для рекламных кампаний в iOS для Azure Mobile Engagement"
	description="Последние обновления и указания для пакета SDK для iOS для Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="MehrdadMzfr" />

#Пакет SDK для Azure Mobile Engagement (iOS)

Начните с этой статьи, чтобы получить подробную информацию об интеграции Azure Mobile Engagement в приложение iOS. Если вы хотите потренироваться, обязательно пройдите наш [15-минутный учебник](mobile-engagement-ios-get-started.md).

Щелкните, чтобы просмотреть [содержимое пакета SDK](mobile-engagement-ios-sdk-content.md).

##Процедуры по интеграции
1. Начните с этого раздела: [Как интегрировать Mobile Engagement в приложение iOS](mobile-engagement-ios-integrate-engagement.md)

2. Сведения об уведомлениях: [Как интегрировать Reach (Notifications) в приложение iOS](mobile-engagement-ios-integrate-engagement-reach.md)

3. Реализация плана добавления тегов: [Как использовать API для расширенного добавления тегов Mobile Engagement в приложении iOS](mobile-engagement-ios-use-engagement-api.md)


##Заметки о выпуске

###3\.2.1 (11.12.2015)

-   Фиксированная задержка при активации нового экземпляра приложения уведомлением с прямыми ссылками. 

Информацию о предыдущих версиях см. в [полных заметках о выпуске](mobile-engagement-ios-release-notes.md).

##Процедуры обновления

Если вы уже интегрировали в приложение старую версию службы Engagement, при обновлении пакета SDK необходимо учитывать следующее.

Если вы пропустили несколько версий пакета SDK, вам понадобиться выполнить ряд процедур. См. полную версию статьи [Процедуры обновления](mobile-engagement-ios-upgrade-procedure.md).

Для каждой новой версии пакета SDK необходимо сначала заменить (удалить и повторно импортировать в проект Хcode) папки EngagementSDK и EngagementReach.

###Из версии 2.0.0 в 3.0.0
Прекращена поддержка iOS 4.X. Начиная с этой версии, целевое устройство для развертывания приложения должно быть с версией не ниже iOS 6.

Если в приложении используется обработка рекламных кампаний, необходимо добавить значение `remote-notification` в массив `UIBackgroundModes` в файле Info.plist для получения удаленных уведомлений.

Метод `application:didReceiveRemoteNotification:` необходимо заменить на `application:didReceiveRemoteNotification:fetchCompletionHandler:` в делегате приложения.

"AEPushDelegate.h" — нерекомендуемый интерфейс, поэтому все ссылки на него необходимо удалить. Это, в частности, подразумевает удаление `[[EngagementAgent shared] setPushDelegate:self]` и методов делегата в делегате приложения:

	-(void)willRetrieveLaunchMessage;
	-(void)didFailToRetrieveLaunchMessage;
	-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

<!---HONumber=AcomDC_0302_2016-->