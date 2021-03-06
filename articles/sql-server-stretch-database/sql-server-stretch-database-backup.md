<properties
	pageTitle="Резервное копирование и восстановление баз данных, для которых включено растяжение | Microsoft Azure"
	description="Узнайте, как выполнять резервное копирование и восстановление баз данных с поддержкой растяжения."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglasl"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>


# Резервное копирование и восстановление баз данных, для которых включено растяжение 

Для резервного копирования и восстановления баз данных, для которых включено растяжение, можно продолжать использовать методы, которые вы используете. Дополнительные сведения о резервном копировании и восстановлении SQL Server см. в разделе [Резервное копирование и восстановление баз данных SQL Server](https://msdn.microsoft.com/library/ms187048.aspx).

Резервная копия базы данных с поддержкой растяжения представляет собой неполную резервную копию, которая не включает данные, перенесенные на удаленный сервер.

База данных с поддержкой растяжения предоставляет полную поддержку восстановления до точки во времени. После восстановления базы данных SQL Server до точки во времени и повторного подключения к Azure база данных с поддержкой растяжения выполняет согласование удаленных данных до той же точки во времени. Дополнительные сведения о восстановлении до точки во времени в SQL Server см. в разделе [Восстановление базы данных SQL Server до точки во времени (модель полного восстановления)](https://msdn.microsoft.com/library/ms179451.aspx). Сведения о хранимой процедуре, которую необходимо выполнить после восстановления для повторного подключения к Azure, см. в разделе [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx).

## <a name="Reconnect"></a>Восстановление базы данных с поддержкой растяжения из резервной копии

1.  Восстановите базу данных из резервной копии.

2.  Создайте главный ключ базы данных для базы данных с поддержкой растяжения. Дополнительные сведения см. в разделе [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx).

3.  Создайте учетные данные, действующие в базе данных с поддержкой растяжения. Дополнительные сведения см. в разделе [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

4.  Запустите хранимую процедуру [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx) для повторного подключения локальной базы данных с поддержкой растяжения к Azure. Укажите учетные данные, действующие в базе данных, которые вы создали на предыдущем шаге. Эти данные должны иметь тип sysname или varchar(128). (Не используйте тип varchar(max).)

    ```tsql
    Declare @credentialName nvarchar(128);
    SET @credentialName = N’<database_scoped_credential_name_created_previously>’;
    EXEC sp_rda_reauthorize_db @credentialName;
    ```

## <a name="MoreInfo"></a>Дополнительные сведения о резервном копировании и восстановлении
Резервные копии баз данных с поддержкой растяжения содержат только локальные и пригодные на момент резервного копирования данные. Эти резервные копии также содержат сведения об удаленной конечной точке, в которой хранятся данные удаленной базы данных. Это называется "неполной резервной копией". Глубокие резервные копии, которые содержат все данные в базе данных, как локальные, так и удаленные, не поддерживаются.

![Схема резервного копирования базы данных с поддержкой растяжения][StretchBackupImage1]

При восстановлении резервной копии базы данных с поддержкой растяжения происходит восстановление локальных данных и пригодных данных в базу данных, как и ожидалось. (Пригодные данные — это данные, которые не были перемещены, но будут перемещены в Azure в зависимости от конфигурации таблиц базы данных с поддержкой растяжения.) После выполнения операции восстановления база данных содержит локальные и пригодные на момент резервного копирования данные, но не содержит необходимых учетных данных и артефактов для подключения к удаленной конечной точке.

![База данных с поддержкой растяжения после резервного копирования][StretchBackupImage2]

Необходимо выполнить хранимую процедуру **sys.sp\_rda\_reauthorize\_db** для повторного подключения между локальной базой данных и ее удаленной конечной точкой. Эту операцию могут выполнять только пользователи с разрешением db\_owner. Для выполнения этой хранимой процедуры также требуется указать имя пользователя и пароль администратора удаленной конечной точки. Это означает, что нужно указать имя пользователя и пароль администратора для целевого сервера Azure.

![База данных с поддержкой растяжения после резервного копирования][StretchBackupImage3]

После повторного подключения база данных с поддержкой растяжения пытается согласовать пригодные данные из локальной базы данных с удаленными данными путем создания копии удаленных данных в удаленной конечной точке и ее связывания с локальной базой данных. Этот процесс выполняется автоматически и не требует никакого вмешательства пользователя. После этого согласования локальная база данных и удаленная конечная точка находятся в согласованном состоянии.

![База данных с поддержкой растяжения после резервного копирования][StretchBackupImage4]

## См. также
[Управление растяжением баз данных и устранение неполадок](sql-server-stretch-database-manage.md) [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx) [Резервное копирование и восстановление баз данных SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)

<!--Image references-->
[StretchBackupImage1]: ./media/sql-server-stretch-database-backup/StretchDBBackup1.png
[StretchBackupImage2]: ./media/sql-server-stretch-database-backup/StretchDBBackup2.png
[StretchBackupImage3]: ./media/sql-server-stretch-database-backup/StretchDBBackup3.png
[StretchBackupImage4]: ./media/sql-server-stretch-database-backup/StretchDBBackup4.png

<!---HONumber=AcomDC_0302_2016-->