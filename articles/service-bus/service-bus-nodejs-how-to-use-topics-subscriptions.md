<properties 
	pageTitle="Использование разделов служебной шины с Node.js | Microsoft Azure" 
	description="Узнайте, как использовать разделы и подписки служебной шины в Azure в приложении Node.js." 
	services="service-bus" 
	documentationCenter="nodejs" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="02/08/2016" 
	ms.author="sethm"/>


# Использование разделов и подписок служебной шины

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

В этом руководстве показано, как использовать разделы и подписки служебной шины в приложениях Node.js В этой статье описаны такие сценарии, как **создание разделов и подписок**, **создание фильтров подписок**, **отправка сообщений** в раздел, **получение сообщений от подписки** и **удаление разделов и подписок**. Дополнительные сведения о разделах и подписках см. в разделе [Дальнейшие действия](#next-steps).

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Создание приложения Node.js

Создайте пустое приложение Node.js. Указания по созданию приложения Node.js см. в статьях [Создание и развертывание приложения Node.js на веб-сайте Azure], [Облачная служба Node.js][Node.js Cloud Service] (с помощью Windows PowerShell) или веб-сайт с WebMatrix.

## Настройка приложения для использования служебной шины

Для использования служебной шины скачайте пакет Node.js для Azure. Пакет содержит набор библиотек, взаимодействующих со службами REST Service Bus.

### Использование диспетчера пакета Node (NPM) для получения пакета

1.  Используя интерфейс командной строки, такой как **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix), перейдите в папку, в которой создан пример приложения.

2.  В командной строке введите команду **npm install azure**. Должен получиться вот такой результат:

	```
    	azure@0.7.5 node_modules\azure
	├── dateformat@1.0.2-1.2.3
	├── xmlbuilder@0.4.2
	├── node-uuid@1.2.0
	├── mime@1.2.9
	├── underscore@1.4.4
	├── validator@1.1.1
	├── tunnel@0.0.2
	├── wns@0.5.3
	├── xml2js@0.2.7 (sax@0.5.2)
	└── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
	```

3.  Выполнив команду **ls**, можно убедиться, что каталог **node\_modules** создан. Найдите в папке пакет **azure**, содержащий библиотеки, необходимые для доступа к разделам служебной шины.

### Импорт модуля

С помощью Блокнота или другого текстового редактора добавьте следующее в начало файла **server.js** приложения:

```
var azure = require('azure');
```

### Настройка подключения к Service Bus

Модуль Azure считывает переменные среды AZURE\_SERVICEBUS\_NAMESPACE и AZURE\_SERVICEBUS\_ACCESS\_KEY для получения сведений, необходимых для подключения к служебной шине. Если эти переменные среды не заданы, при вызове **createServiceBusService** необходимо указать сведения об учетной записи.

Пример настройки переменных среды в файле конфигурации для облачной службы Azure см. в статье [Облачная служба Node.js с хранилищем][].

Пример настройки переменных среды для веб-сайта Azure на [классическом портале Azure][] см. в статье [Веб-приложение Node.js с хранилищем][].

## Создание раздела

Объект **ServiceBusService** позволяет работать с разделами. Следующий код создает объект **ServiceBusService**. Добавьте его в начало файла **server.js** после оператора импорта модуля Аzure.

```
var serviceBusService = azure.createServiceBusService();
```

Вызов **createTopicIfNotExists** для объекта **ServiceBusService** возвратит указанный раздел (если он существует) или создаст новый раздел с указанным именем. В приведенном ниже коде используется **createTopicIfNotExists** для создания раздела с именем MyTopic или подключения к нему.

```
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

**createServiceBusService** также поддерживает дополнительные параметры, позволяющие переопределить настройки раздела по умолчанию, такие как срок жизни сообщения или максимальный размер раздела. В следующем примере показано, как установить максимальный размер раздела 5 ГБ и значение срока жизни в 1 минуту.

```
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### Фильтры

Дополнительные операции фильтрации можно применить к выполняемым операциям, используя **ServiceBusService** К операциям фильтрации могут относиться ведение журнала, автоматический повтор и т. д. Фильтры являются объектами, реализующими метод со следующей сигнатурой:

```
function handle (requestOptions, next)
```

Выполнив предварительную обработку параметров запроса, метод должен вызвать `next`, передавая обратный вызов со следующей сигнатурой:

```
function (returnObject, finalCallback, next)
```

В этой функции обратного вызова и после обработки returnObject (ответ на запрос к серверу) функция обратного вызова должна либо вызвать "next", если он существует, чтобы продолжить обработку других фильтров, либо в противном случае просто вызвать "finalCallback" для завершения обращения к службе.

В Azure SDK для Node.js включены два фильтра, реализующие логику повторных попыток: **ExponentialRetryPolicyFilter** и **LinearRetryPolicyFilter** Следующий код создает объект **ServiceBusService**, использующий **ExponentialRetryPolicyFilter**

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## Создание подписок

Подписки на разделы также создаются с помощью объекта **ServiceBusService**. У подписок есть имена, и они могут использовать дополнительный фильтр, ограничивающий набор сообщений, доставляемых в виртуальную очередь подписки.

> [AZURE.NOTE] Подписки являются постоянными и продолжают существовать либо до их удаления, либо до удаления раздела, с которым они связаны. Если приложение содержит логику для создания подписки, оно сначала должно проверить, существует ли подписка, используя метод **getSubscription**.

### Создание подписки с фильтром по умолчанию (MatchAll)

Фильтр **MatchAll** является фильтром по умолчанию, используемым, если при создании новой подписки не указан фильтр. Если используется фильтр **MatchAll**, все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка с именем AllMessages и фильтром по умолчанию **MatchAll**.

```
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### Создание подписок с фильтрами

Вы также можете создать фильтры, позволяющие определять, какие сообщения, отправленные в раздел, будут отображаться в определенной подписке раздела.

Самый гибкий тип фильтра, который поддерживается подписками, — это **SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Дополнительные сведения о выражениях, которые можно использовать с SQL-фильтром, см. в описании синтаксиса [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Добавить фильтры в подписку можно с помощью метода **createRule** объекта **ServiceBusService**. Этот метод позволяет добавлять новые фильтры в существующую подписку.

> [AZURE.NOTE] Так как ко всем новым подпискам автоматически применяется фильтр по умолчанию, сначала необходимо удалить фильтр по умолчанию, иначе **MatchAll** переопределит поведение всех остальных заданных фильтров. Вы можете удалить правило по умолчанию с помощью метода **deleteRule** объекта **ServiceBusService**.

В следующем примере создается подписка с именем `HighMessages`, содержащая объект **SqlFilter**, который выбирает только сообщения, значение настраиваемого свойства **MessageNumber** которых превышает 3.

```
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'HighMessages', 
            'HighMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Аналогичным образом в следующем примере создается подписка с именем `LowMessages` и фильтром **SqlFilter**, который выбирает только те сообщения, у которых значение свойства **messagenumber** меньше или равно 3.

```
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'LowMessages', 
            'LowMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Если сообщение отправляется в раздел `MyTopic`, оно всегда будет доставляться в приемники, подписанные на подписку раздела `AllMessages`, и в отдельные приемники, подписанные на подписки разделов `HighMessages` и `LowMessages` (в зависимости от содержимого сообщений).

## Как отправлять сообщения в раздел

Чтобы отправить сообщение в раздел служебной шины, ваше приложение должно использовать метод **sendTopicMessage** объекта **ServiceBusService**. Сообщения, отправленные в разделы служебной шины, являются объектами **BrokeredMessage**. Объекты **BrokeredMessage** содержат набор стандартных свойств (например **Label** и **TimeToLive**), словарь, используемый для хранения настраиваемых свойств приложения, и текст из строковых данных. Приложение может задать текст сообщения, передав строковое значение в метод **sendTopicMessage**, и все обязательные стандартные свойства будут заполнены значениями по умолчанию.

На приведенном ниже примере продемонстрирована отправка тестового сообщения в раздел MyTopic. Обратите внимание, что значение свойства **messagenumber** сообщений зависит от итерации цикла (определяет, какие подписки получают его).

```
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Разделы служебной шины поддерживают максимальный размер сообщения 256 МБ (максимальный размер заголовка, содержащего стандартные и настраиваемые свойства приложения — 64 МБ). Ограничения на количество сообщений в разделе нет, но есть максимальный общий размер сообщений, содержащихся в разделе. Этот размер задается при создании с верхним пределом 5 ГБ.

## Получение сообщений от подписки

Сообщения извлекаются из подписки с помощью метода **receiveSubscriptionMessage** в объекте **ServiceBusService**. По умолчанию прочитанные сообщения удаляются из подписки. Но сообщение можно прочитать (извлечь) и заблокировать, не удаляя его из подписки, установив для необязательного параметра **isPeekLock** значение **true**.

Поведение по умолчанию — чтение и удаление сообщения как часть операции получения — это простейшая модель, оптимальная для сценариев, в которых приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку служебная шина помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

Если для параметра **isPeekLock** установлено значение **true**, получение становится двухэтапной операцией, что позволяет поддерживать приложения, которые не могут пропускать сообщения. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Обработав сообщение (или сохранив его в надежном месте для последующей обработки), приложение вызывает метод **deleteMessage** и указывает сообщение, которое будет удалено как параметр, таким образом завершая второй этап процесса получения. Метод **deleteMessage** помечает сообщение как использованное и удаляет его из подписки.

В следующем примере показано получение и обработка сообщений с помощью метода **receiveSubscriptionMessage**. В этом примере сообщение сначала поступает из подписки LowMessages и удаляется, а затем — из подписки HighMessages, если для параметра **isPeekLock** установлено значение true. Затем сообщение удаляется с помощью метода **deleteMessage**:

    serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        }
    });
    serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            console.log(lockedMessage);
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                    console.log('message has been deleted.');
                }
            }
        }
    });

## Как обрабатывать сбои приложения и нечитаемые сообщения

служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если по какой-либо причине приложению-получателю не удается обработать сообщение, оно вызывает метод **unlockMessage** в объекте **ServiceBusService**. Таким образом, служебная шина разблокирует сообщение в подписке и сделает его доступным для приема тем же или другим приложением.

Кроме того, с сообщением, заблокированным в подписке, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), служебная шина автоматически разблокирует сообщение и сделает его доступным для приема.

Если в приложении происходит сбой после обработки сообщения, но перед вызовом метода **deleteMessage**, сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства **MessageId** сообщения, которое остается постоянным для различных попыток доставки.

## Удаление разделов и подписок

Разделы и подписки хранятся постоянно, и их нужно удалять явным образом на [классическом портале Azure][] или с помощью программных средств. В следующем примере показано, как удалить раздел с именем `MyTopic`.

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

При удалении раздела также удаляются все подписки, зарегистрированные в этом разделе. Подписки также можно удалять по отдельности. В следующем примере показано, как удалить подписку с именем `HighMessages` из раздела `MyTopic`:

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## Дальнейшие действия

Вы узнали основные сведения о разделах служебной шины. Для получения дополнительных сведений используйте следующие ссылки.

-   См. статью [Очереди, темы и подписки][].
-   Справочник API для [SqlFilter][].
-   Посетите репозиторий [Пакет SDK хранилища Azure для Node][] на веб-сайте GitHub.

  [Пакет SDK хранилища Azure для Node]: https://github.com/Azure/azure-sdk-for-node
  [классическом портале Azure]: http://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Очереди, темы и подписки]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Создание и развертывание приложения Node.js на веб-сайте Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Облачная служба Node.js с хранилищем]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Веб-приложение Node.js с хранилищем]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
 

<!---HONumber=AcomDC_0211_2016-->