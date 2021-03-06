<properties
	pageTitle="Подключение к Базе данных SQL с помощью Java и драйвера JDBC в операционной системе Windows"
	description="В статье представлен пример кода Java, который можно использовать для подключения к базе данных SQL Azure. В примере кода драйвер JDBC запускается на компьютере с клиентской версией Windows."
	services="sql-database"
	documentationCenter=""
	authors="LuisBosquez"
	manager="jeffreyg"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="lbosq"/>


# Подключение к Базе данных SQL с помощью Java и драйвера JDBC в операционной системе Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


В этой статье представлен пример кода Java, который можно использовать для подключения к Базе данных SQL Azure. Для использования примера кода Java требуется пакет средств разработки Java Development Kit (JDK) версии 1.8. Подключение к Базе данных SQL Azure осуществляется с помощью драйвера JDBC.


## Предварительные требования

### Драйверы и библиотеки

- [Драйвер Microsoft JDBC для SQL Server — SQL JDBC 4](http://www.microsoft.com/download/details.aspx?displaylang=en&id=11774).
- Любая операционная система с поддержкой [пакета JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

### База данных SQL

Чтобы узнать, как создать базу данных, перейдите на страницу [Начало работы](sql-database-get-started.md).

### Таблица SQL

В приведенном примере кода Java предполагается, что в вашей базе данных SQL Azure уже есть следующая тестовая таблица.

<!--
Could this instead be a #tempPerson table, so that the Java code sample could be fully self-sufficient and be runnable (with automatic cleanup)?
-->


	CREATE TABLE Person
	(
		id         INT    PRIMARY KEY    IDENTITY(1,1),
		firstName  VARCHAR(32),
		lastName   VARCHAR(32),
		age        INT
	);


## Шаг 1. Получение строки подключения

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE]При использовании драйвера JTDS JDBC необходимо будет добавить ssl=require в URL-адрес строки подключения и задать для виртуальной машины Java параметр -Djsse.enableCBCProtection=false. Этот параметр виртуальной машины Java отключает исправление для уязвимости системы безопасности, поэтому убедитесь, что понимаете все связанные с ним риски, прежде чем задавать этот параметр.


## Шаг 2. Компиляция образца кода Java


В этом разделе приведена основная часть кода Java. Она содержит комментарии, указывающие, куда следует вставлять сегменты кода Java из последующих разделов. Код из этого раздела можно скомпилировать и запустить даже без вставки дополнительных сегментов. При этом после подключения выполнение кода прекратится. Примеры комментариев:


1. `// INSERT two rows into the table.`
2. `// TRANSACTION and commit for an UPDATE.`
3. `// SELECT rows from the table.`


Далее приведена основная часть кода Java. Она включает функцию `main` класса `SQLDatabaseTest`.


	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;

	public class SQLDatabaseTest {

		public static void main(String[] args) {
			String connectionString =
				"jdbc:sqlserver://your_server.database.windows.net:1433;"
				+ "database=your_database;"
				+ "user=your_user@your_server;"
				+ "password=your_password;"
				+ "encrypt=true;"
				+ "trustServerCertificate=false;"
				+ "hostNameInCertificate=*.database.windows.net;"
				+ "loginTimeout=30;";

			// Declare the JDBC objects.
			Connection connection = null;
			Statement statement = null;
			ResultSet resultSet = null;
			PreparedStatement prepsInsertPerson = null;
			PreparedStatement prepsUpdateAge = null;

			try {
				connection = DriverManager.getConnection(connectionString);

				// INSERT two rows into the table.
				// ...

				// TRANSACTION and commit for an UPDATE.
				// ...

				// SELECT rows from the table.
				// ...
			}
			catch (Exception e) {
				e.printStackTrace();
			}
			finally {
				// Close the connections after the data has been handled.
				if (prepsInsertPerson != null) try { prepsInsertPerson.close(); } catch(Exception e) {}
				if (prepsUpdateAge != null) try { prepsUpdateAge.close(); } catch(Exception e) {}
				if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
				if (statement != null) try { statement.close(); } catch(Exception e) {}
				if (connection != null) try { connection.close(); } catch(Exception e) {}
			}
		}
	}


Разумеется, для выполнения кода Java из предыдущего абзаца нужно заменить реальными значениями следующие заполнители:


- your\_server
- your\_database
- your\_user
- your\_password


## Шаг 3. Вставка строк


Этот сегмент кода Java выполняет инструкцию INSERT Transact-SQL для вставки двух строк в таблицу Person. Общая последовательность выглядит так:


1. Создайте объект `PreparedStatement` с помощью объекта `Connection`.
 - Мы включаем параметр `Statement.RETURN_GENERATED_KEYS`, чтобы позже можно было получить значение, автоматически созданное для ключа **id**.
2. Вызовите метод `execute` в контексте объекта `PreparedStatement`.
3. Используя объект `PreparedStatement`, получите числовое значение, автоматически созданное для первичного ключа.
 - Оно относится к спецификации AUTO\_INCREMENT столбца **id** таблицы Person.


Скопируйте и вставьте этот небольшой сегмент кода Java в приведенную выше основу кода под комментарием `// INSERT two rows into the table.`.


	// Create and execute an INSERT SQL prepared statement.
	String insertSql = "INSERT INTO Person (firstName, lastName, age) VALUES "
		+ "('Bill', 'Gates', 59), "
		+ "('Steve', 'Ballmer', 59);";

	prepsInsertPerson = connection.prepareStatement(
		insertSql,
		Statement.RETURN_GENERATED_KEYS);
	prepsInsertPerson.execute();
	// Retrieve the generated key from the insert.
	resultSet = prepsInsertPerson.getGeneratedKeys();
	// Iterate through the set of generated keys.
	while (resultSet.next()) {
		System.out.println("Generated: " + resultSet.getString(1));
	}


## Шаг 4. Выполнение транзакции

Следующий сегмент кода Java выполняет инструкцию UPDATE Transact-SQL для увеличения значения `age` в каждой строке таблицы Person. Общая последовательность выглядит так:


1. Чтобы не допустить автоматической фиксации обновлений в базе данных, вызывается метод `setAutoCommit`.
2. Для выполнения инструкции UPDATE в контексте транзакции вызывается метод `executeUpdate`.
3. Чтобы однозначно зафиксировать транзакцию, вызывается метод `commit`.


Скопируйте и вставьте этот небольшой сегмент кода Java в приведенную выше основу кода под комментарием `// TRANSACTION and commit for an UPDATE.`.


	// Set AutoCommit value to false to execute a single transaction at a time.
	connection.setAutoCommit(false);

	// Write the SQL Update instruction and get the PreparedStatement object.
	String transactionSql = "UPDATE Person SET Person.age = Person.age + 1;";
	prepsUpdateAge = connection.prepareStatement(transactionSql);

	// Execute the statement.
	prepsUpdateAge.executeUpdate();

	//Commit the transaction.
	connection.commit();

	// Return the AutoCommit value to true.
	connection.setAutoCommit(true);


## Шаг 4. Выполнение запроса


Этот сегмент кода Java выполняет инструкцию SELECT Transact-SQL для вывода всех обновленных строк таблицы Person. Общая последовательность выглядит так:


1. Создайте объект `Statement` с помощью объекта `Connection`.
2. Создайте объект `ResultSet` с помощью объекта `Statement`.
3. Зациклите вызов `resultSet.next`, чтобы перебрать все возвращаемые строки.


Скопируйте и вставьте этот небольшой сегмент кода Java в приведенную выше основу кода под комментарием `// SELECT rows from a table.`.


	// Create and execute a SELECT SQL statement.
	String selectSql = "SELECT firstName, lastName, age FROM dbo.Person";
	statement = connection.createStatement();
	resultSet = statement.executeQuery(selectSql);

	// Iterate through the result set and print the attributes.
	while (resultSet.next()) {
		System.out.println(resultSet.getString(2) + " "
			+ resultSet.getString(3));
	}

## Дальнейшие действия

Дополнительную информацию см. в [Центре разработчика Java](/develop/java/).

<!---HONumber=AcomDC_0107_2016-->