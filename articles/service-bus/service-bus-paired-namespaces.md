<properties 
   pageTitle="Сопряженные пространства имен служебной шины | Microsoft Azure"
   description="Сведения о реализации и затратах для сопряженных пространств имен"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/28/2015"
   ms.author="sethm" />

# Сведения о реализации и финансовых затратах для сопряженных пространств имен

Метод [PairNamespaceAsync][] с помощью экземпляра [SendAvailabilityPairedNamespaceOptions][] выполняет определенные задачи от вашего имени. Так как использование функции связано с финансовыми расходами, полезно понимать эти задачи, чтобы при выполнении каких-либо автоматических действий они были ожидаемы. API включает следующие автоматические действия, выполняемые от вашего имени:

-   Создает очереди невыполненной работы.
-   Создает объект [MessageSender][], который обращается к очередям или разделам.
-   Если сущность обмена сообщениями становится недоступной, отправляет сущности сообщения для проверки связи, чтобы определить, когда сущность снова станет доступной.
-   При необходимости создает набор "конвейеров сообщений", которые перемещают сообщения из очередей невыполненной работы в основные очереди.
-   Согласовывает закрытие или завершение со сбоем основного и дополнительного экземпляров [MessagingFactory][].

На высоком уровне эта функция работает следующим образом: если основная сущность работоспособна, изменений в поведении не происходит. Когда интервал сбоя [FailoverInterval][] истекает, и попытки отправить сообщение основной сущности завершаются неудачно с исключениями [MessagingException][] или [TimeoutException][], которые не являются временными, происходит следующее:

1.  Отправка операций в основную сущность отключается, и система проверяет связь с основной сущностью, до тех пор, пока связь не будет восстановлена.

2.  Выбирается произвольная очередь невыполненной работы.

3.  Объекты [BrokeredMessage][] направляются в выбранную очередь невыполненной работы.

1.  Если операция отправки в выбранную очередь невыполненной работы завершается неудачно, эта очередь убирается из ротации, и выбирается новая очередь. Все отправители сообщений в экземпляр [MessagingFactory][] узнают о сбое.

Последовательность операций представлена на следующих рисунках. Сначала отправитель отправляет сообщения.

![Сопряженные пространства имен][0]

Если отправка в основную очередь завершается неудачно, отправитель начинает отправлять сообщения в очередь невыполненной работы, выбранную случайным образом. Одновременно он запускает задачу проверки связи.

![Сопряженные пространства имен][1]

На этом этапе сообщения остаются в дополнительной очереди и все еще не доставлены в основную очередь. После того как основная очередь снова станет работоспособной, по крайней мере один процесс должен запустить процесс выкачивания. Процесс выкачивания доставляет сообщения из всех возможных очередей невыполненной работы в соответствующие сущности назначения (очереди и разделы).

![Сопряженные пространства имен][2]

В оставшейся части этого раздела приводятся конкретные сведения о работе этих фрагментов.

## Создание очередей невыполненной работы

Объект [SendAvailabilityPairedNamespaceOptions][], передаваемый в метод [PairNamespaceAsync][], показывает количество очередей невыполненной работы, которые вы хотите использовать. Затем создается каждая очередь невыполненной работы со следующим набором свойств (все остальные значения устанавливаются в значения по умолчанию для [QueueDescription][]):

| Путь | [основное пространство имен]/x-servicebus-transfer/[индекс], где [индекс] — это значение в диапазоне [0, BacklogQueueCount) |
|----------------------------------------|------------------------------------------------------------------------------------------------------|
| MaxSizeInMegabytes | 5120 |
| MaxDeliveryCount | int.MaxValue |
| DefaultMessageTimeToLive | TimeSpan.MaxValue |
| AutoDeleteOnIdle | TimeSpan.MaxValue |
| LockDuration | 1 минута |
| EnableDeadLetteringOnMessageExpiration | Да |
| EnableBatchedOperations | Да |

Например, первая очередь невыполненной работы, создаваемая для пространства имен **contoso**, называется `contoso/x-servicebus-transfer/0`.

При создании очередей код сначала проверяет, существует ли такая очередь. Если очередь не существует, то она создается. Код не выполняет очистку "лишних" очередей невыполненной работы. В частности, если приложение с основным пространством имен **contoso** запрашивает пять очередей невыполненной работы, но очередь невыполненной работы с путем `contoso/x-servicebus-transfer/7` существует, то эта дополнительная очередь по-прежнему присутствует, но не используется. В системе явно разрешено существование дополнительных очередей невыполненной работы, которые не будут использоваться. Как владелец пространства имен вы должны самостоятельно удалять все неиспользуемые и ненужные очереди невыполненной работы. Причина такого подхода в том, что служебная шина не знает назначения всех очередей в пространстве имен. Кроме того, если очередь с указанным именем существует, но не соответствует предполагаемому [QueueDescription][], то вы должны определить собственные причины для изменения поведения по умолчанию. При внесении изменений в код очередей невыполненной работы не предоставляется никаких гарантий. Тщательно проверьте свои изменения.

## Пользовательский объект MessageSender

При отправке все сообщения проходят через внутренний объект [MessageSender][], который работает как обычно, если все нормально, и перенаправляет сообщения в очереди невыполненной работы при возникновении сбоя. При возникновении ошибки, которая не является временной, запускается таймер. По истечении периода [TimeSpan][], состоящего из значения свойства [FailoverInterval][], в течение которого не было ни одной успешной отправки сообщения, запускается отработка отказа. На этом этапе для каждой сущности выполняются следующие действия.

- С интервалом [PingPrimaryInterval][] выполняется задача проверки связи, чтобы определить, доступна ли сущность. Когда связь будет восстановлена, весь клиентский код, использующий сущность, немедленно начинает отправлять новые сообщения в основное пространство имен.

- Будущие запросы на отправку сообщений от любых других отправителей в эту же сущность приведут к изменению [BrokeredMessage][] таким образом, чтобы оно попало в очередь невыполненной работы. При этом изменении некоторые свойства [BrokeredMessage][] удаляются и сохраняются в другом месте. Следующие свойства удаляются и добавляются под новыми псевдонимами, позволяя служебной шине и пакету SDK обрабатывать сообщения одинаково:

| Старое имя свойства | Новое имя свойства |
|-------------------------|-------------------|
| SessionId | x-ms-sessionid |
| TimeToLive (срок жизни) | x-ms-timetolive |
| ScheduledEnqueueTimeUtc | x-ms-path |

Исходный путь назначения также сохраняется в сообщении как свойство с именем x-ms-path. Такой подход позволяет сообщениям для множества сущностей сосуществовать в одной очереди невыполненной работы. Свойства преобразуются обратно в процессе выкачивания.

С пользовательским объектом [MessageSender][] могут возникнуть проблемы, когда сообщения приблизятся к пределу в 256 КБ и начнется отработка отказа. Пользовательский объект [MessageSender][] хранит сообщения для всех очередей и разделов совместно в очередях невыполненной работы. В этом объекте сообщения из многих основных источников совмещаются с очередями невыполненной работы. Чтобы обеспечить балансировку нагрузки между большим количеством клиентов, которые не знают друг о друге, пакет SDK произвольно выбирает одну очередь невыполненной работы для каждого [QueueClient][] или [TopicClient][], которого вы создаете в коде.

## Проверка связи

Сообщение проверки связи — это пустое сообщение [BrokeredMessage][], у которого свойство [ContentType][] имеет значение application/vnd.ms-servicebus-ping, а значение свойства [TimeToLive][] равно 1 секунде. Проверка связи для служебной шины имеет одну особенность: сервер никогда не доставляет сообщение проверки связи, если любой вызывающий объект запрашивает [BrokeredMessage][]. Таким образом, вам не нужно учиться получать и игнорировать эти сообщения. Для каждой сущности (уникальной очереди или раздела) каждого экземпляра [MessagingFactory][] каждого клиента проверка связи выполняется, когда они считаются недоступными. По умолчанию это происходит один раз в минуту. Сообщения проверки связи считаются обычными сообщениями служебной шины и могут тарифицироваться за использование пропускной способности и за сообщения. Как только клиент определит, что система доступна, отправка сообщений прекращается.

## Процесс выкачивания

По крайней мере один исполняемый файл в приложении должен активно выполнять процесс выкачивания. Процесс выкачивания выполняет прием с длинным опросом, который длится 15 минут. Когда все сущности доступны и имеется 10 очередей невыполненной работы, приложение, на котором выполняется процесс выкачивания, вызывает операцию получения 40 раз в час, 960 раз в день и 28 800 раз за 30 дней. Когда процесс выкачивания перемещает сообщения из очереди невыполненной работы в основную очередь, каждое сообщение тарифицируется за следующие действия (на всех этапах взимается стандартная плата за размер сообщения и пропускную способность):

1.  Отправка в очередь невыполненной работы.

2.  Получение из очереди невыполненной работы.

3.  Отправка в основную очередь.

4.  Получение из основной очереди.

## Поведение при закрытии или сбое

В приложении, в котором размещен процесс выкачивания, он продолжает работать при закрытии или сбое основной или дополнительной [MessagingFactory][], если другая MessagingFactory также не была закрыта или в ней также не произошел сбой и процесс выкачивания это не обнаружил. Если другая [MessagingFactory][] не закрывается в течение 5 секунд, процесс выкачивания завершит открытую [MessagingFactory][] со сбоем.

## Дальнейшие действия

Подробное обсуждение асинхронного обмена сообщениями служебной шины см. в статье [Шаблоны асинхронного обмена сообщениями и высокий уровень доступности].

  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [SendAvailabilityPairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [FailoverInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.failoverinterval.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
  [PingPrimaryInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.pingprimaryinterval.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [ContentType]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx
  [TimeToLive]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
  [Шаблоны асинхронного обмена сообщениями и высокий уровень доступности]: service-bus-async-messaging.md
  [0]: ./media/service-bus-paired-namespaces/IC673405.png
  [1]: ./media/service-bus-paired-namespaces/IC673406.png
  [2]: ./media/service-bus-paired-namespaces/IC673407.png

<!---HONumber=AcomDC_0107_2016-->