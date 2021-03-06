<properties
	pageTitle="Создание и экспорт BACPAC-файла базы данных SQL Azure"
	description="Создание и экспорт BACPAC-файла базы данных SQL Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/23/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Создание и экспорт BACPAC-файла базы данных SQL Azure

**Отдельная база данных**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

В этой статье приведены указания по экспорту BACPAC-файла базы данных SQL Azure с помощью [портала Azure](https://portal.azure.com).

[BACPAC-файл](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) — это файл с расширением BACPAC, который содержит схему базы данных и данные. Основной вариант использования BACPAC-файлов — перемещение базы данных с одного сервера на другой с целью [миграции локальной базы данных в облако](sql-database-cloud-migrate.md), а также для архивации существующих баз данных в открытом формате.

> [AZURE.NOTE] BACPAC-файлы не предназначены для операций службы архивации и восстановления. База данных SQL Azure автоматически создает резервные копии для каждой пользовательской базы данных. Дополнительные сведения см. в статье [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md).


BACPAC-файл экспортируется в контейнер больших двоичных объектов хранилища Azure, который можно загрузить после успешного завершения операции.

Для работы с этой статьей необходимо следующее:

- Подписка Azure. Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ УЧЕТНАЯ ЗАПИСЬ**. Оформив подписку, вернитесь к этой статье.
- База данных Azure SQL. Если у вас нет базы данных SQL, создайте ее в соответствии с инструкциями в следующей статье: [Создание первой базы данных SQL Azure](sql-database-get-started.md).
- [Учетная запись хранения Azure](../storage/storage-create-storage-account.md) с контейнером больших двоичных объектов для хранения BACPAC-файла.


## Экспорт базы данных

Откройте колонку "База данных SQL" для базы данных, которую нужно скопировать.

> [AZURE.IMPORTANT] Для получения транзакционно согласованного BACPAC-файла сначала [создайте копию базы данных](sql-database-copy.md), а затем экспортируйте эту копию.

1.	Перейдите на [портал Azure](https://portal.azure.com).
2.	Нажмите **ПРОСМОТРЕТЬ ВСЕ**.
3.	Нажмите кнопку **Базы данных SQL**.
2.	Щелкните по базе данных, которую хотите экспортировать в BACPAC-файл.
3.	В колонке "База данных SQL" нажмите **Экспорт**, чтобы открыть колонку **Экспорт базы данных**.

    ![кнопка экспорта][1]

1.  Нажмите **Хранилище** и выберите учетную запись хранения и контейнер больших двоичных объектов, в котором будет сохранен BACPAC-файл.

    ![экспорт базы данных][2]

1.  Введите **Имя пользователя администратора сервера** и **Пароль** для сервера Azure SQL, на котором находится база данных, которую вы экспортируете.
1.  Нажмите **Создать** для экспорта базы данных.

При выборе пункта **Создать** создается запрос на экспорт базы данных и отправляется службе. Операция экспорта может занять некоторое время в зависимости от размера базы данных.

## Отслеживание хода выполнения операции экспорта

2.	Нажмите **ПРОСМОТРЕТЬ ВСЕ**.
3.	Выберите **Серверы SQL**.
2.	Выберите сервер, содержащий исходную базу данных, которую вы только что экспортировали.
3.	В колонке SQL Server нажмите **Журнал импорта и экспорта**:

    ![журнал импорта и экспорта][3] ![журнал импорта и экспорта][4]

## Убедитесь, что BACPAC-файл находится в контейнере хранилища.

2.	Нажмите **ПРОСМОТРЕТЬ ВСЕ**.
3.	Нажмите **Учетные записи хранения (классические)**.
2.	Щелкните учетную запись хранения, в которой сохранили BACPAC-файл.
3.	Нажмите **Контейнеры** и выберите контейнер, в который экспортировали базу данных, для получения подробных сведений о резервной копии (из этого контейнера можно загрузить и сохранить BACPAC-файл).

    ![сведения о файле .bacpac][5]


## Дальнейшие действия

- [Импорт базы данных SQL Azure](sql-database-import.md)



## Дополнительные ресурсы

- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [Отработка аварийного восстановления](sql-database-disaster-recovery-drills.md)
- [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-history.png
[4]: ./media/sql-database-export/export-status.png
[5]: ./media/sql-database-export/bacpac-details.png

<!---HONumber=AcomDC_0224_2016-->