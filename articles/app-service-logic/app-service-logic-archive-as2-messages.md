<properties 
   pageTitle="Архивация сообщений соединителя AS2 | Служба приложений Microsoft Azure" 
   description="Как архивировать или сохранить сообщения соединителя AS2 в службе приложений Azure" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="02/18/2016"
   ms.author="rajram"/>


# Обзор архивации сообщений соединителя AS2

>[AZURE.NOTE] Эта версия статьи предназначена для приложений логики со схемой версии 2014-12-01-preview.

[Соединитель AS2](app-service-logic-connector-as2.md) предоставляет возможность архивации сообщений. Архивация сохраняет сообщения в **контейнере больших двоичных объектов Azure**, который является частью настроек пакета.

Архивация предоставляется для сообщений и подтверждений (MDN) в следующих двух точках.

1. **Триггер получения или декодирования**: сообщение архивируется сразу при получении экземпляром приложения API. 
2. **Действие кодирования или отправки**: закодированное сообщение архивируется после завершения всей обработки и непосредственно перед отправкой партнеру. 

## Практическое руководство. Получение архивного URL-адреса сообщения

Перейдите в экземпляр приложения API «Соединитель AS2» и нажмите «Отслеживание». Выберите сведения отслеживания с помощью параметров фильтра. Когда сообщение откроется в представлении, щелкните его, чтобы увидеть его подробное представление. Архивный URL-адрес для этого сообщения будет отображаться в этом подробном представлении: ![][1]

## Практическое руководство. Извлечение архивированного сообщения

Для извлечения архивированного сообщения из хранилища BLOB-объектов Azure используйте URL-адрес, полученный выше.


<!--Image references-->
[1]: ./media/app-service-logic-archive-as2-messages/Tracking.jpg
 

<!---HONumber=AcomDC_0224_2016-->