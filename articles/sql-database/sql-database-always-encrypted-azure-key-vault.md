<properties
	pageTitle="Защита конфиденциальных данных в базе данных SQL с помощью шифрования базы данных | Microsoft Azure"
	description="Сведения о настройке защиты конфиденциальных данных в базе данных SQL всего за несколько минут."
	keywords="база данных SQL, шифрование SQL, шифрование базы данных, ключ шифрования, конфиденциальные данные, постоянное шифрование"	
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="cgronlun"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="sstein"/>

# Защита конфиденциальных данных в базе данных SQL с помощью шифрования базы данных и хранение ключей шифрования в хранилище ключей Azure

> [AZURE.SELECTOR]
- [Хранилище ключей Azure](sql-database-always-encrypted-azure-key-vault.md)
- [Хранилище сертификатов Windows](sql-database-always-encrypted.md)


В этой статье показано, как защитить конфиденциальные данные в базе данных SQL с помощью шифрования базы данных с помощью [мастера настройки постоянного шифрования](https://msdn.microsoft.com/library/mt459280.aspx) в [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx) и сохранить ключи шифрования в хранилище ключей Azure.

Постоянное шифрование — это новая технология шифрования, применяемая в базе данных SQL Azure и SQL Server. Она защищает конфиденциальные данные, которые хранятся на сервере в неактивном состоянии, перемещаются между клиентом и сервером и просто используются, предотвращая их доступность в виде открытого текста внутри системы базы данных. К зашифрованным данным можно получить доступ только через клиентские приложения и серверы приложений, у которых есть доступ к ключам. Дополнительные сведения см. в статье [Постоянное шифрование (ядро СУБД)](https://msdn.microsoft.com/library/mt163865.aspx).


После настройки этой функции мы создадим клиентское приложение на языке C# для работы с зашифрованными данными, используя Visual Studio.

Выполнив шаги, приведенные в этой статье, вы узнаете, как настроить постоянное шифрование в базе данных SQL Azure. Здесь вы научитесь:

- Использовать мастер настройки постоянного шифрования в SQL Server Management Studio для создания [ключей постоянного шифрования](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
    - Создавать [главный ключ столбца](https://msdn.microsoft.com/library/mt146393.aspx).
    - Создавать [ключ шифрования столбца](https://msdn.microsoft.com/library/mt146372.aspx).
- Создавать таблицу базы данных и шифровать некоторые столбцы.
- Создавать приложение, которое вставляет, выбирает и отображает данные зашифрованных столбцов.

> [AZURE.NOTE] В настоящее время функция постоянного шифрования для базы данных SQL Azure доступна в предварительной версии.


## Предварительные требования

Для работы с этим руководством вам потребуется:

- Учетная запись Azure и подписка (еще до начала). Если у вас ее нет, зарегистрируйтесь, чтобы [воспользоваться бесплатной пробной версией](https://azure.microsoft.com/pricing/free-trial/).
- [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) версии 13.0.700.242 или более поздней версии.
- [NET Framework версии 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) или более поздней версии (на клиентском компьютере).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- [Azure PowerShell](../powershell-install-configure.md) начиная с версии 1.0.
    - Чтобы определить свою версию PowerShell, наберите **(Get-Module azure -ListAvailable).Version**.



## Включение доступа клиентского приложения к службе базы данных SQL

Сначала необходимо включить доступ клиентского приложения к службе базы данных SQL. Для этого необходимо настроить проверку подлинности и получить *идентификатор клиента* и *секрет*, которые понадобятся для проверки подлинности приложения в приведенном ниже коде.

1. Откройте [классический портал](http://manage.windowsazure.com).
2. Выберите **Active Directory** в меню слева и щелкните по каталогу Active Directory, который будет использовать ваше приложение.
3. Щелкните **Приложения** и затем **ДОБАВИТЬ** (внизу).
4. Введите имя приложения (например *myClientApp*), выберите **ВЕБ-ПРИЛОЖЕНИЕ** и щелкните стрелку, чтобы продолжить.
5. В полях "URL-АДРЕС ВХОДА" и "URI ИДЕНТИФИКАТОРА ПРИЛОЖЕНИЯ" можно просто ввести допустимый URL-адрес (например: **http://myClientApp*).
6. Нажмите **НАСТРОИТЬ**.
7. Скопируйте **ИДЕНТИФИКАТОР КЛИЕНТА** (он потребуется в коде позже).
8. В разделе "Ключи" выберите **1 год** в раскрывающемся списке **Выбрать длительность** (мы скопируем ключ после сохранения ниже).
11. Прокрутите вниз и щелкните **Добавить приложение**.
12. Оставьте параметр **ПОКАЗАТЬ** в значении **Приложения Microsoft**, затем найдите и выделите **Управление службами Windows Azure** и щелкните флажок, чтобы продолжить.
13. В строке **Диспетчер служб Azure Windows** щелкните раскрывающийся список **Делегированные разрешения** и выберите **Доступ к управлению службами Azure**.
14. Щелкните **СОХРАНИТЬ** (внизу).
15. После завершения сохранения найдите и скопируйте значение ключа в разделе **ключи** (это значение потребуется в коде позже). 



## Создание хранилища ключей Azure для хранения ключей

Теперь, когда клиентское приложение настроено и у вас есть идентификатор клиента, пора создать хранилище ключей Azure и настроить его политику доступа, так чтобы ваше приложение могло обращаться к секретам в хранилище (ключам постоянного шифрования). Для использования ключей в хранилище ключей Azure необходимы права доступа *create*, *get*, *list*, *sign*, *verify*, *wrapKey* и *unwrapKey* для создания нового главного ключа столбца и для настройки шифрования в SQL Server Management Studio.

Для быстрого создания хранилища ключей Azure можно запустить приведенный ниже сценарий. Подробное описание этих командлетов и дополнительные сведения о создании и настройке хранилища ключей Azure см. в разделе [Приступая к работе с хранилищем ключей Azure](../Key-Vault/key-vault-get-started.md).



    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $clientId = '<client ID that you copied in step 7 above>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'
    

    Login-AzureRmAccount
    $subscriptionId = (Get-AzureRmSubscription -SubscriptionName $subscriptionName).SubscriptionId
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup –Name $resourceGroupName –Location $location
    New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzureRmKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $clientId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list




## Создание пустой базы данных SQL
1. Войдите на [портал Azure](https://portal.azure.com/).
2. Выберите пункты **Создать** > **Хранилище, кэш и резервное копирование** > **SQL Database**.
3. Создайте **пустую** базу данных с именем **Clinic** на новом или существующем сервере. Подробные инструкции по созданию базы данных на портале Azure см. в разделе [Создание базы данных SQL за несколько минут](sql-database-get-started.md).

	![создать пустую базу данных](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Позже в этом руководстве вам потребуется строка подключения, поэтому после создания базы данных Clinic перейдите к ней и скопируйте строку подключения (это можно сделать в любое время, но так как мы уже находимся на портале целесообразно скопировать ее сейчас).

1. Последовательно щелкните **Базы данных SQL** > **Clinic** > **Показать строки подключения к базе данных**.
2. Скопируйте строку подключения для **ADO.NET**.

	![скопировать строку подключения](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)


## Подключение к базе данных с помощью SQL Server Management Studio.

Нам нужно открыть среду SQL Server Management Studio и подключиться к серверу через базу данных Clinic.


1. Откройте SQL Server Management Studio (щелкните **Подключиться** > **Компонент Database Engine...**, чтобы открыть окно **Подключение к серверу**, если оно еще не открыто).
2. Введите имя сервера и учетные данные. Имя сервера можно найти в колонке базы данных SQL и в строке подключения, которую вы скопировали ранее. Введите полное имя сервера вместе с именем домена *database.windows.net*.

	![скопировать строку подключения](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

3. Если откроется окно **Новое правило брандмауэра**, войдите в Azure, и среда SQL Server Management Studio создаст такое правило для вас.


## Создание таблицы

Сначала мы создадим обычную таблицу, в которой будут содержаться данные о пациентах (пока без шифрования, его мы настроим в следующем разделе).

1. Разверните узел **Базы данных**.
1. Щелкните правой кнопкой мыши базу данных **Clinic** и выберите пункт **Создать запрос**.
2. Вставьте следующий сценарий Transact-SQL в окно нового запроса и нажмите кнопку **Выполнить**.


        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1), 
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL, 
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## Шифрование некоторых столбцов (и настройка постоянного шифрования)

В SQL Server Management Studio есть мастер, с помощью которого можно легко настроить функцию постоянного шифрования. Он позволяет автоматически настроить главный ключ столбца, ключ шифрования столбца и зашифрованные столбцы.

1. Последовательно разверните узлы **Базы данных** > **Clinic** > **Таблицы**.
2. Щелкните правой кнопкой таблицу **Patients** и выберите пункт **Шифрование столбцов...**, чтобы открыть мастер настройки постоянного шифрования.

    ![зашифровать столбцы](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

3. **Выполните действия на странице Выбор столбцов.**

    На странице **Введение** нажмите кнопку **Далее**, чтобы открыть страницу **Выбор столбцов**, где можно выбрать столбцы, которые требуется зашифровать, [тип шифрования и ключ шифрования столбца](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2).

    Для каждого пациента необходимо зашифровать данные в столбцах **SSN** и **BirthDate**. Для столбца SSN будет использоваться детерминированное шифрование, которое поддерживает уточняющие запросы на соответствие условию, операции объединения и группировки, а для столбца BirthDate — случайное шифрование, которое не поддерживает какие-либо операции.

    Задайте для параметра **Тип шифрования** столбца SSN значение **Детерминированное**, а для столбца BirthDate — **Случайное** и нажмите кнопку **Далее**.

    ![зашифровать столбцы](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

4. **Настройка главного ключа**

    На странице **Настройка главного ключа** можно настроить главный ключ столбца и выбрать поставщика хранилища ключей, в котором будет храниться этот ключ. В настоящее время главные ключи столбцов можно хранить в хранилище сертификатов Windows, хранилище ключей Azure или аппаратном модуле безопасности.

    В этом руководстве показано, как сохранить ключи в хранилище ключей Azure.

    1.     Выберите **хранилище ключей Azure**.
    1.     В раскрывающемся списке выберите нужное хранилище ключей.
    1.     Нажмите кнопку **Далее**.

    ![настройка главного ключа](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)


5. **Проверка**

    Можно зашифровать столбцы сейчас или сохранить сценарий PowerShell и выполнить его позже. Для целей этого руководства выберите **Завершить сейчас** и нажмите кнопку **Далее**.

6. **Сводка**

    Убедитесь, что все параметры настроены правильно, и нажмите кнопку **Готово**, чтобы завершить настройку функции постоянного шифрования.


    ![summary](./media/sql-database-always-encrypted-azure-key-vault/summary.png)


### Задания, выполненные мастером

После завершения работы мастера в базе данных будет настроена функция постоянного шифрования и выполнены следующие задания.

- Главный ключ столбца будет создан и сохранен в хранилище ключей Azure.
- Ключ шифрования столбца будет создан и сохранен в хранилище ключей Azure.
- Настройка шифрования для выбранных столбцов. (Сейчас в таблице Patients нет данных, но как только они появятся, они будут зашифрованы в выбранных столбцах.)

Чтобы убедиться, что ключи созданы в SQL Server Management Studio, последовательно разверните узлы **Clinic** > **Безопасность** > **Ключи постоянного шифрования**. Таким образом можно увидеть новые ключи, созданные с помощью мастера.


## Создание клиентского приложения, которое работает с зашифрованными данными

Теперь, когда функция постоянного шифрования настроена, мы можем создать приложение, которое может выполнять операции вставки и выбора в зашифрованных столбцах.

> [AZURE.IMPORTANT] При передаче открытого текста на сервер со столбцами с постоянным шифрованием приложение должно использовать объекты [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx). Передача значений литералов без использования объектов SqlParameter приведет к возникновению исключения.


1. Откройте Visual Studio и создайте консольное приложение на языке C#. Убедитесь, что для проекта используется платформа **.NET Framework** версии 4.6 или более поздней.
2. Укажите имя проекта **AlwaysEncryptedConsoleApp** и нажмите кнопку **ОК**.


	![новое консольное приложение](./media/sql-database-always-encrypted-azure-key-vault/console-app.png)


3. Установите следующие пакеты NuGet, последовательно выбрав **Сервис** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.

В консоли диспетчера пакетов выполните следующие 2 строки кода:
    
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory


   
## Изменение строки подключения для активации функции постоянного шифрования

В этом разделе объясняется, как активировать функцию постоянного шифрования в строке подключения к базе данных. Непосредственное изменение нового консольного приложения описывается в разделе **Пример консольного приложения с постоянным шифрованием**.


Чтобы активировать функцию постоянного шифрования, необходимо добавить ключевое слово **Column Encryption Setting** к строке подключения и установить для него значение **Enabled**.

Это можно задать непосредственно в строке подключения или с помощью [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). В разделе ниже показано, как использовать **SqlConnectionStringBuilder** для примера приложения.



### Активация функции постоянного шифрования в строке подключения

Добавьте в строку подключения следующее ключевое слово:

    Column Encryption Setting=Enabled


### Активация функции постоянного шифрования с помощью SqlConnectionStringBuilder

В коде ниже показано, как активировать функцию постоянного шифрования, указав параметр [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) со значением [Enabled](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder = 
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting = 
       SqlConnectionColumnEncryptionSetting.Enabled;



## Пример консольного приложения с функцией постоянного шифрования

В этом примере показано, как:

- Изменить строку подключения для активации функции постоянного шифрования.
- Зарегистрируйте хранилище ключей Azure в качестве поставщика хранилища ключей приложения.  
- Вставить данные в зашифрованные столбцы.
- Выбрать запись, выполнив фильтрацию по конкретному значению в зашифрованном столбце.

Замените содержимое **Program.cs** следующим. Замените строку подключения для глобальной переменной connectionString в строке прямо над методом Main на правильную строку подключения, указанную на портале Azure. Это единственное изменение, которое необходимо внести в этот код.

Теперь запустите приложение, чтобы увидеть функцию постоянного шифрования в действии.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;
    
    namespace AlwaysEncryptedConsoleAKVApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure Portal.
        static string connectionString = @"<connection string from the portal>";
        static string clientId = @"<client id from step 7 above>";
        static string clientSecret = "<key from step 13 above>";


        static void Main(string[] args)
        {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted 
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider()
        {

            _clientCredential = new ClientCredential(clientId, clientSecret);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope)
        {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }



## Как проверить, что данные шифруются

Чтобы проверить, что данные на сервере действительно зашифрованы, можно запросить данные пациентов через SQL Server Management Studio (используя текущее подключение, где не задан параметр Column Encryption Setting).

Выполните следующий запрос в базе данных Clinic:

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Вы увидите, что в зашифрованных столбцах не содержатся данные в виде открытого текста.

   ![новое консольное приложение](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)


Чтобы получить доступ к данным в незашифрованном виде, нужно указать в строке подключения **Column Encryption Setting=enabled**.

1. В **обозревателе объектов** SQL Server Management Studio щелкните сервер правой кнопкой мыши и выберите пункт **Отключить**.
2. Щелкните **Подключиться** > **Компонент Database Engine**, чтобы открыть окно **Подключение к серверу**, и нажмите кнопку **Параметры**.
3. Щелкните **Дополнительные параметры подключения** и введите **Column Encryption Setting=enabled**.

	![новое консольное приложение](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)

4. Выполните следующий запрос в базе данных Clinic:

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Теперь в зашифрованных столбцах отображаются незашифрованные данные.


	![новое консольное приложение](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## Дальнейшие действия
После создания базы данных с функцией постоянного шифрования вы можете сделать следующее:

- [Сменить и очистить ключи](https://msdn.microsoft.com/library/mt607048.aspx).
- [Перенести данные, которые уже зашифрованы с использованием постоянного шифрования.](https://msdn.microsoft.com/library/mt621539.aspx)



## Сопутствующая информация

- [Always Encrypted (client Development) (Постоянное шифрование (разработка клиентских приложений))](https://msdn.microsoft.com/library/mt147923.aspx)
- [Transparent Data Encryption (Прозрачное шифрование данных)](https://msdn.microsoft.com/library/bb934049.aspx)
- [SQL Server Encryption (Шифрование SQL Server)](https://msdn.microsoft.com/library/bb510663.aspx)
- [Always Encrypted Wizard (Мастер настройки постоянного шифрования)](https://msdn.microsoft.com/library/mt459280.aspx)
- [Блог по функции постоянного шифрования](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

<!---HONumber=AcomDC_0302_2016-->