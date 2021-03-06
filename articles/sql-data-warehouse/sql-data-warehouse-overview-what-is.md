<properties
   pageTitle="Общие сведения о хранилище данных SQL Azure | Microsoft Azure"
   description="Распределенная база данных корпоративного класса, способная обрабатывать петабайты реляционных и нереляционных данных. Это первое облачное хранилище в отрасли, в котором можно увеличивать и уменьшать количество вычислительных ресурсов, а также приостанавливать их работу в считанные секунды."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;"/>


# Что такое хранилище данных SQL Azure?

Хранилище данных SQL Azure — это распределенная база данных корпоративного класса, способная обрабатывать большие объемы реляционных и нереляционных данных. Это первое облачное хранилище в отрасли, которое сочетает в себе проверенные возможности SQL и способность увеличивать и уменьшать количество вычислительных ресурсов, а также приостанавливать их работу в считанные секунды. Хранилище данных SQL тесно взаимодействует с Azure, и его можно с легкостью развернуть в считанные секунды. Кроме того, эта служба полностью управляемая, а значит вам не придется тратить время на исправление и обслуживание программного обеспечения, а также на резервное копирование. Благодаря встроенным функциям резервного копирования, которое выполняется автоматически, хранилище данных SQL обеспечивает отказоустойчивость и автоматическое резервное копирование и самостоятельное восстановление.

Чтобы воспользоваться всеми преимуществами Azure и создать хранилище данных, которое справится с любой корпоративной рабочей нагрузкой, при создании хранилища данных SQL мы уделили особое внимание нескольким ключевым атрибутам.

## Оптимизация

### Архитектура хранилища данных

В основе хранилища данных SQL лежит архитектура массовой параллельной обработки Майкрософт, изначальное предназначение которой — работа с локальными корпоративными хранилищами данных больших размеров. В этой архитектуре используются встроенные функции повышения производительности хранилища данных SQL. Она обеспечивает легкое масштабирование и выполнение параллельных вычислений для сложных SQL-запросов. Кроме этого, архитектура хранилища данных SQL позволяет использовать преимущества ее взаимодействия с Azure. На основе этих аспектов можно выделить 4 ключевых компонента архитектуры:

![Архитектура хранилища данных SQL][1]

- **Управляющий узел.** Использование хранилища данных SQL для разработки, загрузки, а также работы со средствами бизнес-аналитики предусматривает подключение к управляющему узлу. Управляющий узел в хранилище данных SQL — это база данных SQL, и подключение к нему ничем не отличается от подключения к стандартной базе данных SQL. Тем не менее, он скрыто координирует все перемещения и вычисления данных в системе. Выполнение команды на управляющем узле влечет ее разбивку на набор запросов, передаваемых на вычислительные узлы службы.

- **Вычислительные узлы:** вычислительные узлы хранилища данных SQL, подобно управляющему узлу, используют базы данных SQL. Их задача состоит в обеспечении вычислительной мощности службы. Независимо от сценария при каждой загрузке данных в хранилище данных SQL происходит их распределение по узлам службы. Получив команду, управляющий узел разбивает ее на фрагменты для каждого вычислительного узла, которые, в свою очередь, выполняют операции с соответствующими данными. По завершении вычисления эти узлы передают частичные результаты на управляющий узел, который объединяет их и возвращает ответ.

- **Хранилище.** Все данные хранилища данных SQL хранятся в стандартных больших двоичных объектах Azure. Это означает, что при взаимодействии с данными вычислительные узлы выполняют запись напрямую в большие двоичные объекты или чтение из них. Служба хранилища Azure предусматривает прозрачное и практически неограниченное увеличение, что позволяет масштабировать хранилище автоматически и отдельно от вычислений. Кроме того, служба хранилища Azure позволяет сохранить хранилище при масштабировании или приостановке его использования, а также оптимизировать резервное копирование и восстановление, тем самым обеспечивая дополнительную надежность и отказоустойчивость хранилища.

- **Службы перемещения данных.** Последним объединяющим компонентом хранилища данных SQL являются службы перемещения данных. Они позволяют управляющему узлу взаимодействовать со всеми вычислительными узлами и передавать на них данные. Кроме того, с их помощью вычислительные узлы могут осуществлять передачу данных между собой, что предоставляет им доступ к данным, которые хранятся на других узлах, и позволяет получить данные, необходимые для соединения и объединения.

### Оптимизированный механизм

Архитектура массовой параллельной обработки позволяет хранилищу данных SQL использовать описанный выше подход "разделяй и властвуй" для решения проблем с объемными данными. Так как данные в хранилище данных SQL разделяются и распространяются по вычислительным узлам служб, каждый такой узел может работать с частью данных в параллельном режиме. Наконец, узлы передают результаты на управляющий узел, после чего происходит их объединение и передача пользователям. Этот подход также предоставляет ряд преимуществ оптимизации производительности в зависимости от хранилища данных:

- Хранилище данных SQL использует расширенный оптимизатор запросов и сложный статистический набор для всех данных службы, чтобы создавать планы запросов. Информация о размере данных и их распространении помогает службе оптимизировать распределенные запросы. Для этого используется оценка стоимости определенных операций с запросами.

- Помимо создания оптимальных планов запросов, в хранилище данных SQL также используются дополнительные алгоритмы и методы, которые эффективно перемещают данные между вычислительными ресурсами в зависимости от потребности выполнения запроса. Эти операции встроены в службы перемещения данных хранилища. При этом оптимизация происходит автоматически.

- Кластеризованные индексы columnstore в хранилище данных SQL также позволяют добиться высокой скорости выполнения запросов. В хранилище данных SQL, где принцип хранения данных основан на столбцах, обеспечивается пятикратное увеличение сжатия данных (по сравнению с хранилищем с традиционным построчным хранением) и десятикратное увеличение производительности запросов. Запросы к хранилищу данных прекрасно работают с индексами columnstore, так как они часто сканируют всю таблицу или весь раздел таблицы, а также уменьшают влияние переноса данных на этапы запроса.

## Масштабируемость

Архитектура хранилища данных SQL разделяет хранилище и вычислительные ресурсы, что позволяет масштабировать их независимо друг от друга. Благодаря структуре Базы данных SQL, обеспечивающей быстрое и простое развертывание, дополнительные вычисления можно выполнять в кратчайшие сроки. Это преимущество дополняет использование больших двоичных объектов службы хранилища Azure. Большие двоичные объекты не только являются залогом стабильного реплицированного хранилища, но также формируют инфраструктуру для легкого расширения по низкой цене. Использование масштабируемого облачного хранилища вместе с вычислительными ресурсами Azure в хранилище данных SQL позволяет платить за использование хранилища запросов по мере необходимости. Изменить объем вычислительных ресурсов можно простым перемещением ползунка на классическом портале Azure. Кроме того, ресурсы можно запланировать или добавить к рабочей нагрузке, используя T-SQL и PowerShell.

Благодаря возможности полностью контролировать вычислительные ресурсы независимо от хранилища хранилище данных SQL позволяет вам полностью приостановить использование своего хранилища данных. При этом хранилище останется на своем месте, а все вычисления мгновенно помещаются в основной пул Azure, что обеспечивает экономию денег. При необходимости просто возобновите использование вычислительных ресурсов и сделайте свои данные и вычислительные ресурсы доступными для рабочей нагрузки.

Использование вычислительных ресурсов в хранилище данных SQL измеряется в единицах использования хранилища данных SQL (DWU). DWU — это единица измерения базовой мощности хранилища данных, разработанная для постоянного обеспечения стандартного уровня производительности хранилища. DWU, в частности, используется для обеспечения следующих преимуществ:

- возможность эффективного масштабирования хранилища, не беспокоясь о базовом оборудовании или программном обеспечении;

- возможность сформировать представление о производительности на основе уровня DWU до изменения размера хранилища данных;

- изменение или перемещение базового оборудования и программного обеспечения экземпляра без влияния на производительность при рабочей нагрузке;

- настройка базовой архитектуры службы без влияния на производительность при рабочей нагрузке;

- повышение производительности, обеспечивающее масштабируемость системы и равномерное влияние (благодаря возможности быстрого повышения производительности в хранилище данных SQL).

### Единицы использования хранилища данных

Мы рассмотрим единицы использования хранилища данных в качестве единиц измерения трех точных метрик, которые тесно связаны с производительностью при рабочей нагрузке хранилища данных. Наша цель — линейное масштабирование метрик рабочей нагрузки в соответствии с выбранными для хранилища данных DWU, что позволит нам обеспечить общую доступность.

**Сканирование или объединение.** Эта метрика рабочей нагрузки использует стандартный запрос хранения данных, который сканирует большое количество строк, а затем выполняет сложное объединение. Эта операция требует большого количества операций ввода-вывода и интенсивного использования ЦП.

**Загрузка.** Эта метрика отображает возможность приема данных в службе. Во время завершения загрузки PolyBase загружает репрезентативный набор данных из большого двоичного объекта службы хранилища Azure. Эта метрика предназначена для тестирования нагрузки сети и ЦП службы.

**CREATE TABLE AS SELECT.** CREATE TABLE AS SELECT измеряет возможность создания копии таблицы. Эта функция предусматривает чтение данных из хранилища, их распространение по различным узлам устройства и повторную запись в хранилище. Она требует интенсивного использования ЦП и сети.

### Выбор времени масштабирования

В целом DWU предназначены для простоты использования. Если вы хотите получить результаты быстрее, увеличьте количество DWU за отдельную оплату. Если вам не нужна большая вычислительная мощность, уменьшите количество DWU и оплачивайте только необходимые ресурсы. Вот несколько случаев, в которых следует рассматривать изменение количества DWU:

- В периоды, когда вам не нужно выполнять запросы, например вечером или в выходные дни, приостановите работу вычислительных ресурсов. Все выполняемые запросы будут отменены, а все DWU, выделенные для хранилища данных, — освобождены.

- Если при выполнении ресурсоемкой загрузки данных или их преобразовании нужно увеличить масштаб, чтобы получить данные быстрее.

- Чтобы определить оптимальное значение DWU, попробуйте увеличить и уменьшить масштаб, а также выполнить несколько запросов после загрузки данных. Так как масштабирование выполняется достаточно быстро, попробуйте использовать разные уровни производительности не дольше одного часа.

> [AZURE.NOTE] Обратите внимание, что в связи с архитектурой или хранилищем данных SQL ожидаемое увеличение масштаба производительности может не произойти при небольшом объеме данных. Чтобы получить точные результаты тестирования производительности, мы советуем использовать 1 TБ в качестве начального объема.

## Интеграция

Хранилище данных SQL работает на основе проверенного реляционного ядра СУБД и предусматривает множество функций, необходимых для работы с корпоративным хранилищем данных. Если вы уже знакомы с Transact-SQL, вы можете легко применить свои знания при работе с хранилищем данных SQL. Примеры в документации помогут вам начать работу, независимо от того, есть ли у вас опыт или вы только приступаете к работе с хранилищем. В целом при разработке элементов языка для хранилища данных SQL мы предусмотрели следующие принципы:

- Хранилище данных SQL использует синтаксис Transact-SQL сервера SQL Server для многих операций и поддерживает широкий набор традиционных конструкций SQL, таких как хранимые процедуры, пользовательские функции, секционирование таблиц, индексы и параметры сортировки.

- Хранилище данных SQL также включает в себя ряд передовых функций SQL Server, в том числе кластеризованные индексы columnstore, интеграцию PolyBase и аудит данных (предусматривающий также оценку угроз).

- Так как хранилище данных SQL еще находится на стадии разработки, некоторые элементы языка Transact-SQL используются не во всех рабочих нагрузках хранилищ данных, а только в более новых версиях SQL Server, которые сейчас могут быть недоступны. Дополнительные сведения по этой теме см. в документации по миграции.

Используя Transact-SQL и схожесть функций между SQL Server, хранилищем данных SQL, базой данных SQL и системой аналитических платформ, можно разрабатывать решения, которые соответствуют вашим требованиям к хранению и обработке данных. Вы можете выбрать место для хранения данных, исходя из требований к производительности, безопасности и масштабированию, а затем переносить данные между разными системами.

В дополнение к использованию контактной зоны Transact-SQL хранилище данных SQL можно интегрировать с многими инструментами, уже знакомыми пользователям SQL Server. В частности, мы предусмотрели интеграцию хранилища данных SQL с инструментами нескольких категорий, среди которых:

**Традиционные инструменты SQL Server.** Доступна полная интеграция служб SQL Server Analysis Services, служб интеграции и Reporting Services с хранилищем данных SQL.

**Облачные инструменты.** Хранилище данных SQL можно использовать с рядом новых инструментов в Azure. Кроме этого, оно тесно интегрировано с фабрикой данных Azure, Stream Analytics, машинным обучением и Power BI.

**Сторонние инструменты.** Сертифицированную интеграцию с хранилищем данных SQL поддерживает большое количество сторонних инструментов. Просмотрите полный список.

## Гибридная среда

Хранилище данных SQL с PolyBase позволяет переносить данные в пределах своей экосистемы, что ранее было недоступно. Это, в свою очередь, позволяет настраивать расширенные гибридные сценарии, предусматривающие использование нереляционных локальных источников данных.

Технология PolyBase проста в применении и обеспечивает использование данных из различных источников, выполняя уже знакомые команды T-SQL. PolyBase позволяет отправлять запросы на нереляционные данные, которые находятся в хранилище BLOB-объектов Azure, как если бы они находились в обычной таблице. С помощью Polybase можно отправлять запросы к нереляционным данным или импортировать такие данные в хранилище данных SQL.

- Для доступа к нереляционным данным технология PolyBase использует внешние таблицы. Определения таблиц находятся в хранилище данных SQL. Их можно открывать с помощью SQL и любых других инструментов, подобно обычным реляционным данным.

- Интеграция Polybase не зависит используемых технологий. PolyBase предоставляет одинаковые компоненты и функциональные возможности для всех поддерживаемых ресурсов. Технология Polybase позволяет читать данные в различных форматах, включая файлы с разделителями и ORC-файлы.

- PolyBase можно использовать для доступа к хранилищу BLOB-объектов, которое также может служить хранилищем кластера HD Insight, предоставляя новейшие методы доступа к одним и тем же данным как с помощью реляционных, так и нереляционных инструментов.


## Дальнейшие действия

Теперь, когда вам уже известны некоторые сведения о хранилище данных SQL, просмотрите дополнительные сведения о [рабочей нагрузке хранилища данных], [подготовке к работе] и загрузке [демонстрационных данных], чтобы приступить к работе.

>[AZURE.NOTE] Мы хотим улучшить эту статью. Если на вопрос "Помогла ли вам эта статья?" вы выберите "нет", добавьте краткую рекомендацию о том, чего в ней не хватает или как ее улучшить. Заранее спасибо!

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[рабочей нагрузке хранилища данных]: ./sql-data-warehouse-overview-workload.md
[демонстрационных данных]: ./sql-data-warehouse-get-started-load-samples.md
[подготовке к работе]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0309_2016-->