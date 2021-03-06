<properties
   pageTitle="Функции шаблонов диспетчера ресурсов | Microsoft Azure"
   description="Описывает функции, используемые в шаблоне диспетчера ресурсов Azure для извлечения значений, работы со строками и числовыми значениями и получения сведений о развертывании."
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
   ms.date="02/22/2016"
   ms.author="tomfitz"/>

# Функции шаблонов диспетчера ресурсов Azure

В этом разделе описаны все функции, которые можно использовать в шаблоне диспетчера ресурсов Azure.

Функции шаблонов и их параметры зависят от регистра. Например, диспетчер ресурсов одинаково преобразует **variables('var1')** и **VARIABLES('VAR1')**. При вычислении функция сохранит регистр, если только не изменяет его явным образом (например, toUpper или toLower). Для некоторых типов ресурсов требования к регистру могут не зависеть от того, как происходит вычисление функций.

## Числовые функции

Диспетчер ресурсов предоставляет следующие функции для работы с целыми числами:

- [добавление](#add)
- [copyIndex](#copyindex)
- [div](#div)
- [int](#int)
- [длина](#length)
- [mod (модуль)](#mod)
- [mul](#mul)
- [sub](#sub)


<a id="add" />
### добавление

**add(операнд1, операнд2)**

Возвращает сумму двух указанных целочисленных значений.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| операнд1 | Да | Первый операнд.
| операнд2 | Да | Второй операнд.


<a id="copyindex" />
### copyIndex

**copyIndex(offset)**

Возвращает индекс текущего цикла итерации.

Эта функция всегда используется с объектом **копирования**. Примеры использования **copyIndex** см. в статье [Создание нескольких экземпляров ресурсов в диспетчере ресурсов Azure](resource-group-create-multiple.md).


<a id="div" />
### div

**div(операнд1, операнд2)**

Возвращает целую часть частного от деления двух указанных целочисленных значений.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| операнд1 | Да | Делимое.
| операнд2 | Да | Делитель; должен отличаться от 0.


<a id="int" />
### int

**int(valueToConvert)**

Преобразует указанное значение в целое число.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| valueToConvert | Да | Значение, которое необходимо преобразовать в целое число. Это может быть значение типа String или Integer.

В следующем примере указанное пользователем значение параметра преобразуется в целое число.

    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    }


<a id="length" />
### длина

**length(массив или строка)**

Возвращает количество элементов в массиве или количество знаков в строке. Эту функцию можно использовать с массивом для указания числа итераций при создании ресурсов. В следующем примере параметр **siteNames** ссылается на массив имен для использования при создании веб-сайтов.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

Дополнительные сведения об использовании этой функции с массивом см. в статье [Создание нескольких экземпляров ресурсов в Диспетчере ресурсов Azure](resource-group-create-multiple.md).

Или вы можете использовать ее со строкой:

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "nameLength": "[length(parameters('appName'))]"
    }


<a id="mod" />
### mod (модуль)

**mod(операнд1, операнд2)**

Возвращает остаток от деления двух указанных целочисленных значений.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| операнд1 | Да | Делимое.
| операнд2 | Да | Делитель; должен отличаться от 0.



<a id="mul" />
### mul

**mul(операнд1, операнд2)**

Возвращает произведение двух указанных целочисленных значений.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| операнд1 | Да | Первый операнд.
| операнд2 | Да | Второй операнд.


<a id="sub" />
### sub

**sub(операнд1, операнд2)**

Возвращает разность двух указанных целочисленных значений.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| операнд1 | Да | Уменьшаемое.
| операнд2 | Да | Вычитаемое.


## Строковые функции

Диспетчер ресурсов предоставляет следующие функции для работы со строками:

- [base64](#base64)
- [concat](#concat)
- [padLeft](#padleft)
- [replace](#replace)
- [split](#split)
- [string](#string)
- [substring](#substring)
- [toLower](#tolower)
- [toUpper](#toupper)
- [trim](#trim)
- [uniqueString](#uniquestring)
- [uri](#uri)

Чтобы получить число символов в строке или массиве, см. [length](#length).

<a id="base64" />
### base64

**base64 (входная\_строка)**

Возвращает входную строку в кодировке Base64.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| входная\_строка | Да | Строковое значение, которое нужно вернуть в кодировке Base64.

В следующем примере показано, как использовать функцию Base64.

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

<a id="concat" />
### concat

**concat (аргумент1, аргумент2, аргумент3...)**

Объединяет несколько значений и возвращает результат. Эта функция может принимать любое количество аргументов, а также строки или массивы параметров.

В следующем примере показано, как объединить несколько строк для возврата объединенной строки.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://', reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

В следующем примере показано, как объединить два массива.

    "parameters": {
        "firstarray": {
            type: "array"
        }
        "secondarray": {
            type: "array"
        }
     },
     "variables": {
         "combinedarray": "[concat(parameters('firstarray'), parameters('secondarray'))]
     }
        

<a id="padleft" />
### padLeft

**padLeft (строка\_для\_заполнения, общая\_длина, символ\_заполнения)**

Возвращает выровненную по правому краю строку, добавляя символы в левую часть до достижения общее указанной длины.
  
| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| строка\_для\_заполнения | Да | Строка, предназначенная для выравнивания по правому краю.
| общая\_длина | Да | Общее число символов в возвращаемой строке.
| символ\_заполнения | Да | Символ, используемый для заполнения левой части до достижения общей длины.

В следующем примере показано, как заполнить предоставленное пользователем значение параметра, добавляя символ нуля, пока длина строки не достигнет 10 символов. Если исходное значение параметра длиннее 10 символов, символы не добавляются.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }

<a id="replace" />
### replace

**replace(исходная\_строка, старый\_символ, новый\_символ)**

Возвращает новую строку, где все экземпляры одного символа заменены другим символом.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| исходная\_строка | Да | Строка, в которой все экземпляры одного символа будут заменены другим символом.
| старый\_символ | Да | Символ, который удаляется из исходной строки.
| новый\_символ | Да | Символ, добавляемый вместо удаляемого символа.

В следующем примере показано, как удалить все тире из предоставленной пользователем строки.

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

<a id="split" />
### split

**split(inputString, delimiter)** **split(inputString, [delimiters])**

Возвращает массив строк, содержащий подстроки входной строки, разделенные переданными разделителями.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| входная\_строка | Да | Строка, которую нужно разделить.
| delimiter | Да | Разделитель, который нужно использовать; может быть отдельной строкой или массивом строк.

В следующем примере входная строка разделяется с помощью запятой.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "stringPieces": "[split(parameters('inputString'), ',')]"
    }

<a id="string" />
### строка

**string(valueToConvert)**

Преобразует указанное значение в строку.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| valueToConvert | Да | Значение, которое необходимо преобразовать в строку. Это может быть значение типа Boolean, Integer или String.

В следующем примере указанное пользователем значение параметра преобразуется в строку.

    "parameters": {
        "appId": { "type": "int" }
    },
    "variables": { 
        "stringValue": "[string(parameters('appId'))]"
    }

<a id="substring" />
### substring

**substring(stringToParse, startIndex, length)**

Возвращает подстроку, которая начинается с указанной позиции и содержит указанное количество символов.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| stringToParse | Да | Исходная строка, из которой извлекается подстрока.
| startIndex | Нет | Отсчитываемая от нуля позиция первого знака для подстроки.
| длина | Нет | Число символов в подстроке.

Следующий пример извлекает три первых символа из параметра.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "prefix": "[substring(parameters('inputString'), 0, 3)]"
    }

<a id="tolower" />
### toLower

**toLower(изменяемая\_строка)**

Преобразует указанную строку в нижний регистр.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| изменяемая\_строка | Да | Строка для преобразования в нижний регистр.

В следующем примере предоставленное пользователем значение параметра преобразуется в нижний регистр.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "lowerCaseAppName": "[toLower(parameters('appName'))]"
    }

<a id="toupper" />
### toUpper

**toUpper(изменяемая\_строка)**

Преобразует указанную строку в верхний регистр.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| изменяемая\_строка | Да | Строка для преобразования в верхний регистр.

В следующем примере предоставленное пользователем значение параметра преобразуется в верхний регистр.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "upperCaseAppName": "[toUpper(parameters('appName'))]"
    }

<a id="trim" />
### trim

**trim (stringToTrim)**

Удаляет все начальные и конечные знаки пробела из указанной строки.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| stringToTrim | Да | Строка для усечения.

Следующий пример удаляет пробелы из значения параметра, предоставленного пользователем.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "trimAppName": "[trim(parameters('appName'))]"
    }

<a id="uniquestring" />
### uniqueString

**uniqueString (stringForCreatingUniqueString…)**

Выполняет 64-разрядную хэш-операцию с указанными строками для создания уникальной строки. Эта функция полезна в тех случаях, когда необходимо создать уникальное имя ресурса. Указываются значения параметров, которые представляют уровень уникальности результата. Можно указать, является ли уникальным имя подписки, группы ресурсов или развертывания.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| stringForCreatingUniqueString | Да | Базовая строка, используемая в хэш-функции для создания уникальной строки.
| Дополнительные параметры (если необходимы) | Нет | Можно добавить столько строк, сколько необходимо для создания значения, которое задает уровень уникальности.

Возвращаемое значение — не полностью произвольная строка, а, скорее, результат хэш-функции. Возвращаемое значение содержит 13 знаков. Оно не обязательно будет уникальным глобально. Может потребоваться добавить к значению префикс из вашего соглашения об именовании, чтобы создать более понятное имя.

В следующих примерах показывается, как использовать uniqueString для создания уникального значения для различных часто используемых уровней.

Уникальность в зависимости от подписки

    "[uniqueString(subscription().subscriptionId)]"

Уникальность в зависимости от группы ресурсов

    "[uniqueString(resourceGroup().id)]"

Уникальность в зависимости от развертывания для группы ресурсов

    "[uniqueString(resourceGroup().id, deployment().name)]"
    
В следующем примере показано, как создать уникальное имя учетной записи хранения на основе вашей группы ресурсов.

    "resources": [{ 
        "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]", 
        "type": "Microsoft.Storage/storageAccounts", 
        ...

<a id="uri" />
### uri

**uri (baseUri, relativeUri)**

Создает абсолютный URI, объединяя строки baseUri и relativeUri.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| baseUri | Да | Строка базового универсального кода ресурса (URI).
| relativeUri | Да | Строка относительного универсального кода ресурса (URI), добавляемая к строке базового универсального кода ресурса (URI).

Значение параметра **baseUri** может включать в себя определенный файл, однако при построении универсального кода ресурса (URI) используется только базовый путь. Например, при передаче ****http://contoso.com/resources/azuredeploy.json** в качестве параметра baseUri получается базовый универсальный код ресурса (URI) ****http://contoso.com/resources/**.

В следующем примере показано создание ссылки на вложенный шаблон в зависимости от значения параметра родительского шаблона.

    "templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"

## Функции массивов

Диспетчер ресурсов предоставляет ряд функций для работы с массивами значений.

Чтобы объединить несколько массивов в один, используйте функцию [concat](#concat).

Чтобы получить количество элементов в массиве, используйте функцию [length](#length).

Чтобы разделить строку на массив строк, используйте функцию [split](#split).

## Функции для параметров развертывания

Диспетчер ресурсов предоставляет следующие функции для получения значений из разделов шаблонов и значений, связанных с развертыванием:

- [deployment](#deployment)
- [parameters](#parameters)
- [variables](#variables)

Сведения о получении значений из ресурсов, групп ресурсов или подписки см. в статье [Функции для работы с ресурсами](#resource-functions).

<a id="deployment" />
### deployment

**deployment()**

Возвращает сведения о текущей операции развертывания.

Эта функция возвращает объект, который передается во время развертывания. Свойства в возвращаемом объекте будут зависеть от того, передается ли объект развертывания в виде ссылки или встроенного объекта. Когда объект развертывания передается встроенным, как, например, при использовании параметра **-TemplateFile** в Azure PowerShell для указания на локальный файл, возвращаемый объект имеет следующий формат:

    {
        "name": "",
        "properties": {
            "template": {
                "$schema": "",
                "contentVersion": "",
                "resources": [
                ],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

Когда объект передается в виде ссылки, как, например, при использовании параметра **- TemplateUri** для указания на удаленный объект, объект возвращается в следующем формате.

    {
        "name": "",
        "properties": {
            "templateLink": {
                "uri": "",
                "contentVersion": ""
            },
            "mode": "",
            "provisioningState": ""
        }
    }

В следующем примере показано использование deployment() для ссылки на другой шаблон в зависимости от URI родительского шаблона.

    "variables": {  
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
    }  


<a id="parameters" />
### parameters

**parameters (имя\_параметра)**

Возвращает значение параметра. Указанное имя параметра должно быть определено в разделе параметров шаблона.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| имя\_параметра | Да | Имя параметра, который требуется вернуть.

В следующем примере показано упрощенное использование функции parameters.

    "parameters": { 
      "siteName": {
          "type": "string"
      }
    },
    "resources": [
       {
          "apiVersion": "2014-06-01",
          "name": "[parameters('siteName')]",
          "type": "Microsoft.Web/Sites",
          ...
       }
    ]

<a id="variables" />
### variables

**variables (имя\_переменной)**

Возвращает значение переменной. Указанное имя переменной должно быть определено в разделе переменных шаблона.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| имя\_переменной | Да | Имя переменной, которую необходимо вернуть.



## Функции для работы с ресурсами

Диспетчер ресурсов предоставляет следующие функции для получения значений ресурсов:

- [listkeys](#listkeys)
- [list*](#list)
- [providers](#providers)
- [reference](#reference)
- [resourceGroup](#resourcegroup)
- [resourceId](#resourceid)
- [подписку](#subscription)

Сведения о получении значений параметров, переменных или текущего развертывания см. в разделе [Функции для параметров развертывания](#deployment-value-functions).

<a id="listkeys" />
### listKeys

**listKeys (resourceName или resourceIdentifier, apiVersion)**

Возвращает ключи для любого типа ресурса, который поддерживает операции listKeys. Параметр "идентификатор\_ресурса" можно задать с помощью [функции resourceId](./#resourceid) или с использованием формата **пространство\_имен\_поставщика/тип\_ресурса/имя\_ресурса**. Вы можете использовать эту функцию для получения primaryKey и secondaryKey.
  
| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| имя\_ресурса или идентификатор\_ресурса | Да | Уникальный идентификатор ресурса.
| версия\_API | Да | Версия API для состояния среды выполнения ресурса.

В следующем примере показано, как получить ключи из учетной записи хранения в разделе выходных данных.

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2015-05-01-preview')]", 
        "type" : "object" 
      } 
    } 

<a id="list" />
### list*

**list* (resourceName или resourceIdentifier, apiVersion)**

Любая операция, которая начинается с **list**, может быть использована в шаблоне как функция. Это относится к **listKeys**, как показано выше, но также и к таким операциям, как **list**, **listAdminKeys** и **listStatus**. При вызове функции используйте действительное имя функции, а не list*. Чтобы определить, какие типы ресурсов поддерживают операцию list, используйте следующую команду PowerShell.

    PS C:\> Get-AzureRmProviderOperation -OperationSearchString *  | where {$_.Operation -like "*list*"} | FT Operation

Или получите список с помощью Azure CLI. В следующем примере извлекаются все операции для **apiapps** и используется служебная программа JSON [jq](http://stedolan.github.io/jq/download/), которая отбирает только операции list.

    azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains("list"))"

<a id="providers" />
### providers

**providers (providerNamespace, [resourceType])**

Возвращает сведения о поставщике ресурсов и поддерживаемых им типах ресурсов. Если тип не указан, возвращаются все поддерживаемые типы.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| пространство\_имен\_поставщика | Да | Пространство имен поставщика.
| тип\_ресурса | Нет | Тип ресурса в указанном пространстве имен.

Все поддерживаемые типы возвращаются в следующем формате:

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

Следующий пример показывает, как использовать функцию provider:

    "outputs": {
	    "exampleOutput": {
		    "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
		    "type" : "object"
	    }
    }

<a id="reference" />
### reference

**reference (имя\_ресурса или идентификатор\_ресурса, [версия\_API])**

Позволяет выражению получать значение из состояния среды выполнения другого ресурса.

| Параметр | Обязательно | Описание
| :--------------------------------: | :------: | :----------
| имя\_ресурса или идентификатор\_ресурса | Да | Имя или уникальный идентификатор ресурса.
| версия\_API | Нет | Версия API для указанного ресурса. Если ресурс не предоставляется в рамках того же шаблона, необходимо включить этот параметр.

Функция **reference** получает свое значение из состояния среды выполнения, и поэтому ее невозможно использовать в разделе переменных. Она может использоваться в разделе выходных данных шаблона.

С помощью функции reference вы прямо объявляете, что один ресурс зависит от другого, если ресурс, на который указывает ссылка, предоставляется в том же шаблоне. При этом свойство **dependsOn** использовать не нужно. Расчет функции выполняется только после развертывания ресурса, на который указывает ссылка.

Следующий пример ссылается на учетную запись хранения, которая развертывается в том же шаблоне.

    "outputs": {
		"NewStorage": {
			"value": "[reference(parameters('storageAccountName'))]",
			"type" : "object"
		}
	}

Следующий пример ссылается на учетную запись хранения, которая не развертывается в этом шаблоне, но существует в той же группе ресурсов, что и развертываемые ресурсы.

    "outputs": {
		"ExistingStorage": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15')]",
			"type" : "object"
		}
	}

Можно получить конкретное значение из возвращенного объекта, например URI конечной точки BLOB-объекта, как показано ниже.

    "outputs": {
		"BlobUri": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

Если не требуется непосредственно указывать версию API в шаблоне, вы можете использовать функцию [providers](#providers) и получить одно значение, например последнюю версию, как показано ниже.

    "outputs": {
		"BlobUri": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).primaryEndpoints.blob]",
			"type" : "string"
		}
	}

Следующий пример ссылается на учетную запись хранения в другой группе ресурсов.

    "outputs": {
		"BlobUri": {
			"value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

<a id="resourcegroup" />
### resourceGroup

**resourceGroup()**

Возвращает структурированный объект, представляющий текущую группу ресурсов. Формат объекта будет следующим:

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
    }

В следующем примере расположение группы ресурсов используется для назначения расположения веб-сайту.

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

<a id="resourceid" />
### resourceId

**resourceId ([имя\_группы\_ресурсов], тип\_ресурса, имя\_ресурса1, [имя\_ресурса2]...)**

Возвращает уникальный идентификатор ресурса. Используйте эту функцию в том случае, когда имя ресурса является неоднозначным или не было предоставлено в пределах того же шаблона. Идентификатор возвращается в следующем формате:

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}
      
| Параметр | Обязательно | Описание
| :---------------: | :------: | :----------
| имя\_группы\_ресурсов | Нет | Необязательное имя группы ресурсов. Значение по умолчанию — текущая группа ресурсов. Укажите это значение, если вы получаете ресурс из другой группы ресурсов.
| тип\_ресурса | Да | Тип ресурса, включая пространство имен поставщика ресурсов.
| имя\_ресурса1 | Да | Имя ресурса.
| имя\_ресурса2 | Нет | Имя следующего ресурса, если ресурс является вложенным.

В следующем примере показано, как получить идентификаторы ресурсов для веб-сайта и базы данных. Веб-сайт находится в группе ресурсов с именем **myWebsitesGroup**, а база данных — в текущей группе ресурсов для этого шаблона.

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'),parameters('databaseName'))]
    
Эта функция часто необходима при использовании учетной записи хранения или виртуальной сети в альтернативной группе ресурсов. Учетная запись хранения и виртуальная сеть могут использоваться несколькими группами ресурсов, поэтому их не следует удалять при удалении одной группы ресурсов. В следующем примере показано, как ресурс из внешней группы ресурсов можно легко использовать:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]
    }

<a id="subscription" />
### подписку

**subscription()**

Возвращает сведения о подписке в следующем формате:

    {
        "id": "/subscriptions/#####"
        "subscriptionId": "#####"
    }

В следующем примере показана функция subscription, вызываемая в разделе выходных данных.

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 


## Дальнейшие действия
- Описание разделов в шаблоне диспетчера ресурсов Azure см. в статье [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).
- Инструкции по объединению нескольких шаблонов см. в статье [Использование связанных шаблонов в диспетчере ресурсов Azure](resource-group-linked-templates.md).
- Указания по выполнению заданного количества циклов итерации при создании типа см. в статье [Создание нескольких экземпляров ресурсов в Диспетчере ресурсов Azure](resource-group-create-multiple.md).
- Указания по развертыванию созданного шаблона см. в статье [Развертывание приложения с использованием шаблона Диспетчера ресурсов Azure](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0224_2016-->