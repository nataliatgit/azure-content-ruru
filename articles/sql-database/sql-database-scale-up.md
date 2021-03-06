<properties
	pageTitle="Изменение уровня обслуживания и уровня производительности базы данных SQL Azure"
	description="Измените уровень производительности и уровень обслуживания базы данных SQL Azure для масштабирования базы данных. Изменение ценовой категории базы данных SQL Azure."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/02/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Изменение уровня обслуживания и уровня производительности (ценовой категории) базы данных SQL

**Отдельная база данных**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)

В этой статье показано, как изменить уровень производительности и уровень обслуживания базы данных SQL с помощью [портала Azure](https://portal.azure.com).

Используйте сведения, представленные в статьях [Обновление баз данных SQL уровня Web или Business до новых уровней служб](sql-database-upgrade-server-portal.md) и [Уровни служб и уровни производительности в Базе данных SQL Azure](sql-database-service-tiers.md), для определения уровня служб и уровня производительности, соответствующих Базе данных SQL Azure.

> [AZURE.IMPORTANT] Изменение уровня производительности и уровня обслуживания базы данных SQL — это оперативная операция. Это означает, что в течение всей процедуры ваша база данных будет доступна.

- При снижении уровня базы данных ее размер не должен превышать максимально допустимый размер целевого уровня служб. 
- При обновлении базы данных с включенной [стандартной](https://msdn.microsoft.com/library/azure/dn758204.aspx) или [активной](https://msdn.microsoft.com/library/azure/dn741339.aspx) георепликацией необходимо сначала обновить базы данные-получатели до требуемого уровня производительности перед обновлением базы данных-источника.
- При понижении от уровня обслуживания Premium необходимо сначала завершить все активные отношения георепликации. Чтобы остановить процесс репликации между базой данных-источником и действующими базами данных-получателями, выполните действия, описанные в разделе [Завершение связи непрерывной копии](https://msdn.microsoft.com/library/azure/dn741323.aspx).
- Предложения службы восстановления отличаются для разных уровней служб. При переходе на более низкий уровень можно потерять возможность восстановления на момент времени или получить меньший срок хранения резервных копий. Дополнительные сведения см. в статье [Резервное копирование и восстановление Базы данных SQL Azure](https://msdn.microsoft.com/library/azure/jj650016.aspx).
- Изменение ценовой категории базы данных не приводит к изменению максимального размера баз данных. Чтобы изменить максимальный размер базы данных, воспользуйтесь [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) или [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- В течение 24 часов можно выполнять не более четырех отдельных изменений базы данных (уровней служб или уровней производительности).
- Новые свойства базы данных не применяются до тех пор, пока изменение не завершится.


**Для работы с этой статьей необходимо следующее:**

- Подписка Azure. Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.
- База данных SQL Azure. Если у вас нет базы данных SQL, создайте ее в соответствии с инструкциями в следующей статье: [Создание первой базы данных SQL Azure](sql-database-get-started.md).


## Изменение уровня обслуживания и уровня производительности базы данных


Откройте колонку «База данных SQL» для базы данных, которую необходимо масштабировать.

1.	Перейдите на [портал Azure](https://portal.azure.com).
2.	Нажмите **ПРОСМОТРЕТЬ ВСЕ**.
3.	Нажмите **Базы данных SQL**.
2.	Щелкните базу данных, которую хотите изменить.
3.	В колонке "База данных SQL" выберите **Все настройки**, а затем **Ценовая категория (масштабировать DTU)**.

    ![ценовая категория][1]


1.  Выберите новый уровень обслуживания и нажмите кнопку **Выбрать**.

    После нажатия кнопки **Выбрать** будет отправлен запрос на изменение уровня базы данных. Операция может занять некоторое время в зависимости от размера базы данных. Щелкните уведомление, чтобы просмотреть подробные сведения и состояние операции масштабирования.

    > [AZURE.NOTE] Изменение ценовой категории базы данных не приводит к изменению максимального размера баз данных. Чтобы изменить максимальный размер базы данных, воспользуйтесь [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) или [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).

    ![выберите ценовую категорию][2]

3.	На ленте слева щелкните **Уведомления**:

    ![уведомления][3]

## Убедитесь, что ценовая категория база данных соответствует выбранному.

   После завершения операции масштабирования убедитесь, что база данных находится на нужном уровне:

2.	Нажмите **ПРОСМОТРЕТЬ ВСЕ**.
3.	Нажмите **Базы данных SQL**.
2.	Щелкните базу данных, которую вы обновили.
3.	Щелкните **Ценовая категория** и убедитесь, что вы выбрали правильную категорию.

    ![новая цена][4]


## Дальнейшие действия

- Измените максимальный размер базы данных с помощью [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) или [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- [Масштабирование](sql-database-elastic-scale-get-started.md)
- [Подключение к базе данных SQL и выполнение запросов с помощью SSMS](sql-database-connect-query-ssms.md)
- [Экспорт базы данных SQL Azure](sql-database-export.md)

## Дополнительные ресурсы

- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/pricing-tile.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png

<!---HONumber=AcomDC_0224_2016-->