<properties
   pageTitle="Общие сведения о безопасности Базы данных SQL"
   description="Узнайте об обеспечении безопасности Базы данных SQL Azure и SQL Server, включая различия между облаком и локальным сервером SQL Server, когда дело касается проверки подлинности, авторизации, безопасность подключения, шифрования и обеспечения соответствия требованиям."
   services="sql-database"
   documentationCenter=""
   authors="tmullaney"
   manager="jeffreyg"
   editor="jeffreyg"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="01/22/2016"
   ms.author="thmullan;jackr"/>


# Защита Базы данных SQL

## Обзор

В этой статье рассматриваются основы обеспечения безопасности уровня данных приложения, использующего Базу данных SQL Azure. В частности, эта статья поможет приступить к работе с ресурсами для ограничения доступа, защиты данных и мониторинга действий с базой данных, созданной в [учебнике по созданию первой базы данных SQL](sql-database-get-started.md). Полный обзор функции безопасности, доступных для всех видов SQL, см. в статье [Центр обеспечения безопасности для Базы данных SQL Azure и SQL Server Database Engine](https://msdn.microsoft.com/library/bb510589). Также см. дополнительные сведения в техническом документе [Безопасность и База данных SQL Azure](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF-файл).

## Безопасность подключения

Под безопасностью подключения подразумеваются способы ограничения и защиты подключений к базе данных с помощью правил брандмауэра и шифрования подключения.

Правила брандмауэра используются как сервером, так и базой данных для отклонения попыток подключиться с IP-адресов, которые не были явно включены в белый список. Чтобы разрешить подключение к новой базе данных из вашего приложения или с клиентского компьютера с общедоступным IP-адресом, сначала необходимо создать правило брандмауэра уровня сервера. Для этого воспользуйтесь классическим порталом Azure, API REST или PowerShell. В брандмауэре сервера рекомендуется максимально ограничить диапазоны разрешенных IP-адресов. Дополнительные сведения см. в статье [Брандмауэр Базы данных SQL Azure](https://msdn.microsoft.com/library/ee621782).

Во время каждого сеанса передачи данных в Базу данных SQL Azure и из нее все подключения к базе должны быть зашифрованы (SSL/TLS). В строке подключения приложения необходимо настроить параметры шифрования подключения, а также указать *отсутствие* доверия сертификату сервера (это происходит автоматически при копировании строки подключения из классического портала Azure), в противном случае подключение не будет проверять подлинность сервера и будет уязвимо для атак типа "злоумышленник в середине". Например, для драйвера ADO.NET такими параметрами строки подключения являются **Encrypt=True** и **TrustServerCertificate=False**. Дополнительные сведения см. в разделе, посвященном [шифрованию подключений к Базе данных SQL Azure и проверке сертификатов](https://msdn.microsoft.com/library/azure/ff394108#encryption).


## Аутентификация

В процессе аутентификации предлагается подтвердить личность пользователя при подключении к базе данных. База данных SQL поддерживает два типа аутентификации:

 - **Аутентификация SQL**, которая использует имя пользователя и пароль.
 - **Аутентификация Azure Active Directory**, которая использует удостоверения, контролируемые Azure Active Directory, и поддерживается управляемыми и интегрированными доменами.

При создании логического сервера для базы данных вы указали имя для входа "Администратор сервера" и пароль. Используя эти учетные данные, можно выполнить проверку подлинности для любой базы данных на этом сервере в качестве владельца базы данных (или "dbo"). Если вы хотите использовать аутентификацию Azure Active Directory, необходимо создать другого администратора сервера с именем Azure AD admin, которому разрешено администрировать пользователей и группы Azure AD. Этот администратор также может выполнять все операции, доступные обычному администратору. В разделе [Подключение к базе данных SQL с использованием проверки подлинности Azure Active Directory](sql-database-aad-authentication.md) содержится пошаговое руководство по созданию администратора Azure AD для включения аутентификации Azure Active Directory.

Но рекомендуется, чтобы приложение использовало другую учетную запись для аутентификации. Это позволит ограничить разрешения, предоставляемые приложению, и снизить риски вредоносных действий в случае, если оно уязвимо для атак путем внедрения кода SQL. Рекомендуемый подход заключается в создании [пользователя автономной базы данных](https://msdn.microsoft.com/library/ff929188), что позволяет приложению выполнять аутентификацию непосредственно в одной базе данных. Чтобы создать пользователя автономной базы данных, использующего аутентификацию SQL, можно выполнить следующую команду T-SQL при подключении к пользовательской базе данных с учетными данными администратора сервера:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Если администратор Azure AD создан, можно создать пользователя автономной базы данных, использующего аутентификацию Azure Active Directory. Для этого выполните следующую команду T-SQL при подключении к пользовательской базе данных с именем администратора Azure AD:

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

В любом случае в строке подключения приложения должны быть указаны именно эти учетные данные, а не данные администратора сервера.

Дополнительные сведения о проверке подлинности для работы с Базой данных SQL см. в статье [Управление базами данных, именами для входа и пользователями в Базе данных SQL Azure](sql-database-manage-logins.md).


## Авторизация
Авторизация подразумевает набор действий, которые можно выполнять в Базе данных SQL Azure. Такой набор действий определяется разрешениями учетной записи пользователя и присвоенными ей ролями. Обычно пользователям рекомендуется предоставлять наименьшие необходимые привилегии. Базой данных SQL Azure можно с легкостью управлять с использованием ролей в T-SQL:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

Учетной записи администратора сервера, которая используется для подключения, присвоена роль db\_owner, обладатель которой может выполнять любые действия в базе данных. Сохраните эту учетную запись для развертывания обновлений схемы и выполнения других действий по управлению. Для подключения к базе данных из приложения с использованием наименьших привилегий, необходимых приложению, используйте учетную запись "ApplicationUser" с более ограниченными разрешениями.

Существуют способы еще больше ограничить действия пользователя с Базой данных SQL Azure.

* Для создания учетных записей пользователей приложения с более широкими возможностями или учетных записей для управления с меньшим набором возможностей можно использовать [роли базы данных](https://msdn.microsoft.com/library/ms189121), отличные от db\_datareader и db\_datawriter.
* Детальная настройка [разрешений](https://msdn.microsoft.com/library/ms191291) позволяет указать операции, которые можно выполнять с отдельными столбцами, таблицами, представлениями, процедурами и другими объектами в базе данных.
* Для безопасного временного повышения прав можно использовать [олицетворение](https://msdn.microsoft.com/library/vstudio/bb669087) и [подписание модулей](https://msdn.microsoft.com/library/bb669102).
* [Безопасности на уровне строк](https://msdn.microsoft.com/library/dn765131) может использоваться, чтобы ограничить доступ пользователя к строкам.
* [Маскирование данных](sql-database-dynamic-data-masking-get-started.md) позволяет снизить уязвимость конфиденциальных данных.
* Чтобы ограничить действия, которые можно выполнять в базе данных, можно использовать [хранимые процедуры](https://msdn.microsoft.com/library/ms190782).

Управление базами данных и логическими серверами на классическом портале Azure или с помощью API-интерфейса диспетчера ресурсов Azure контролируется путем назначения ролей для учетной записи пользователя портала. Дополнительные сведения об этом см. в разделе [Контроль доступа на основе ролей на портале Azure](../role-based-access-control-configure.md).


## Шифрование

База данных SQL Azure может помочь защитить ваши данные путем их шифрования, когда они находятся на хранении или хранятся в файлах базы данных и в ее резервных копиях, с помощью [прозрачного шифрования данных](http://go.microsoft.com/fwlink/?LinkId=526242). Для шифрования базы данных подключитесь к ней в качестве владельца базы данных и выполните следующую команду:

```
CREATE DATABASE ENCRYPTION KEY
   WITH ALGORITHM = AES_256
   ENCRYPTION BY SERVER CERTIFICATE ##MS_TdeCertificate##;

ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Другие способы для шифрования секретных данных включают следующие:

* [Шифрование на уровне ячейки](https://msdn.microsoft.com/library/ms179331.aspx) для шифрования определенных столбцов или даже ячеек данных с использованием разных ключей шифрования.
* Если требуется аппаратный модуль безопасности или централизованное управление иерархии ключей шифрования, рассмотрите возможность использования [ хранилища ключей Azure с сервером SQL Server на виртуальной машине Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).
* [Всегда зашифрованы](https://msdn.microsoft.com/library/mt163865.aspx) (в предварительной версии) делает шифрование прозрачным для приложений и позволяет клиентам шифровать конфиденциальные данные внутри клиентских приложений без совместного использования ключей шифрования с Базой данных SQL.

## Аудит

Аудит и отслеживание событий базы данных может помочь в постоянном обеспечении соответствия нормативным требованиям и обнаружении подозрительных действий. Аудит Базы данных SQL позволяет фиксировать события, происходящие в базе данных, в журнале аудита в вашей учетной записи хранения Azure. Кроме того, благодаря интеграции аудита Базы данных SQL с Microsoft Power BI обеспечиваются упрощенное создание подробных отчетов и проведение детализированного анализа. Дополнительные сведения см. в статье [Приступая к работе с аудитом Базы данных SQL](sql-database-auditing-get-started.md).

## Соответствие нормативным требованиям

Помимо обозначенных выше функций и возможностей, которые могут помочь обеспечить соответствие вашего приложения различным требованиям безопасности, в отношении Базы данных SQL Azure регулярно проводится аудит, а сама база данных сертифицирована в соответствии с рядом стандартов. Дополнительные сведения см. в [Центре управления безопасностью Microsoft Azure](https://azure.microsoft.com/support/trust-center/), где представлен актуальный список [сертификатов соответствия Базы данных SQL](https://azure.microsoft.com/support/trust-center/services/).

<!---HONumber=AcomDC_0128_2016-->