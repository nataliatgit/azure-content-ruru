<properties
   pageTitle="Создание кластеров Hadoop в HDInsight | Microsoft Azure"
   	description="Научитесь создавать кластеры для Azure HDInsight с помощью портала Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/06/2016"
   ms.author="jgao"/>

# Создание кластеров Hadoop под управлением Windows в HDInsight с помощью портала Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Узнайте, как создать кластер Hadoop в HDInsight с помощью портала Azure. [Портал Microsoft Azure](../azure-portal-overview.md) — это централизованное место, где можно подготавливать ресурсы Azure и управлять ими. Портал Azure является одним из средств, которые можно использовать для создания кластера Hadoop под управлением Linux или Windows в HDInsight. Сведения о других инструментах и функциях создания кластера приведены на вкладке в верхней части этой страницы или в разделе [Способы создания кластера](hdinsight-provision-clusters.md#cluster-creation-methods).

###Предварительные требования:

Прежде чем следовать указаниям в этой статье, необходимо подготовить следующее:

- Подписка Azure. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## Создание кластеров


**Создание кластера HDInsight**

1. Войдите на [портал Azure](https://portal.azure.com).
2. Щелкните **Создать**, **Аналитика данных**, а затем — **HDInsight**.

  ![Создание нового кластера на портале Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Создание нового кластера на портале Azure")

3. Введите или выберите следующие значения:

  * **Имя кластера**: введите имя кластера. Если имя кластера доступно, рядом с ним появится зеленый флажок.
  * **Тип кластера**: выберите **Hadoop**. Другие варианты включают **HBase**, **Storm** и **Spark**.
  * **Операционная система кластера**: выберите **Windows**. Чтобы создать кластер на основе Linux, выберите **Linux**.
  * **Версия**: см. статью [Версии HDInsight](hdinsight-component-versioning.md).
  * **Подписка**: выберите подписку Azure, которая будет использоваться для создания этого кластера.
  * **Группа ресурсов**: выберите существующую группу ресурсов или создайте новую. Эта запись будет выбрана по умолчанию для одной из существующих групп ресурсов (при их наличии).
  * **Учетные данные**: настройте имя пользователя и пароль для пользователя Hadoop (пользователя HTTP). При включении удаленного рабочего стола для кластера необходимо настроить имя пользователя удаленного рабочего стола и пароль, а также указать дату окончания срока действия учетной записи. Щелкните **Выбрать** внизу страницы, чтобы сохранить изменения.

![Укажите учетные данные кластера](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Укажите учетные данные кластера")

  * **Источник данных**: выберите существующую или создайте новую учетную запись хранения Azure, которая будет использована в качестве файловой системы по умолчанию для кластера.

   ![Колонка "Источник данных"](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Укажите конфигурацию источника данных")

  * **Метод выбора**: выберите значение **Из всех подписок**, чтобы активировать поиск учетных записей хранения во всех своих подписках. Задайте для этого параметра значение **Ключ доступа**, если вы хотите ввести **имя хранилища** и **ключ доступа** существующей учетной записи хранения.
  * **Выбрать учетную запись хранения или создать новую**: щелкните **Выбрать учетную запись хранения**, чтобы найти и выбрать существующую учетную запись хранения, которую нужно связать с кластером. Чтобы создать новую учетную запись хранения, выберите **Создать**. В появившееся поле введите имя учетной записи хранения. Если имя доступно, появится зеленый флажок.
  * **Выбрать контейнер по умолчанию**. Эта команда позволяет ввести имя контейнера по умолчанию и использовать его для кластера. Вы можете ввести любое имя, однако мы рекомендуем использовать такое же имя, как у кластера, чтобы легко распознавать, какой контейнер используется для конкретного кластера.
  * **Расположение**. Географический регион, к которому будет относиться существующая или новая учетная запись хранения. Это расположение будет определять расположение кластера. Учетная запись хранения, используемая по умолчанию, должна находиться в том же центре обработки данных Azure, что и кластер.
  	
  * **Ценовые категории узлов**. Задайте количество рабочих узлов, необходимых для кластера. Оценочная стоимость кластера отобразится в колонке.
  

![Колонка "Ценовые категории узла"](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Укажите количество узлов кластера")


 * **Необязательная конфигурация**: позволяет выбрать версию кластера, а также настроить другие необязательные параметры, например присоединение **виртуальной сети** и конфигурацию **внешнего метахранилища** для хранения данных Hive и Oozie. Используйте действия сценариев, чтобы настроить кластер для установки пользовательских компонентов или использовать дополнительные учетные записи хранения с кластером.

 * **Версия HDInsight**: выберите версию, которую вы хотите использовать для кластера. Дополнительную информацию см. в разделе [Версии кластеров HDInsight](hdinsight-component-versioning.md).
 * **Виртуальная сеть** — выберите виртуальную сеть Azure и подсеть для размещения кластера в виртуальной сети.  

![Колонка "Виртуальная сеть"](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Укажите сведения о виртуальной сети")

Сведения об использовании HDInsight в виртуальной сети, включая требования к конфигурации виртуальной сети, см. в разделе [Расширение возможностей HDInsight с помощью виртуальной сети Azure](hdinsight-extend-hadoop-virtual-network.md).
  

  		
* **Внешние метахранилища**: укажите базу данных SQL Azure для хранения метаданных Hive и Oozie, связанных с кластером.
 
  > [AZURE.NOTE] Возможность настройки метахранилища не поддерживается для кластеров типа HBase.

![Колонка "Пользовательские метахранилища"](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Укажите внешние метахранилища")


Для параметра **Использовать существующую базу данных SQL для метаданных Hive** выберите **Да**, укажите базу данных SQL, а затем введите имя пользователя и пароль для базы данных. Повторите эти шаги, если вы хотите **Использовать существующую базу данных SQL для метаданных Oozie**. Щелкайте **Выбрать**, пока не перейдете в колонку **Необязательная конфигурация**.


>[AZURE.NOTE] База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных SQL Azure в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, а затем **Службы Azure**, **Да** и **Сохранить**.
		
 * Щелкните **Действия сценария**, если хотите использовать настраиваемый сценарий для настройки кластера во время его создания. Дополнительную информацию о действиях сценариев см. в статье [Настройка кластеров HDInsight с помощью действия сценария](hdinsight-hadoop-customize-cluster.md). В колонке "Действия сценария" введите данные, как показано на снимке экрана.
  	

![Колонка "Действие сценария"](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Укажите действие сценария")


 * **Ключи хранилища Azure**: укажите дополнительные учетные записи хранения, которые нужно связать с кластером. В колонке **Ключи хранилища Azure** нажмите кнопку **Добавить ключ к хранилищу данных** и выберите существующую учетную запись хранения или создайте новую.
    

 ![Колонка "Дополнительное хранилище"](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Укажите дополнительные учетные записи хранения")


4. Щелкните **Создать**. Выберите **Закрепить на начальной панели**, чтобы добавить элемент кластера на начальную панель портала. Значок указывает, что выполняется создание кластера. После завершения создания вместо него будет отображаться значок HDInsight.
	
  Обычно создание кластера занимает около 15 минут. Отслеживать процесс подготовки вы можете с помощью элемента на начальной панели или записи **Уведомления** в левой части страницы.
	

5. После завершения создания щелкните элемент кластера на начальной панели, чтобы открыть колонку кластера. Колонка кластера содержит важные сведения о кластере: имя, группа ресурсов, к которой он принадлежит, расположение, операционная система, URL-адрес панели мониторинга кластера и т. д.

![Колонка "Кластер"](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Свойства кластера")


Ниже приведено описание значков в верхней части этой колонки и в разделе **Основные компоненты**.


* **Параметры** и **Все параметры** — отображение колонки **Параметры** кластера с подробными сведениями о его конфигурации.
* **Панель мониторинга**, **Панель мониторинга кластера** и **URL-адрес**: пути доступа к панели мониторинга кластера, которая представляет собой веб-портал для выполнения заданий в кластере.
* **Удаленный рабочий стол**: позволяет включать или отключать удаленный рабочий стол на узлах кластера.
* **Масштабировать кластер**. Позволяет изменить количество рабочих узлов для этого кластера.
* **Удалить**: удаление кластера HDInsight.
* **Быстрый запуск** (![значок облака и молнии = быстрый запуск](./media/hdinsight-provision-clusters/quickstart.png)). Отображает данные, необходимые для начала работы с HDInsight.
* **Пользователи** (![значок "пользователи"](./media/hdinsight-provision-clusters/users.png)): здесь вы можете указать разрешения на доступ к _порталу управления_ этого кластера для других пользователей в своей подписке Azure.
	

> [AZURE.IMPORTANT] Этот параметр влияет _только_ на доступ и разрешения для этого кластера на портале и не определяет пользователей, которые могут подключаться к кластеру HDInsight или отправлять в него задания.
		
* **Теги** (![значок "теги"](./media/hdinsight-provision-clusters/tags.png)): теги позволяют задавать пары "ключ — значение" для определения пользовательской таксономии облачных служб. Например, можно создать ключ с именем __project__, а затем использовать общее значение для всех служб, связанных с определенным проектом.

##Настройка кластеров

- См. статью [Настройка кластеров HDInsight с помощью службы начальной загрузки](hdinsight-hadoop-customize-cluster-bootstrap.md).
- См. статью [Настройка кластеров HDInsight под управлением Windows с помощью действия сценария](hdinsight-hadoop-customize-cluster.md).

##Дальнейшие действия
В этой статье вы ознакомились с несколькими способами создания кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) — узнайте, как начать работу с кластером HDInsight.
* [Отправка заданий Hadoop программными средствами](hdinsight-submit-hadoop-jobs-programmatically.md) — узнайте, как программными средствами отправлять задания в HDInsight.
* [Управление кластерами Hadoop в HDInsight с помощью портала Azure](hdinsight-administer-use-management-portal.md)

<!---HONumber=AcomDC_0224_2016-->
