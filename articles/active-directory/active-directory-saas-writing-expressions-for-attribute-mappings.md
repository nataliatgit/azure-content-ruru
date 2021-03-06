<properties
	pageTitle="Запись выражений для сопоставления атрибутов в Azure Active Directory | Microsoft Azure"
	description="Узнайте, как использовать сопоставление выражений для преобразования значений атрибутов в допустимый формат при автоматической подготовке объектов приложения SaaS в Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="markusvi"/>


# Запись выражений для сопоставления атрибутов в Azure Active Directory

При настройке подготовки для приложения SaaS одним из типов сопоставления атрибутов, которые можно указать, является сопоставление выражений. Для этого необходимо написать выражение, похожее на скрипт. Оно позволит вам преобразовать данные пользователей в форматы, более подходящие для приложений SaaS.





## Общие сведения о синтаксисе

Синтаксис выражений для сопоставления атрибутов напоминает функции Visual Basic для приложений (VBA).

- Все выражение должно определяться функциями, состоящими из имени и следующих за ним в скобках аргументов: <br> *имя\_функции (<<argument 1>>, <<argument N>>)*.


- Функции можно вкладывать одну в другую. Например: <br> *функция\_один(функция\_два(<<argument1>>))*.


- В функцию можно передавать следующие три типа аргументов.

   1. Атрибуты, которые должны быть заключены в квадратные скобки. Например: [имя\_атрибута].

   2. Строковые константы, которые должны быть заключены в двойные кавычки. Например: "США".

   3. Другие функции. Например: функция\_один(<<argument1>>, функция\_два(<<argument2>>)).


- Если в строковых константах необходимо использовать обратную косую черту (\\) или кавычки (""), такие символы следует экранировать обратной косой чертой (\\). Например: "Название компании: "Contoso"".



## Список функций

[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)





----------
### Добавить

**Функция:**<br> Append(source, suffix)

**Описание:**<br> Получает исходное строковое значение и присоединяет к его концу суффикс.
 
**Параметры:**<br>

|Имя| Обязательно/повторяется | Тип | Примечания |
|--- | ---                 | ---  | ---   |
| **источник** | Обязательно | Строка | Как правило, имя атрибута из исходного объекта. |
| **суффикс** | Обязательно | Строка | Строка, которую необходимо присоединить к концу исходного значения. |


----------
### FormatDateTime

**Функция:**<br> FormatDateTime(source, inputFormat, outputFormat)

**Описание:**<br> Преобразовывает строку даты из одного формата в другой.
 
**Параметры:**<br>

|Имя| Обязательно/повторяется | Тип | Примечания |
|--- | ---                 | ---  | ---   |
| **источник** | Обязательно | Строка | Как правило, имя атрибута из исходного объекта. |
| **входной\_формат** | Обязательно | Строка | Предполагаемый формат исходного значения. Список поддерживаемых форматов см. на странице [http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **выходной\_формат** | Обязательно | Строка | Формат вывода даты. |



----------
### Join

**Функция:**<br> Join(separator, source1, source2, …)

**Описание:**<br> Функция Join() схожа с функцией Append() за исключением того, что она может объединять несколько строковых значений **source** в одну строку. При этом каждое значение разделяется строкой **separator**.

Если одно из исходных значений является атрибутом с несколькими значениями, все значения в этом атрибуте будут объединены, а между ними будут значения разделителя.

 
**Параметры:**<br>

|Имя| Обязательно/повторяется | Тип | Примечания |
|--- | ---                 | ---  | ---   |
| **разделитель** | Обязательно | Строка | Строка, используемая для разделения исходных значений, когда они объединяются в одну строку. Если разделитель не требуется, может не иметь значения (""). |
| ****source1 … sourceN** | Обязательно; количество повторов может меняться | Строка | Строковые значения для объединения. |



----------
### Mid

**Функция:**<br> Mid(source, start, length)

**Описание:**<br> Возвращает подстроку исходного значения. Подстрокой является строка, содержащая только некоторые символы из исходной строки.


**Параметры:**<br>

|Имя| Обязательно/повторяется | Тип | Примечания |
|--- | ---                 | ---  | ---   |
| **источник** | Обязательно | Строка | Как правило, имя атрибута. |
| **start** | Обязательно | целое число | Индекс положения в строке **source**, откуда должна начинаться подстрока. Первый символ в строке будет иметь индекс 1, второй символ — индекс 2 и т. д. |
| **длина** | Обязательно | целое число | Длина подстроки. Если длина превышает размер **исходной** строки, функция возвращает подстроку из **начала** индекса и до конца **исходной** строки. |




----------
### Not

**Функция:**<br> Not(источник)

**Описание:**<br> Изменяет логическое значение **source**. Если значение **source** равно *True*, возвращает значение *False*. В противном случае возвращает значение *True*.


**Параметры:**<br>

|Имя| Обязательно/повторяется | Тип | Примечания |
|--- | ---                 | ---  | ---   |
| **источник** | Обязательно | Логическая строка | Предполагаемые значения **source**: True или False. |



----------
### Замените

**Функция:**<br> ObsoleteReplace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**Описание:**<br> Заменяет значения в пределах строки. Ее работа зависит от указанных параметров.

- Если указаны параметры **oldValue** и **replacementValue**:

   - заменяет все вхождения старого значения в источнике значениями замены.

- Если указаны параметры **oldValue** и **template**:

   - заменяет все экземпляры **oldValue** в **template** значением **source**.

- Если указаны параметры **oldValueRegexPattern**, **oldValueRegexGroupName**, **replacementValue**:

   - заменяет все значения, сопоставимые с шаблоном старого значения регулярного выражения в исходной строке, значением замены.

- Если указаны параметры **oldValueRegexPattern**, **oldValueRegexGroupName**, **replacementPropertyName**:

   - Если **source** имеет значение, возвращает **source**.

   - Если **source** не имеет значения, используется **oldValueRegexPattern** и **oldValueRegexGroupName** для извлечения значения замены из свойства с помощью параметра **replacementPropertyName**. В качестве результата возвращается значение замены.


**Параметры:**<br>

|Имя| Обязательно/повторяется | Тип | Примечания |
|--- | ---                 | ---  | ---   |
| **источник** | Обязательно | Строка | Как правило, имя атрибута из исходного объекта. |
| **старое\_значение** | Необязательно | Строка | Значение для замены в **source** или **template**. |
| **шаблон\_регулярного\_выражения** | Необязательно | Строка | Шаблон регулярного выражения для значения, заменяемого в **source**. Или, при использовании имени свойства замены, шаблон для извлечения значения из свойства замены. |
| **имя\_группы\_регулярного\_выражения** | Необязательно | Строка | Имя группы в **regexPattern**. Значение этой группы будет извлечено из свойства замены как значение замены, только если используется имя свойства замены. |
| **значение\_замены** | Необязательно | Строка | Новое значение для замены старого. |
| **имя\_атрибута\_замены** | Необязательно | Строка | Имя атрибута, используемого для значения замены, если источник не имеет значение. |
| **шаблон** | Необязательно | Строка | Когда указано значение **template**, будет выполнен поиск **oldValue** в шаблоне с последующей заменой исходным значением. |



----------
### StripSpaces

**Функция:**<br> StripSpaces(source)

**Описание:**<br> Удаляет все пробелы (« ») из исходной строки.

**Параметры:**<br>

|Имя| Обязательно/повторяется | Тип | Примечания |
|--- | ---                 | ---  | ---   |
| **источник** | Обязательно | Строка | Значение **source**, которое необходимо обновить. |



----------
### Switch

**Функция:**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**Описание:**<br> Если значение **source** соответствует **key**, возвращается **value** для **key**. Если значение **source** не соответствует ни одному параметру, возвращается **defaultValue**. Параметры **key** и **value** должны всегда быть парными. Для функции необходимо всегда использовать четное количество параметров.

**Параметры:**<br>

|Имя| Обязательно/повторяется | Тип | Примечания |
|--- | ---                 | ---  | ---   |
| **источник** | Обязательно | Строка | Значение **source**, которое необходимо обновить. |
| **defaultValue** | Необязательно | Строка | Значение по умолчанию, которое необходимо использовать, если источник не соответствует ни одному ключу. Может быть пустой строкой (""). |
| **key** | Обязательно | Строка | **Key**, с которым сравнивается значение **source**. |
| **значение** | Обязательно | Строка | Значение, которым заменяется значение **source**, соответствующее ключу. |



## Примеры

### Извлечение известного доменного имени

Необходимо извлечь известное доменное имя из адреса электронной почты пользователя, чтобы получить имя пользователя. <br> Например, если домен — contoso.com, можно использовать следующее выражение:


**Выражение:** <br> `Replace([mail], "@contoso.com", , ,"", ,)`

**Пример ввода и вывода:** <br>

- **ВВОД**: (mail): "john.doe@contoso.com"

- **ВЫВОД**: john.doe


### Добавление суффикса константы к имени пользователя

При использовании Salesforce Sandbox может потребоваться добавить дополнительный суффикс ко всем именам пользователей перед их синхронизацией.




**Выражение:** <br> `Append([userPrincipalName], ".test"))`

**Пример ввода и вывода:**<br>

- **ВВОД**: (userPrincipalName): "John.Doe@contoso.com"


- **ВЫВОД**: «John.Doe@contoso.com.test»





### Создание псевдонима пользователя путем сцепления частей имени и фамилии

Необходимо создать псевдоним пользователя, используя первые три буквы имени и первые пять букв фамилии пользователя.


**Выражение:** <br> `Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Пример ввода и вывода:** <br>

- **ВВОД**: (givenName): "John"

- **ВВОД**: (surname): "Doe"

- **ВЫВОД**: "JohDoe"




### Вывод даты в виде строки в определенном формате

Необходимо отправить в приложение SaaS даты в определенном формате. <br> Например, требуется отформатировать даты для ServiceNow.



**Выражение:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Пример ввода и вывода:**

- **ВВОД**: (extensionAttribute1): "20150123105347.1Z"

- **ВЫВОД**: "2015-01-23"





### Замена значения на основе предопределенного набора параметров

Необходимо определить часовой пояс пользователя на основе кода государства, сохраненного в Azure AD. <br> Если код государства не совпадает с предопределенными параметрами, используйте значение по умолчанию «Australia/Sydney».


**Выражение:** <br>

`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Пример ввода и вывода:**

- **ВВОД**: (state): "QLD"

- **ВЫВОД**: "Australia/Brisbane"


##Связанные статьи

- [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)
- [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS](active-directory-saas-app-provisioning.md)
- [Настройка сопоставления атрибутов для подготовки пользователей](active-directory-saas-customizing-attribute-mappings.md)
- [Фильтры области для подготовки пользователей](active-directory-saas-scoping-filters.md)
- [Автоматическая подготовка пользователей и групп из Azure Active Directory в приложениях с использованием SCIM](active-directory-scim-provisioning.md)
- [Уведомления о подготовке учетных записей](active-directory-saas-account-provisioning-notifications.md)
- [Список учебников по интеграции приложений SaaS](active-directory-saas-tutorial-list.md)

<!---HONumber=AcomDC_0211_2016-->