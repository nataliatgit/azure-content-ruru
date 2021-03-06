<properties
	pageTitle="Импорт данных в Студию машинного обучения | Microsoft Azure"
	description="Импорт данных в студию машинного обучения Azure из разных источников данных Узнайте, какие типы данных и форматы данных поддерживаются."
	keywords="импорт данных, формат данных, типы данных, источники данных, обучающие данные"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="garye;bradsev" />


# Импорт обучающих данных в Студию машинного обучения Azure из разных источников данных

## Введение

Чтобы при разработке решения прогнозной аналитики и обучении работе с таким решением использовать в студии машинного обучения собственные данные, можно выполнить следующие действия:

- Опережающая передача данных из **локального файла** на жестком диске для создания модуля набора данных в рабочей области.  
- Доступ к данным из одного из нескольких **источников данных в Интернете** во время запуска эксперимента с помощью модуля [чтения][reader]. 
- Использование данных из другого эксперимента машинного обучения Azure, сохраненных в виде **набора данных**. 

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

Каждое из этих действий подробно описано в разделах, перечисленных в приведенном выше меню. В этом разделе показано, как импортировать в студию машинного обучения данные из различных источников.

> [AZURE.NOTE] В студии машинного обучения Microsoft Azure есть множество примеров наборов данных, которые можно использовать для этой цели. Дополнительные сведения см. в разделе [Использование образцов наборов данных в студии машинного обучения Azure](machine-learning-use-sample-datasets.md)).

Вводный раздел описывает также подготовку данных для использования в студии машинного обучения, а также поддерживаемые форматы и типы данных.

> [AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## Подготовка данных для использования в студии машинного обучения Azure.
Студия машинного обучения Microsoft Azure предназначена для работы с прямоугольными массивами или таблицами данных, такими как текстовые данные с разделителями или структурированные данные из базы данных, хотя в некоторых случаях могут быть использованы данные из непрямоугольных массивов.

Лучше, если данные будут относительно чистыми. То есть, перед передачей данных в эксперимент необходимо позаботиться о трудностях, например строках без кавычек.

Тем не менее в студии машинного обучения Microsoft Azure доступны модули, позволяющие выполнить некоторую обработку данных в рамках вашего эксперимента. В зависимости от алгоритмов машинного обучения, которые вы будете использовать, необходимо решить, как будут обрабатываться структурные трудности в данных, например отсутствующие значения и разреженные данные, и существуют ли модули, которые могут здесь помочь. В разделе **Преобразование данных** палитры модулей найдите модули, которые выполняют такие функции.

В любой точке вашего эксперимента можно просмотреть или загрузить данные, созданные модулем, щелкнув правой кнопкой мыши порт вывода. В зависимости от модуля могут быть доступны разные варианты загрузки или вы сможете просмотреть данные в веб-браузере в Студии машинного обучения Microsoft Azure.

## Поддерживаемые форматы и типы данных

В свой эксперимент можно импортировать значительное количество типов данных, в зависимости от того, какая система используется для импорта данных и каков источник этих данных:

- Обычный текст (TXT)
- Текст с разделителями-запятыми с заголовком (CSV) или без заголовка (NH.CSV)
- Текст с разделителями-табуляциями с заголовком (TSV) или без заголовка (NH.TSV)
- Таблица Hive
- Таблицы базы данных SQL
- Значения OData
- Данные SVMLight (SVMLIGHT) (подробнее о формате см. в [определении SVMLight](http://svmlight.joachims.org/))
- Данные в формате ARFF (подробнее о формате см. в [определении ARFF](http://weka.wikispaces.com/ARFF))
- ZIP-файл (ZIP)
- Файл объекта или рабочей области R (RData)

При импорте данных в содержащем метаданные формате (например ARFF) Студия машинного обучения Microsoft Azure использует эти метаданные для определения заголовка и типа данных каждого столбца.

При импорте данных в формате, например, TSV или CSV, который не содержит этих метаданных, Студия машинного обучения Microsoft Azure при выборке данных пытается определить тип данных для каждого столбца. Если данные не содержат заголовки столбцов, Студия машинного обучения Microsoft Azure использует имена по умолчанию.

Вы можете явно указать или изменить заголовки и типы данных для столбцов с помощью [Редактора метаданных][metadata-editor].

Студия машинного обучения распознает следующие **типы данных**:

- Строка
- Число
- Double
- Логический
- DateTime
- TimeSpan

Для передачи данных между модулями Студия машинного обучения Microsoft Azure использует внутренний тип данных, который называется ***Data Table***. Данные можно явно преобразовать в формат Data Table с использованием модуля [Преобразование в набор данных][convert-to-dataset].

Любой модуль, который принимает форматы, отличные от Data Table, перед передачей данных в следующий модуль преобразует данные в формат Data Table без вмешательства пользователя.

При необходимости можно преобразовать данные Data Table обратно в формат CSV, TSV, ARFF или SVMLight, используя другие модули преобразования. Узнать о модулях, которые выполняют эти функции, можно узнать в разделе **Преобразование форматов данных** палитры модулей.



<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0211_2016-->