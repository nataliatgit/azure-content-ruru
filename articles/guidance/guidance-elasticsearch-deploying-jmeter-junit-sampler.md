<properties
   pageTitle="Развертывание дискретизатора JUnit для JMeter для тестирования производительности Elasticsearch | Microsoft Azure"
   description="Руководство по использованию дискретизатора JUnit для создания и отправки данных в кластер Elasticsearch."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Развертывание дискретизатора JUnit для JMeter при тестировании производительности Elasticsearch

Этот материал входит в [цикл статей, посвященных вопросам Elasticsearch](guidance-elasticsearch.md).

В этом документе описывается, как создать и использовать дискретизатор JUnit для создания и отправки данных в кластер Elasticsearch в рамках плана тестирования JMeter. Это исключительно гибкий подход к нагрузочному тестированию, позволяющий создавать большие объемы тестовых данных без использования внешних файлов.

> [AZURE.NOTE] С помощью этого подхода разработаны нагрузочные тесты, используемые для оценки производительности приема данных, описанной в документе "Максимальное увеличение производительности приема данных с помощью Elasticsearch в Azure". В этом документе представлены сведения о коде JUnit.

Для тестирования производительности приема данных код JUnit разрабатывался в Eclipse (Mars), а зависимости определялись в Maven. Ниже описан пошаговый процесс установки Eclipse, настройки Maven, создания теста JUnit и его развертывания как дискретизатора запросов JUnit в тесте JMeter.

> [AZURE.NOTE] Подробные сведения о структуре и конфигурации тестовой среды см. в статье [Создание среды тестирования производительности для Elasticsearch в Azure][].

## Установка необходимых компонентов

На компьютер разработки нужно установить [среду выполнения Java](http://www.java.com/en/download/ie_manual.jsp). Вам потребуется установить также [интегрированную среду разработки Eclipse для разработчиков Java](https://www.eclipse.org/downloads/index.php?show_instructions=TRUE).

> [AZURE.NOTE] Если в качестве среды разработки используется главная виртуальная машина JMeter, описанная в статье [Создание среды тестирования производительности для Elasticsearch в Azure][], скачайте установщик Eclipse для 32-разрядной версии Windows.

## Создание проекта JUnit для нагрузочного тестирования Elasticsearch

Запустите интегрированную среду разработки Eclipse, если она еще не запущена, и закройте страницу *приветствия*. В меню *Файл* выберите команду *Создать*, а затем — пункт *Проект Java*.

![](./media/guidance-elasticsearch/jmeter-deploy7.png)

В окне *Создание проекта Java* введите имя проекта, щелкните *Использовать среду JRE по умолчанию* и нажмите кнопку *Готово*.

![](./media/guidance-elasticsearch/jmeter-deploy8.png)

В окне *Обозреватель пакетов* разверните узел с именем проекта. Убедитесь, что он содержит папку с именем *src* и ссылку на указанную среду JRE.

![](./media/guidance-elasticsearch/jmeter-deploy9.png)

Щелкните правой кнопкой мыши папку *src*, последовательно выберите *Создать*, *Тестовый случай JUnit*.

![](./media/guidance-elasticsearch/jmeter-deploy10.png)

В окне *Создание тестового случая JUnit* установите переключатель *Создать тест Junit 4*, введите имя пакета (оно может совпадать с именем проекта, но должно начинаться со строчной буквы) и имя тестового класса, а затем выберите параметры создания заглушек метода, необходимых для теста. Оставьте поле *Тестируемый класс* пустым и нажмите кнопку *Готово*.

![](./media/guidance-elasticsearch/jmeter-deploy11.png)

Если откроется диалоговое окно *Создание тестового случая JUnit*, добавьте библиотеку JUnit 4 в путь сборки и нажмите кнопку *ОК*.

![](./media/guidance-elasticsearch/jmeter-deploy12.png)

В окне редактора Java должен появиться "скелет" программы для теста JUnit.

![](./media/guidance-elasticsearch/jmeter-deploy13.png)

В окне *Обозреватель пакетов* щелкните правой кнопкой мыши узел проекта, выберите пункт *Настройка*, а затем — *Преобразовать в проект Maven*.

> [AZURE.NOTE] Maven упрощает управление внешними зависимостями, с которыми связан проект (например, клиентские библиотеки Java для Elasticsearch).

![](./media/guidance-elasticsearch/jmeter-deploy14.png)

В диалоговом окне *Создание POM* в раскрывающемся списке *Упаковка* выберите пункт *jar* и нажмите кнопку *Готово*.

![](./media/guidance-elasticsearch/jmeter-deploy15.png)

В нижней области редактора POM может появиться предупреждение *Build path specifies execution environment J2SE-1.5. There are no JREs installed in the workspace that are strictly compatible with this environment* (В пути сборки указана среда выполнения J2SE-1.5. В рабочей области не установлены среды JRE, которые полностью совместимы с этой средой). Появление такого сообщения зависит от версии Java, установленной на компьютере разработки. Если ваша версия Java выше 1.5, можно игнорировать это предупреждение.

![](./media/guidance-elasticsearch/jmeter-deploy16.png)

В редакторе POM разверните окно *Свойства* и нажмите кнопку *Создать*.

![](./media/guidance-elasticsearch/jmeter-deploy17.png)

В диалоговом окне *Добавление свойства* в поле *Имя* введите *es.version*, в поле *Значение* введите *1.7.2* и нажмите кнопку *ОК*. Это версия клиентской библиотеки Java для Elasticsearch, которая будет использоваться. Она может быть впоследствии изменена. Определив ее как свойство POM и добавив ссылку на это свойство в других расположениях проекта, вы сможете быстро изменить версию.

![](./media/guidance-elasticsearch/jmeter-deploy18.png)

В нижней части редактора POM щелкните вкладку *Зависимости* и рядом со списком *Зависимости* нажмите кнопку *Добавить*.

![](./media/guidance-elasticsearch/jmeter-deploy19.png)

В диалоговом окне *Выбор зависимости* в поле *Идентификатор группы* введите *org.elasticsearch*, в поле *Идентификатор артефакта* введите *elasticsearch*, в поле *Версия* введите *\\${es.version}* и нажмите кнопку *ОК*. Сведения о клиентской библиотеке Java для Elasticsearch хранятся в центральном интернет-репозитории Maven. При создании проекта эта конфигурация будет автоматически скачивать библиотеку и ее зависимости.

![](./media/guidance-elasticsearch/jmeter-deploy20.png)

В меню *Файл* выберите команду *Сохранить все*. Проект будет сохранен и скомпилирован, а все указанные в Maven зависимости — скачаны. Убедитесь, что в обозревателе пакетов появилась папка *Зависимости Maven*. Если развернуть эту папку, вы увидите jar-файлы, скачанные для клиентской библиотеки Java для Elasticsearch.

![](./media/guidance-elasticsearch/jmeter-deploy21.png)

## Импорт существующего тестового проекта JUnit в Eclipse

Эта процедура предполагает, что вы уже скачали проект Maven, который был создан с помощью Eclipse.

Запустите интегрированную среду разработки Eclipse. В меню *Файл* щелкните команду *Импорт*.

![](./media/guidance-elasticsearch/jmeter-deploy22.png)

В диалоговом окне *Выбор* разверните папку *Maven*, щелкните пункт *Существующие проекты Maven* и нажмите кнопку *Далее*.

![](./media/guidance-elasticsearch/jmeter-deploy23.png)

В окне *Проекты Maven* укажите папку с проектом (папка с файлом pom.xml), выберите команду *Выделить все* и нажмите кнопку *Готово*.

![](./media/guidance-elasticsearch/jmeter-deploy24.png)

В *обозревателе пакетов* разверните узел, соответствующий проекту. Убедитесь, что проект содержит папку с именем *src*. В этой папке хранится исходный код для теста JUnit. Проект можно будет скомпилировать и развернуть, следуя приведенным ниже инструкциям.

![](./media/guidance-elasticsearch/jmeter-deploy25.png)

## Развертывание теста JUnit в JMeter

В этом проекте предполагается, что вы уже создали проект с именем LoadTest, который содержит класс теста JUnit с именем `BulkLoadTest.java` и принимает параметры конфигурации, передаваемые в конструктор в виде одной строки (именно этот механизм поддерживается средством JMeter).

В интегрированной среде разработки Eclipse в обозревателе пакетов щелкните правой кнопкой мыши узел проекта и выберите пункт *Экспорт*.

![](./media/guidance-elasticsearch/jmeter-deploy26.png)

В *мастере экспорта* на странице *Выбор* разверните узел *Java*, выберите пункт *JAR-файл* и нажмите кнопку *Далее*.

![](./media/guidance-elasticsearch/jmeter-deploy27.png)

На странице *Спецификация JAR-файла* в области *Выберите ресурсы для экспорта* откройте экспортируемый проект и снимите все флажки, кроме установленного для папки *src*. Также снимите флажки *.classpath*, *.project* и *pom.xml*. В поле *JAR-файл* укажите имя и расположение JAR-файла (у него должно быть расширение .jar), а затем нажмите кнопку *Готово*.

![](./media/guidance-elasticsearch/jmeter-deploy28.png)

В проводнике Windows скопируйте созданный JAR-файл на главную виртуальную машину Java JMeter и сохраните его в папку *apache-jmeter-2.13\\lib\\junit*, которая, в свою очередь, расположена в папке установки JMeter. Дополнительные сведения см. в разделе о [создании главной виртуальной машины JMeter](guidance-elasticsearch-creating-performance-testing-environment.md#creating-the-jmeter-master-virtual-machine).

Вернитесь в Eclipse, разверните окно обозревателя пакетов и запишите все JAR-файлы и их расположения, перечисленные в папке *Зависимости Maven*. Обратите внимание, что список файлов, показанных на рисунке ниже, может изменяться в зависимости от используемой версии Elasticsearch.

![](./media/guidance-elasticsearch/jmeter-deploy29.png)

В проводнике Windows скопируйте каждый JAR-файл, указанный в папке зависимостей Maven, в папку *apache-jmeter-2.13\\lib\\junit* на главной виртуальной машине JMeter.

Если папка *lib\\junit* уже содержит старые версии этих файлов, удалите их. Иначе ссылки могут указывать на неправильные JAR-файлы и тест JUnit выполнить не удастся.

Если сейчас на основной виртуальной машине запущено средство JMeter, закройте его. Повторно откройте JMeter. В окне JMeter щелкните правой кнопкой мыши *План тестирования*, последовательно выберите *Добавить* и *Потоки (пользователи)*, а затем щелкните *Группа потоков*.

![](./media/guidance-elasticsearch/jmeter-deploy30.png)

В узле *План тестирования* щелкните правой кнопкой мыши пункт *Группа потоков* и последовательно выберите *Добавить*, *Дискретизатор* и *Запрос JUnit*.

![](./media/guidance-elasticsearch/jmeter-deploy31.png)

На странице *Запрос JUnit* установите флажок *Поиск заметок JUnit 4 (вместо JUnit 3)*. В раскрывающемся списке *Имя класса* выберите класс нагрузочного теста JUnit (указан в виде *&lt;пакет&gt;.&lt;класс&gt;*). В раскрывающемся списке *Метод тестирования* выберите метод тестирования JUnit (метод, который фактически выполняет действия, связанные с тестом, и отмечен в проекте Eclipse заметкой *@test*). В поле *Метка строки конструктора* введите значения, которые будут передаваться в конструктор. Сведения на рисунке ниже приведены исключительно для справки. Указанные пользователем значения *имени класса*, *метода тестирования* и *метки строки конструктора*, вероятно, будут отличаться от представленных на рисунке.

![](./media/guidance-elasticsearch/jmeter-deploy32.png)

Если необходимый класс отсутствует в раскрывающемся списке *Имя класса*, это может означать, что JAR-файл не экспортирован надлежащим образом, не помещен в папку *lib\\junit* или в этой папке отсутствуют некоторые из зависимых JAR-файлов. В таком случае повторно экспортируйте проект из Eclipse и убедитесь, что выбран ресурс *src*. Скопируйте JAR-файл в папку *lib\\junit* и убедитесь, что все зависимые JAR-файлы, перечисленные в Maven, скопированы в папку *lib*.

Закройте JMeter. Сохранять план тестирования не нужно. Скопируйте JAR-файл с тестовым классом JUnit в папку */home/&lt;имя\_пользователя&gt;/apache-jmeter-2.13/lib/junit* на каждой виртуальной машине, подчиненной JMeter. *&lt;Имя\_пользователя&gt;* — это имя пользователя с правами администратора, указанное при создании виртуальной машины. Дополнительные сведения см. в разделе [Создание виртуальных машин, подчиненных JMeter](guidance-elasticsearch-creating-performance-testing-environment.md#creating-the-jmeter-subordinate-virtual-machines).

Скопируйте зависимые JAR-файлы для тестового класса JUnit в папку `/home/[username]/apache-jmeter-2.13/lib/junit` на каждой виртуальной машине, подчиненной JMeter. Не забудьте предварительно удалить из этой папки все более ранние версии JAR-файлов.

Для копирования файлов с компьютера Windows на компьютер Linux можно использовать служебную программу `pscp`.

[Создание среды тестирования производительности для Elasticsearch в Azure]: guidance-elasticsearch-creating-performance-testing-environment.md

<!---HONumber=AcomDC_0224_2016-->