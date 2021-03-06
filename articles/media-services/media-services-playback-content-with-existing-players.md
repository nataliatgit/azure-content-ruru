<properties 
	pageTitle="Воспроизведение содержимого" 
	description="В этом разделе перечислены существующие проигрыватели, которые можно использовать для воспроизведения содержимого." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="02/03/2016"  
	ms.author="juliako"/>


#Воспроизведение содержимого с помощью существующих проигрывателей

Службы мультимедиа Azure поддерживают многие популярные форматы потоковой передачи, например Smooth Streaming, HTTP Live Streaming и MPEG-Dash. В этом подразделе описываются имеющиеся проигрыватели, которые можно использовать для тестирования потоков.

>[AZURE.NOTE]Для воспроизведения упакованного динамически или динамически зашифрованного содержимого, необходимо получить не менее одной единицы потоковой передачи для конечной точки потоковой передачи, из которой планируется доставка содержимого. Информацию о масштабировании единиц потоковой передачи см. в разделе [Как масштабировать единицы потоковой передачи](media-services-manage-origins.md#scale_streaming_endpoints).

###Проигрыватель содержимого служб мультимедиа классического портала Azure

На **классическом портале Azure** имеется проигрыватель содержимого, с помощью которого можно проверить видео.

Выберите нужное видео (убедитесь, что оно [опубликовано](media-services-manage-content.md#publish)) и нажмите кнопку **Воспроизвести** в нижней части портала.
 
Важные особенности

- **ПРОИГРЫВАТЕЛЬ КОНТЕНТА СЛУЖБ МУЛЬТИМЕДИА** выполняет воспроизведение из конечной точки потоковой передачи по умолчанию. Если требуется воспроизвести из конечной точке потоковой передачи не по умолчанию, используйте другой проигрыватель. Например, [мультимедиапроигрыватель Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
 

![AMSPlayer][AMSPlayer]

###Azure Media Player

Используйте [мультимедиапроигрыватель Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html) для воспроизведения содержимого (незашифрованного или защищенного) в любом из следующих форматов:

- Smooth Streaming
- MPEG DASH
- HLS
- Последовательный MP4


###Flash Player

####Шифрование AES с маркером 

[http://aestoken.azurewebsites.net]("http://aestoken.azurewebsites.net)

###Проигрыватели Silverlight

####Мониторинг

[http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)

####PlayReady с маркером

[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### Проигрыватели DASH

[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

###Другие

Для проверки URL-адресов HLS также можно использовать:

- **Safari** на устройстве iOS или
- **3ivx HLS Player** в Windows.

##Разработка видеопроигрывателей

Сведения о том, как разрабатывать собственные проигрыватели, см. в разделе [Разработка видеопроигрывателей](media-services-develop-video-players.md)




##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png

<!---HONumber=AcomDC_0211_2016-->