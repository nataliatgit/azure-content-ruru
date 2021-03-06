<properties
   pageTitle="Создание базы данных хранилища данных SQL на портале Azure | Microsoft Azure"
   description="Узнайте, как создать хранилище данных SQL на портале Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Создание хранилища данных SQL

> [AZURE.SELECTOR]
- [Портал Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

В этом руководстве показано, как всего за несколько минут можно создать базу данных хранилища данных SQL с помощью портала Azure.

Изучив данный учебник, вы научитесь:

- создание сервера, на котором будет размещаться база данных;
- создание базы данных, содержащей образец базы данных AdventureWorksDW.

Если вы пытаетесь перенести существующую базу данных в хранилище данных SQL, см. статью [Общие сведения о миграции](./sql-data-warehouse-overview-migrate.md) или используйте [служебную программу миграции](./sql-data-warehouse-migrate-migration-utility.md).

Общие сведения о загрузке см. в статье [Загрузка данных в хранилище данных SQL](./sql-data-warehouse-overview-load.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Шаг 1. Вход и начало работы

1. Войдите на [портал Azure](https://portal.azure.com).

2. Выберите пункты **Создать** > **Данные + хранилище** > **Хранилище данных SQL**.

    ![Создание](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

1. Введите имя базы данных в колонке хранилища данных SQL. В этом примере мы назовем базу данных AdventureWorksDW.

    ![Ввод имени базы данных](./media/sql-data-warehouse-get-started-provision/database-name.png)


## Шаг 2. Настройка и создание сервера

В базе данных SQL и хранилище данных SQL для каждой базы данных назначается сервер, а для каждого сервера назначается географическое расположение. Сервер называется «логический сервер SQL».

> [AZURE.NOTE] <a name="note"></a>Логический сервер SQL имеет следующие характеристики:
  >
  > + Позволяет согласованно настроить несколько баз данных в одном географическом расположении.
  > + Не является физическим оборудованием, как локальный сервер. Он является частью программного обеспечения службы. Именно поэтому мы и называем его *логическим сервером*.
  > + На нем можно разместить несколько баз данных без снижения их производительности.
  > + В имени сервера используется строчная буква *s*. SQL **s**erver — это логический сервер Azure, а SQL **S**erver — имя локальной базы данных Майкрософт.

1. Последовательно щелкните **Сервер** > **Создать сервер**. За использования сервера плата не взимается. Если у вас уже есть логический сервер SQL версии 12, который вы хотите использовать, выберите существующий сервер и перейдите к следующему разделу.

    ![Создание сервера](./media/sql-data-warehouse-get-started-provision/create-server.png)

3. Введите информацию о **новом сервере**.

	- **Имя сервера**. Введите имя для логического сервера. Оно уникально для каждого географического расположения.
	- **Имя администратора сервера**. Введите имя пользователя для учетной записи администратора сервера.
	- **Пароль**. Введите пароль администратора сервера.
	- **Расположение**. Выберите географическое расположение сервера. Чтобы сократить время передачи данных, рекомендуется расположить сервер недалеко от других ресурсов данных, к которым будет получать доступ база данных.
	- **Создать сервер V12**. «ДА» — единственный параметр для хранилища данных SQL.
	- **Разрешить службам Azure доступ к серверу**. Этот флажок всегда установлен для хранилища данных SQL.

    >[AZURE.NOTE] Обязательно сохраните имя сервера, имя администратора сервера и пароль. Эти сведения потребуются для входа на сервер.

1. Нажмите кнопку **ОК**, чтобы сохранить параметры конфигурации логического сервера SQL и вернуться к колонке хранилища данных SQL.

    ![Настройка сервера](./media/sql-data-warehouse-get-started-provision/configure-server.png)

## Шаг 3. Настройка и создание базы данных

После выбора логического сервера SQL все готово для завершения создания базы данных.

2. В колонке **Хранилище данных SQL** заполните оставшиеся поля.

    ![Создание базы данных](./media/sql-data-warehouse-get-started-provision/create-database.png)

    - **Производительность**. Мы рекомендуем начать с 400 DWU. Можно переместить ползунок влево или вправо, чтобы настроить уровень производительности базы данных. Это можно сделать сейчас или позже, после создания базы данных.

        > [AZURE.NOTE] Хранилище данных SQL измеряет производительность в единицах использования хранилища данных (DWU). При увеличении показателя DWU в хранилище данных SQL увеличивается объем вычислительных ресурсов, доступных для операций базы данных. При выполнении рабочей нагрузки можно будет увидеть связь между показателем DWU и результатами рабочей нагрузки.
        >
        > Уровень производительности можно легко и быстро изменить после создания базы данных. Например, если вы не используете базу данных, переместите ползунок влево, чтобы снизить затраты. Или увеличьте производительность, если требуются дополнительные ресурсы. Вы можете приостановить базу данных, чтобы затраты были равны 0. В этом и заключаются преимущества масштабирования хранилища данных SQL.

    - **Выбрать источник**. Последовательно щелкните **Выбрать источник** > **Образец**. Так как сейчас есть только один пример базы данных, когда вы выбираете элемент «Образец», Azure автоматически устанавливает значение AdventureWorksDW для параметра **Выберите пример**.

        ![Выбор образца](./media/sql-data-warehouse-get-started-provision/select-source.png)

    - **Группа ресурсов**. Вы можете сохранить значения по умолчанию. Группы ресурсов — это контейнеры, которые помогают управлять коллекцией ресурсов Azure. Дополнительная информация о [группах ресурсов](../azure-portal/resource-group-portal.md).

    - **Подписка**. Выберите подписку для выставления счетов за эту базу данных.

1. Нажмите кнопку **Создать**, чтобы создать базу данных хранилища данных SQL.

1. Подождите несколько минут, пока создается база данных. Когда база данных будет готова, вы вернетесь на [портал Azure](https://portal.azure.com). Обратите внимание, что на панели мониторинга появилась база данных хранилища данных SQL.

    ![Предварительная версия портала](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)


## Шаг 4. Настройка доступа брандмауэра сервера к IP-адресу клиента

Чтобы подключиться к серверу с текущего IP-адреса, необходимо добавить IP-адрес клиента к правилам брандмауэра. На этом шаге показано, как это сделать.

1. Последовательно выберите элементы **Обзор** > **Серверы SQL Server** > выберите нужный сервер > **Параметры** > **Брандмауэр**.

    ![Поиск параметров брандмауэра](./media/sql-data-warehouse-get-started-provision/find-firewall-settings.png)

4. Щелкните **Добавить IP-адрес клиента**, чтобы система Azure создала правило для этого IP-адреса клиента. Щелкните **Сохранить**.

	![Добавление IP-адреса](./media/sql-data-warehouse-get-started-provision/add-client-ip.png)

1. Создайте правило брандмауэра с диапазоном IP-адресов. Это можно сделать сейчас или позже.

	>[AZURE.IMPORTANT] Ваш IP-адрес, скорее всего, будет периодически меняться, и в таких случаях вы не сможете получить доступ к серверу, пока не создадите новое правило брандмауэра. Чтобы обеспечить согласованный доступ, рекомендуется добавить диапазон IP-адресов. Дополнительную информацию см в статье [Настройка параметров брандмауэра](../sql-database/sql-database-configure-firewall-settings.md).

    Чтобы создать правило, введите имя и диапазон IP-адресов и нажмите кнопку **Сохранить**.

    ![Добавление правила брандмауэра](./media/sql-data-warehouse-get-started-provision/add-rule.png)

После настройки брандмауэра можно подключаться с компьютера к базе данных хранилища данных SQL Azure, которую вы только что создали.

## Дальнейшие действия

Теперь, когда создан пример базы данных для хранилища данных SQL, вы можете [подключиться](./sql-data-warehouse-get-started-connect.md) к этой базе данных.

<!---HONumber=AcomDC_0309_2016-->