<properties 
	pageTitle="Связанные ресурсы в коллекции плиток" 
	description="Дополнительные сведения о связанных ресурсах, отображаемых в коллекции плиток на портале предварительной версии Azure." 
	services="azure-portal" 
	documentationCenter="" 
	authors="adamabdelhamed" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-portal" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="adamab"/>

# Связанные ресурсы в коллекции плиток

В коллекции плиток вы можете найти плитки для конкретного ресурса, чтобы перетащить их в свою текущую колонку. Используя коллекцию плиток, вы можете создавать представления управления, связывающие ресурсы. Ресурсы, связанные с выбранным ресурсом, включают все ресурсы, которые совместно используют одну и ту же группу ресурсов в качестве ресурса, а также все ресурсы, которые ссылаются на этот ресурс или на которые ссылается ресурс.

## Связанные ресурсы в диспетчере ресурсов Azure

Связывание — это функция диспетчера ресурсов Azure. Она позволяет объявлять отношения между ресурсами, даже если они не находятся в одной и той же группе ресурсов. Связывание не влияет на среду выполнения ресурсов, выставление счетов или доступ на основе ролей. Это просто механизм, который можно использовать для представления связей, чтобы такие средства, как коллекция плиток, могли предоставлять широкие возможности управления. Ваши средства могут проверять ссылки, используя API ссылок. Кроме того, они могут предоставлять настраиваемые возможности управления отношениями.

## Как связать ресурсы?

Во время создания ресурсов на портале либо путем развертывания шаблона с использованием Azure PowerShell или Azure CLI для некоторых зависимых ресурсов ссылки создаются автоматически. Вы также можете программно связать ресурсы с помощью [API REST связанных ресурсов](https://msdn.microsoft.com/library/azure/mt238499.aspx) или путем объявления отношений в шаблоне. Полную информацию о работе с ресурсами см. в статье [Связывание ресурсов в диспетчере ресурсов Azure](../resource-group-link-resources.md).

## Дальнейшие действия

- Если вам нужна информация для написания шаблонов диспетчера ресурсов Azure, см. статью [Разработка шаблонов](../resource-group-authoring-templates.md).
- Если вы хотите подробно изучить процесс создания связей между ресурсами, см. статью [Связывание ресурсов в диспетчере ресурсов Azure](../resource-group-link-resources.md).
- Чтобы узнать больше о работе с группами ресурсов на портале предварительной версии, см. статью [Использование портала предварительной версии Azure для управления ресурсами Azure](resource-group-portal.md).

<!---HONumber=Oct15_HO3-->