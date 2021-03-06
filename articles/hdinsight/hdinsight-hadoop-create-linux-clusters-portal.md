<properties
   	pageTitle="Создание кластеров Hadoop, HBase, Storm или Spark на платформе Linux в HDInsight с помощью портала | Microsoft Azure"
   	description="Узнайте, как создавать кластеры Hadoop, HBase, Storm или Spark на платформе Linux в HDInsight с помощью браузера и портала предварительной версии Azure."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="12/08/2015"
   	ms.author="nitinme"/>


#Создание кластеров под управлением Linux в HDInsight с помощью портала предварительной версии Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Портал предварительной версии Azure — это веб-средство управления службами и ресурсами, размещенными в облаке Microsoft Azure. Используйте сведения, приведенные в этом документе, чтобы создать новый кластер HDInsight под управлением Linux с помощью портала предварительной версии и веб-браузера.

##Предварительные требования

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Современный браузер__. Портал предварительной версии Azure использует HTML5 и Javascript и может работать неправильно в старых браузерах.

##Создание кластеров

1. Войдите в [портал предварительной версии Azure](https://portal.azure.com).

2. Последовательно выберите **СОЗДАТЬ**, **Анализ данных**, а затем — **HDInsight**.

    ![Создание нового кластера на портале предварительной версии Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.1.png "Создание нового кластера на портале предварительной версии Azure")

3. Введите **Имя кластера**, выберите **Hadoop** в качестве **Типа кластера** и в раскрывающемся списке **Операционная система кластера** выберите **Ubuntu**. Если имя кластера доступно, рядом с ним появится зеленый флажок.

    > [AZURE.NOTE] Для подготовки кластера HBase или Storm выберите соответствующее значение **типа кластера** из раскрывающегося списка.

    ![Введите имя и тип кластера](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.2.png "Введите имя и тип кластера")

4. Если у вас есть несколько подписок, выберите запись **Подписка**, чтобы выбрать подписку Azure для кластера.

5. Щелкните **Группа ресурсов**, чтобы просмотреть список существующих групп ресурсов и выбрать ту, в которой будет создан кластер. Или выберите **Создать** и введите имя новой группы ресурсов. Если новое имя группы доступно, рядом с ним появится зеленый флажок.

	> [AZURE.NOTE] Эта запись будет выбрана по умолчанию для одной из существующих групп ресурсов (при их наличии).

6. Щелкните **Учетные данные** и введите пароль для пользователя с правами администратора. Необходимо также ввести **Имя пользователя SSH** и **ПАРОЛЬ** или **ОТКРЫТЫЙ КЛЮЧ**, которые будут использоваться для проверки подлинности пользователя SSH. Мы рекомендуем использовать открытый ключ. Нажмите кнопку **Выбрать** внизу страницы, чтобы сохранить учетные данные конфигурации.

	![Укажите учетные данные кластера](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.3.png "Укажите учетные данные кластера")

	Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях.

	* [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Щелкните **Источник данных**, чтобы выбрать для кластера существующий источник данных или создать новый.

	![Колонка "Источник данных"](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.4.png "Укажите конфигурацию источника данных")

	В настоящее время в качестве источника данных для кластера HDInsight можно выбрать учетную запись хранения Azure. Далее приводится описание элементов интерфейса, которое поможет вам лучше понять записи в колонке **Источник данных**.

	- **Метод выбора**: выберите значение **Из всех подписок**, чтобы активировать поиск учетных записей хранения во всех своих подписках. Задайте для этого параметра значение **Ключ доступа**, если вы хотите ввести **имя хранилища** и **ключ доступа** существующей учетной записи хранения.

	- **Выбрать учетную запись хранения или создать новую**: щелкните **Выбрать учетную запись хранения**, чтобы найти и выбрать существующую учетную запись хранения, которую нужно связать с кластером. Чтобы создать новую учетную запись хранения, выберите **Создать**. В появившееся поле введите имя учетной записи хранения. Если имя доступно, появится зеленый флажок.

	- **Выбрать контейнер по умолчанию**. Эта команда позволяет ввести имя контейнера по умолчанию и использовать его для кластера. Вы можете ввести любое имя, однако мы рекомендуем использовать такое же имя, как у кластера, чтобы легко распознавать, какой контейнер используется для конкретного кластера.

	- **Расположение**. Географический регион, к которому будет относиться существующая или новая учетная запись хранения.

		> [AZURE.IMPORTANT] Выбранное расположение для источника данных по умолчанию будет также определять расположение кластера HDInsight. Кластер и источник данных по умолчанию должны находиться в одном регионе.

	Нажмите **Выбрать**, чтобы сохранить конфигурацию источника данных.

8. Щелкните **Ценовые категории узла**, чтобы отобразить сведения об узлах, которые будут созданы для этого кластера. Задайте количество рабочих узлов, необходимых для кластера. Оценочная стоимость кластера отобразится в колонке.

	![Колонка "Ценовые категории узла"](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.5.png "Укажите количество узлов кластера")
    
    > [AZURE.IMPORTANT] Если вы планируете использовать более 32 рабочих узлов (при создании кластера или в ходе масштабирования после создания кластера), для головного узла потребуется минимум 8-ядерный процессор и 14 ГБ ОЗУ.
    >
    > Дополнительные сведения о размерах узлов и связанных затратах см. в статье [Цены на HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

	Нажмите **Выбрать**, чтобы сохранить конфигурацию цен узла.

9. Щелкните **Необязательная конфигурация**, чтобы выбрать версию кластера и настроить другие необязательные параметры, например присоединение **виртуальной сети** и настройку **внешнего метахранилища** для хранения данных Hive и Oozie. Вы можете настроить кластер для установки пользовательских компонентов с помощью действия сценариев или использовать с кластером дополнительные учетные записи хранения.

	* Откройте раскрывающийся список **Версия HDInsight** и выберите версию, которую вы хотите использовать для кластера. Дополнительную информацию см. в статье [Версии кластеров HDInsight](hdinsight-component-versioning.md).

	* **Виртуальная сеть** — выберите виртуальную сеть Azure и подсеть для размещения кластера в виртуальной сети.

		![Колонка "Виртуальная сеть"](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.6.png "Укажите сведения о виртуальной сети")

    	Сведения об использовании HDInsight в виртуальной сети, включая требования к конфигурации виртуальной сети, см. в разделе [Расширение возможностей HDInsight с помощью виртуальной сети Azure](hdinsight-extend-hadoop-virtual-network.md).

	* Щелкните **Внешние метахранилища**, чтобы указать базу данных SQL, которую вы хотите использовать для сохранения метаданных Hive и Oozie, связанных с кластером.
    
        > [AZURE.NOTE] Возможность настройки метахранилища не поддерживается для кластеров типа HBase.

		![Колонка "Пользовательские метахранилища"](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.7.png "Укажите внешние метахранилища")

		Для параметра **Использовать существующую базу данных SQL для метаданных Hive** выберите **Да**, укажите базу данных SQL, а затем введите имя пользователя и пароль для базы данных. Повторите эти шаги, если вы хотите **Использовать существующую базу данных SQL для метаданных Oozie**. Щелкайте **Выбрать**, пока не перейдете в колонку **Необязательная конфигурация**.

		>[AZURE.NOTE] База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных SQL Azure в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, а затем **Службы Azure**, **Да** и **Сохранить**.

	* Щелкните **Действия сценария**, если хотите использовать настраиваемый сценарий для настройки кластера во время его создания. Дополнительную информацию о действиях сценариев см. в статье [Настройка кластеров HDInsight с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md). В колонке "Действия сценария" введите данные, как показано на снимке экрана.

		![Колонка "Действие сценария"](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.8.png "Укажите действие сценария")

	* Щелкните **Ключи хранилища Azure**, чтобы указать дополнительные учетные записи хранения, которые нужно связать с кластером. В колонке **Ключи хранилища Azure** нажмите кнопку **Добавить ключ к хранилищу данных** и выберите существующую учетную запись хранения или создайте новую учетную запись.

		![Колонка "Дополнительное хранилище"](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.9.png "Укажите дополнительные учетные записи хранения")

		Щелкайте **Выбрать**, пока не перейдете в колонку **Кластер HDInsight**.

10. В колонке **Новый кластер HDInsight** обязательно выберите параметр **Закрепить на начальной панели**, а затем нажмите кнопку **Создать**. После этого кластер будет создан, а на начальную панель портала Azure будет добавлена его плитка. Значок указывает, что выполняется подготовка кластера. После завершения подготовки вместо него будет отображаться значок HDInsight.

	| Подготовка выполняется | Подготовка завершена |
	| ------------------ | --------------------- |
	| ![Индикатор подготовки на начальной панели](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioning.png) | ![Плитка подготовки кластера](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioned.png) |

	> [AZURE.NOTE] Обычно создание кластера занимает около 15 минут. Отслеживать процесс подготовки можно с помощью элемента на начальной панели или записи **Уведомления** в левой части страницы.

11. После завершения подготовки щелкните плитку кластера на начальной панели, чтобы открыть колонку кластера. Колонка кластера содержит важные сведения о кластере, такие как имя, группа ресурсов, к которой он принадлежит, расположение, операционная система, URL-адрес панели мониторинга кластера и т. д.

	![Колонка "Кластер"](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.Cluster.Blade.png "Свойства кластера")

	Ниже приведено описание значков в верхней части этой колонки и в разделе **Основные компоненты**.

	* **Параметры** и **Все параметры** — отображение колонки **Параметры** кластера с подробными сведениями о его конфигурации.

	* **Панель мониторинга**, **Панель мониторинга кластера** и **URL-адрес**. Пути доступа к панели мониторинга кластера, которая представляет собой веб-портал для выполнения заданий в кластере.

	* **Secure Shell** — сведения, необходимые для доступа к кластеру по протоколу SSH.

	* **Удалить**. Удаление кластера HDInsight.

	* **Быстрый запуск** (![значок облака и молнии = быстрый запуск](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)). Отображает данные, необходимые для начала работы с HDInsight.

	* **Пользователи** (![значок "пользователи"](./media/hdinsight-hadoop-create-linux-cluster-portal/users.png)). Здесь можно указать разрешения на доступ к _порталу управления_ этого кластера для других пользователей в вашей подписке Azure.

		> [AZURE.IMPORTANT] Этот параметр влияет _только_ на права доступа к этому кластеру на портале предварительной версии Azure. Параметр не влияет на то, кто может подключаться к кластеру HDInsight или отправлять в него задания.

	* **Теги** (![значок "теги"](./media/hdinsight-hadoop-create-linux-cluster-portal/tags.png)) — теги позволяют задавать пары «ключ — значение» для определения пользовательской таксономии облачных служб. Например, можно создать ключ с именем __project__, а затем использовать общее значение для всех служб, связанных с определенным проектом.

##Настройка кластеров

- Обратитесь к статье [Настройка кластеров HDInsight с помощью службы начальной загрузки](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Обратитесь к статье [Настройка кластеров HDInsight под управлением Windows с помощью действия сценария](hdinsight-hadoop-customize-cluster.md).

##Дальнейшие действия

Теперь, когда вы успешно создали кластер HDInsight, обратитесь к следующим статьям, чтобы научиться работать с кластером:

###Кластеры Hadoop

* [Использование Hive с HDInsight](hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md)

###Кластеры HBase

* [Начало работы с HBase в HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Разработка приложений Java для HBase в HDInsight](hdinsight-hbase-build-java-maven-linux)

###Кластеры Storm

* [Разработка приложений Java для Storm в HDInsight](hdinsight-storm-develop-java-topology.md)
* [Использование компонентов Python в Storm в HDInsight](hdinsight-storm-develop-python-topology.md)
* [Развертывание и мониторинг топологий с помощью Storm в HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

###Кластеры Spark

* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)

<!---HONumber=AcomDC_0224_2016-->