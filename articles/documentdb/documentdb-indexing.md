<properties 
	pageTitle="Автоматическое индексирование в DocumentDB | Microsoft Azure" 
	description="Узнайте о том, как работает автоматическое индексирование в Azure DocumentDB." 
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="mimig" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2016" 
	ms.author="arramac"/>
	
# Автоматическое индексирование в Azure DocumentDB

Эта статья является выдержкой из документа [Схемонезависимое индексирование с помощью Azure DocumentDB](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), который будет представлен на [41-й внутренней конференции по очень большим базам данных](http://www.vldb.org/2015/), которая пройдет с 31 августа по 4 сентября 2015 года. Статья содержит вводную информацию о работе индексирования в Azure DocumentDB.

После прочтения этой статьи вы сможете ответить на следующие вопросы:

- Как DocumentDB выводит схему из документа JSON?
- Как DocumentDB строит индекс разнородных документов?
- Как DocumentDB выполняет автоматическое индексирование в масштабе?

##<a id="HowDocumentDBIndexingWorks"></a> Как работает индексирование в DocumentDB

[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) — это действительно бессхемная база данных, специально созданная для JSON. Она не ожидает и не требуют каких-либо схем или определений дополнительного индекса для индексации данных в масштабе. Это позволяет быстро определять модели данных приложения и выполнять на них итерации с помощью DocumentDB. По мере добавления документов в коллекцию DocumentDB автоматически индексирует все свойства документов, чтобы они стали доступны для выполнения вами запросов. Автоматическое индексирование позволяет хранить документы, принадлежащие совершенно произвольным схемам, не беспокоясь о схемах или дополнительных индексах.

Во избежание несогласованности между базой данных и моделями программирования приложений DocumentDB использует простоту JSON и отсутствие спецификации схемы. Не делаются никаких предположений о документах. Документы в пределах коллекции DocumentDB могут отличаться как схемой, так и значениями конкретного экземпляра. В отличие от других баз данных документов, ядро СУБД DocumentDB работает непосредственно на уровне грамматики JSON, не привязываясь к понятию схемы документа и размывая границу между значениями структуры и экземпляра документов. Это, в свою очередь, позволяет ему автоматически индексировать документы без схемы или дополнительных индексов.

Индексирование в DocumentDB основано на том факте, что грамматика JSON позволяет представлять документы **в виде деревьев**. Чтобы представить документ JSON в виде дерева, требуется создать фиктивный корневой узел, который будет являться родителем остальные реальных узлов в подчиненном документе. Каждая метка, включая и индексы массива в документе JSON, становится узлом дерева. На рисунке ниже показан пример документа JSON и его соответствующее представление в виде дерева.

>[AZURE.NOTE] JSON является самоописывающим, т. е. каждый документ содержит схему (метаданные) и данные. Например, `{"locationId": 5, "city": "Moscow"}` показывает, что имеется два свойства, `locationId` и `city`, и что они имеют числовое и строковое значения. DocumentDB может вывести схему документов и индексировать их при вставке или замене. При этом вам никогда не понадобится определять схемы или дополнительные индексы.


**Документы JSON в виде деревьев:**

![Документы в виде деревьев](media/documentdb-indexing/DocumentsAsTrees.png)

Например, в приведенном выше примере:

- Например, свойство JSON `{"headquarters": "Belgium"}` в вышеприведенном примере соответствует path/headquarters/Belgium.
- Массив JSON `{"exports": [{"city": “Moscow"}`, `{"city": Athens"}]}` соответствует путям `/exports/[]/city/Moscow` и `/exports/[]/city/Athens`.

При использовании автоматического индексирования: 1. Каждый путь в дереве документов индексируется (если только разработчик явно не настроил политику индексирования, чтобы исключить определенные шаблоны путей). 2. Каждое изменение документа в коллекции DocumentDB приводит к обновлению структуры индекса (т. е. приводит к добавлению или удалению узлов). Одно из основных требований автоматического индексирования документов — обеспечить, чтобы стоимость индексирования и запроса документа с большим уровнем вложенности структуры (например, 10 уровней) была такой же, как для неструктурированного документа JSON, состоящего из пар "ключ — значение" всего на один уровень глубже. Таким образом представление нормализованного пути — это основа, на которой строятся подсистемы автоматического индексирования и выполнения запросов.

Важное следствие единообразного рассмотрения значений схемы и экземпляра с точки зрения путей: если следовать логике, то отображаемый индекс двух документов, обеспечивающий сопоставление путей и идентификаторов документов, содержащих эти пути, можно представить в виде дерева, как и отдельные документы. DocumentDB использует этот факт для создания дерева индексов, которое составляется путем объединения всех деревьев, представляющих отдельные документов в коллекции. По мере добавления новых документов или изменения документов в коллекции дерево индексов в коллекциях DocumentDB со временем увеличивается.


**Индекс DocumentDB в виде дерева:**

![Индекс в виде дерева](media/documentdb-indexing/IndexAsTree.png)

Несмотря на отсутствие схем, языки запросов SQL и JavaScript в DocumentDB предоставляют реляционные проекции и фильтры, иерархическую навигацию между документами, пространственными операциями и вызов определяемых пользователем функций, написанных полностью на языке JavaScript. Среда выполнения запросов DocumentDB поддерживает эти запросы, так как может работать непосредственно с древовидным представлением индекса данных.

Политика индексирования по умолчанию автоматически индексирует все свойства всех документов и обеспечивает согласованность запросов (то есть индекс синхронно обновляется при записи в документ). Как DocumentDB обеспечивает согласованность обновлений в дереве индексов в масштабе? DocumentDB использует методы обслуживания индексов со структурированием журналов, оптимизацией записи и без блокировки. Это значит, что DocumentDB поддерживает устойчивый объем быстрых операций записи, при этом обслуживая согласованные запросы.

Индексирование DocumentDB предназначено для повышения эффективности хранения и обработки мультитенантности. Для повышения эффективности затрат дополнительные затраты дискового пространства при индексировании являются ограниченными и предсказуемыми. Обновления индекса также выполняются в рамках бюджета системных ресурсов, выделяемых на каждую коллекцию DocumentDB.

##<a name="NextSteps"></a>Дальнейшие действия
- Загрузите документ [Схемонезависимое индексирование с помощью Azure DocumentDB](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), который будет представлен на 41-й внутренней конференции по очень большим базам данных, которая пройдет с 31 августа по 4 сентября 2015 года.
- [Формирование запросов с помощью DocumentDB SQL](documentdb-sql-query.md)
- Узнайте о том, как настроить индекс DocumentDB, [здесь](documentdb-indexing-policies.md).
 

<!---HONumber=AcomDC_0204_2016-->