<properties
   pageTitle="Использование связанных шаблонов в диспетчере ресурсов Azure"
   description="Описывает, как использовать связанные шаблоны в шаблоне диспетчера ресурсов Azure для создания решения модульных шаблонов. Показывает, как передавать значения параметров, указывать файл параметров и динамически создаваемые URL-адреса."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/07/2015"
   ms.author="tomfitz"/>

# Использование связанных шаблонов в диспетчере ресурсов Azure

Один шаблон диспетчера ресурсов Azure можно связать с другим шаблоном, что позволяет делить развертывания на набор целевых шаблонов для конкретной цели. Так же как при разделении приложения на несколько классов кода, разделение развертывания дает преимущества с точки зрения тестирования, повторного использования и удобства чтения.

Можно передавать параметры из основного шаблона в связанный шаблон. Кроме того, эти параметры можно напрямую сопоставить с параметрами или переменными, предоставляемыми вызывающим шаблоном. Связанный шаблон может также передать выходную переменную в исходный шаблон, что позволяет активировать двусторонний обмен данными между шаблонами.

## Создание связи с шаблоном

Чтобы создать связь между двумя шаблонами, добавьте ресурс развертывания в основном шаблоне, который указывает на связанный шаблон. Задайте для свойства **templateLink** URI связанного шаблона. Значения параметров для связанного шаблона можно предоставить, указав значения непосредственно в шаблоне или привязав их к файлу параметров. В следующем примере используется свойство **parameters** для непосредственного указания значения параметра.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

Службе диспетчера ресурсов необходим доступ к связанному шаблону, т. е. невозможно указать локальный файл, доступный только в локальной сети, для связанного шаблона. Можно предоставить только URI, который включает в себя **http** или **https**. Один из вариантов — размещение связанного шаблона в учетной запись хранения и использование URI для этого элемента, как показано ниже.

    "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0",
    }


## Создание связи с файлом параметров

В следующем примере используется свойство **parametersLink** привязки к файлу параметров.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

Значение URI для связанного параметра файла не может быть локальным файлом и должно содержать **http** или **https**.

## Использование переменных для связывания шаблонов

В предыдущих примерах были показаны жестко запрограммированные значения URL-адреса для ссылок на шаблоны. Этот подход может подойти для простого шаблона, но он не действует при работе с большим набором модульных шаблонов. Вместо этого можно создать статическую переменную, которая содержит базовый URL-адрес для основного шаблона, а затем динамически создавать URL-адреса для связанных шаблонов на основе этого адреса. Преимущество этого подхода заключается в том, что можно легко переместить или разветвить шаблон, так как для этого необходимо лишь изменить статическую переменную в основном шаблоне. Основной шаблон передает правильные URI через разделенный шаблон.

В следующем примере показано, как использовать базовый URL-адрес для создания двух URL-адресов для связанных шаблонов (**sharedTemplateUrl** и **vmTemplate**).

    "variables": {
        "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
        "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
        "tshirtSizeSmall": {
            "vmSize": "Standard_A1",
            "diskSize": 1023,
            "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
            "vmCount": 2,
            "slaveCount": 1,
            "storage": {
                "name": "[parameters('storageAccountNamePrefix')]",
                "count": 1,
                "pool": "db",
                "map": [0,0],
                "jumpbox": 0
            }
        }
    }

Вы также можете получить базовый URL-адрес текущего шаблона с помощью функции [deployment()](../resource-group-template-functions/#deployment), а затем использовать его для получения URL-адресов других шаблонов в том же расположении. Это полезно в ситуации, когда место расположения шаблонов меняется (например, в связи с изменением версии) либо вы не хотите указывать непосредственные URL-адреса в файле шаблона.

    "variables": {
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
    }

## Передача значений из связанного шаблона

Если вам необходимо передать значение из связанного шаблона в основной шаблон, можно создать значение в разделе **outputs** связанного шаблона. Пример см. в статье [Совместное использование состояния в шаблонах диспетчера ресурсов Azure](best-practices-resource-manager-state.md).

## Дальнейшие действия
- [Создание шаблонов](./resource-group-authoring-templates.md)
- [Развертывание шаблонов](resource-group-template-deploy.md)

<!---HONumber=AcomDC_1210_2015-->