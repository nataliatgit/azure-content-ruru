<properties
   pageTitle="Подключение к базе данных SQL с использованием проверки подлинности Azure Active Directory | Microsoft Azure"
   description="Сведения о подключении к базе данных SQL с использованием проверки подлинности Azure Active Directory."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor="jeffreyg"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="02/01/2016"
   ms.author="rick.byham@microsoft.com"/>

# Подключение к базе данных SQL с использованием проверки подлинности Azure Active Directory

Проверка подлинности Azure Active Directory — это механизм подключения к базе данных SQL Microsoft Azure с помощью удостоверений в Azure Active Directory (Azure AD). С помощью проверки подлинности Azure Active Directory можно централизованно управлять удостоверениями пользователей базы данных и другими службами Майкрософт. Централизованное управление удостоверениями позволяет использовать единое расположение для управления пользователями базы данных SQL и упрощает управление разрешениями. Это дает такие преимущества:

- наличие альтернативного варианта проверки подлинности SQL Server;
- возможность остановить увеличение количества пользователей на серверах баз данных;
- изменение паролей в одном расположении;
- клиенты могут управлять разрешениями базы данных с помощью внешних групп (AAD);
- возможность исключить хранение паролей с помощью встроенной проверки подлинности Windows и других видов проверки подлинности, поддерживаемых Azure Active Directory;
- при проверке подлинности Azure Active Directory используются данные пользователей автономной базы данных для проверки подлинности удостоверений на уровне базы данных.

> [AZURE.IMPORTANT] Проверка подлинности Azure Active Directory — это функция, работающая в режиме предварительной версии. Ее использование регулируется условиями предварительной версии, указанными в лицензионном соглашении (например, в соглашении Enterprise, соглашении для Microsoft Azure или соглашении Microsoft Online Subscription), а также любыми применимыми условиями, которые можно найти на странице [Дополнительные условия использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Для настройки и использования проверки подлинности Azure Active Directory выполните следующие действия.

1. Создайте и заполните каталог Azure Active Directory.
2. Убедитесь, что ваша база данных расположена в базе данных SQL Azure V12.
3. Свяжите каталог Active Directory с подпиской Azure или измените каталог, который сейчас связан с этой подпиской (этот шаг можно пропустить).
4. Создайте администратора Azure Active Directory для сервера SQL Azure.
5. Настройте клиентские компьютеры.
6. Создайте пользователей автономной базы данных в базе данных, сопоставленной с удостоверениями Azure AD.
7. Подключитесь к базе данных с помощью удостоверений Azure AD.


## Архитектура доверия

На следующей общей схеме представлена архитектура решения, в котором используется проверка подлинности Azure AD для базы данных SQL Azure. Стрелки обозначают пути обмена данными.

![схема проверки подлинности aad][1]

На следующей схеме показаны федерация, отношения доверия и отношения размещения. Все эти компоненты позволяют клиенту подключиться к базе данных, отправляя маркер, проверка подлинности которого была выполнена с помощью Azure AD и который является доверенным для базы данных. Важно понимать, что для доступа к базе данных с использованием проверки подлинности Azure AD необходимо связать подписку размещения с Azure Active Directory.

![отношение подписки][2]

## Структура администраторов

При использовании проверки подлинности Azure AD существует две учетные записи администраторов сервера базы данных SQL: учетная запись первоначального администратора сервера SQL Server и учетная запись администратора Azure AD. Создать в пользовательской базе данных первого пользователя автономной базы данных Azure AD может только администратор с учетной записью Azure AD. Администратор Azure AD может использовать для входа имя пользователя Azure AD или имя группы Azure AD. Если учетная запись администратора является учетной записью группы, ее может использовать любой участник группы. По этой причине для одного экземпляра SQL Server может существовать несколько администраторов Azure AD. Использование учетной записи группы в качестве учетной записи администратора повышает управляемость. Это позволяет централизованно добавлять и удалять членов группы в Azure AD, не изменяя пользователей или разрешения в базе данных SQL. За один раз можно настроить только одного администратора Azure AD (пользователя или группу).

![структура администраторов][3]

## Разрешения

Чтобы создавать новых пользователей, в базе данных должно быть разрешение **ИЗМЕНЕНИЕ ЛЮБОГО ПОЛЬЗОВАТЕЛЯ**. Разрешение **ИЗМЕНЕНИЕ ЛЮБОГО ПОЛЬЗОВАТЕЛЯ** можно предоставить любому пользователю базы данных. Разрешение **ИЗМЕНЕНИЕ ЛЮБОГО ПОЛЬЗОВАТЕЛЯ** также предоставляется администраторам сервера и пользователям базы данных с разрешениями **УПРАВЛЕНИЕ БАЗОЙ ДАННЫХ** или **ИЗМЕНЕНИЕ БАЗЫ ДАННЫХ** для этой базы данных, а также членам роли **db\_owner** в базе данных.

Чтобы создать пользователя автономной базы данных в базе данных SQL Azure, необходимо подключиться к базе данных с помощью удостоверения Azure AD. Чтобы создать первого пользователя автономной базы данных, необходимо подключиться к ней с использованием учетной записи администратора Azure AD (который является владельцем базы данных). См. шаги 4 и 5 ниже.

## Функции и ограничения Azure AD

На сервере Azure SQL Server можно подготовить следующих членов Azure Active Directory. - Собственные члены — члены, созданные в Azure AD в управляемом домене или в домене клиента. Дополнительные сведения см. в статье [Добавление собственного имени домена в Azure AD]( https://azure.microsoft.com/documentation/articles/active-directory-add-domain/).
- Федеративные члены домена — члены, созданные в Azure AD с федеративным доменом. Дополнительные сведения см. в статье [Microsoft Azure теперь поддерживает федерацию с Windows Server Active Directory]( https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/). Импортированные члены из других служб Azure Active Directory, являющиеся собственными или федеративными членами домена. Группы Active Directory, созданные как группы безопасности.

Учетные записи Майкрософт (например, outlook.com, hotmail.com, live.com) и гостевые учетные записи (например, gmail.com, yahoo.com) не поддерживаются. Возможность войти на сайт [https://login.live.com](https://login.live.com) с помощью учетной записи и пароля означает, что вы используете учетную запись Майкрософт, которая не поддерживается при проверке подлинности Azure AD для базы данных SQL Azure.

### Дополнительные замечания

- Для повышения управляемости рекомендуем подготовить специальную группу Azure Active Directory от имени администратора.
- За один раз можно настроить только одного администратора сервера Azure SQL Server (пользователя или группу).
- Сначала к серверу Azure SQL Server может подключаться только администратор Azure Active Directory, используя учетную запись Azure Active Directory. Затем администратор Active Directory может настроить других пользователей базы данных Azure Active Directory.
- Мы рекомендуем установить время ожидания подключения в 30 секунд.
- Некоторые средства, такие как бизнес-аналитика и Excel, не поддерживаются.
- Проверка подлинности Azure Active Directory поддерживает только **поставщика данных .NET Framework для SQL Server** (версия не ниже .NET Framework 4.6). Поэтому набор Management Studio (доступен в SQL Server 2016) и приложения уровня данных (DAC и BACPAC) могут подключаться, а **sqlcmd.exe** — нет, поскольку для **sqlcmd** используется поставщик ODBC.
- Двухфакторная проверка подлинности и другие виды интерактивной проверки подлинности не поддерживаются.


## 1\. Создание и заполнение каталога Azure Active Directory

Создайте каталог Azure Active Directory и заполните его пользователями и группами. А именно:

- Создайте управляемый домен исходного домена Azure AD.
- Выполните федерацию локальных доменных служб Active Directory с Azure Active Directory.
- С помощью инструмента **AD FS** в разделе **Служба**, **Конечные точки** включите **WS-Trust 1.3** для URL-пути **/adfs/services/trust/13/windowstransport**.

Дополнительные сведения см. в статьях [Добавление собственного имени домена в Azure AD]( https://azure.microsoft.com/documentation/articles/active-directory-add-domain/), [Microsoft Azure теперь поддерживает федерацию с Windows Server Active Directory]( https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Управление каталогом Azure AD]( https://msdn.microsoft.com/library/azure/hh967611.aspx) и [Управление службой Azure AD с помощью Windows PowerShell]( https://msdn.microsoft.com/library/azure/jj151815.aspx).

## 2\. Убедитесь, что ваша база данных расположена в базе данных SQL Azure V12.

Проверка подлинности Azure Active Directory поддерживается в последней версии базы данных SQL V12. Сведения о базе данных SQL V12 и о том, как определить, доступна ли она в вашем регионе, см. в статье [Новые возможности базы данных SQL V12](sql-database-v12-whats-new.md).

Если база данных уже существует, убедитесь, что она размещена в базе данных SQL V12, подключившись к ней (например, с помощью SQL Server Management Studio) и выполнив команду `SELECT @@VERSION;`. Ожидаемый результат для базы данных в базе данных SQL V12 — по меньшей мере **Microsoft SQL Azure (RTM) — 12.0**.

Если база данных размещена не в базе данных SQL V12, см. статью [Планирование и подготовка к обновлению до версии базы данных SQL Azure V12](sql-database-v12-plan-prepare-upgrade.md), а затем перейдите на классический портал Azure, чтобы перенести базу данных в базу данных SQL V12.

Также можно создать новую базу данных в базе данных SQL версии 12, выполнив действия, описанные в статье [Создание своей первой Базы данных SQL Azure](sql-database-get-started.md). **Совет**. Прочитайте следующий шаг полностью, прежде чем выбрать подписку для новой базы данных.

## 3\. Связывание каталога Active Directory с подпиской Azure или изменение каталога, который сейчас связан с этой подпиской (этот шаг можно пропустить)

Чтобы связать базу данных с каталогом Azure AD для вашей организации, сделайте каталог доверенным для подписки Azure, в которой размещена база данных. Дополнительные сведения см. в статье [Связь подписок Azure с Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).

**Дополнительные сведения.** Каждая подписка Azure связана отношением доверия с экземпляром Azure AD. Это означает, что она доверяет каталогу проверять подлинность пользователей, служб и устройств. Несколько подписок могут доверять одному и тому же каталогу, но одна конкретная подписка доверяет только одному каталогу. На вкладке **Параметры** на сайте [https://manage.windowsazure.com/](https://manage.windowsazure.com/) можно проверить, какой каталог является доверенным для вашей подписки. Данное отношение доверия, которое подписка имеет с каталогом, отличается от отношения, которую подписка имеет со всеми другими ресурсами в Azure (веб-сайтами, базами данных и т. д.), которые больше похожи на дочерние ресурсы подписки. Если срок действия подписки истекает, доступ к другим ресурсам, связанным с этой подпиской, также прекращается. Однако каталог останется в Azure, вы можете связать другую подписку с этим каталогом и продолжать управлять пользователями каталога. Дополнительные сведения о ресурсах см. в статье [Основные сведения о доступе к ресурсам в Azure](https://msdn.microsoft.com/library/azure/dn584083.aspx).

Ниже приведены пошаговые инструкции по изменению связанного каталога для заданной подписки.

1. Подключитесь к [классическому порталу Azure](https://manage.windowsazure.com/) с помощью учетной записи администратора подписки Azure.
2. В области слева выберите элемент **ПАРАМЕТРЫ**.
3. Ваши подписки отобразятся в окне параметров. Если нужная подписка не отображается, в верхней части окна щелкните **Подписки**, в раскрывающемся списке **ФИЛЬТРОВАТЬ ПО КАТАЛОГУ** выберите каталог, содержащий подписки, и щелкните **ПРИМЕНИТЬ**.

	![выбрать подписку][4]
4. В области **Параметры** выберите подписку и внизу страницы щелкните **ИЗМЕНИТЬ КАТАЛОГ**.

	![ad-settings-portal][5]
5. В окне **ИЗМЕНЕНИЕ КАТАЛОГА** выберите службу Azure Active Directory, связанную с сервером SQL Server, и нажмите кнопку со стрелкой, чтобы перейти к следующему шагу.

	![edit-directory-select][6]
6. В диалоговом окне **ПОДТВЕРЖДЕНИЕ сопоставления каталогов** убедитесь, что отображается текст **Все соадминистраторы будут удалены**.

	![edit-directory-confirm][7]
7. Нажмите на кнопку с галочкой, чтобы перезагрузить портал.

> [AZURE.NOTE] После изменения каталога доступ ко всем соадминистраторам, пользователям и группам Azure AD, а также пользователям ресурсов с поддержкой каталога будет запрещен. Указанные пользователи не смогут больше получить доступ к этой подписке или ее ресурсам. Доступ к субъектам на основе нового каталога сможет настроить только администратор службы. Применение этих изменений ко всем ресурсам может занять значительное время. В случае изменения каталога также будет изменен администратор Azure AD для базы данных SQL, а доступ всех существующих пользователей Azure AD к базе данных SQL будет запрещен. Необходимо будет повторно указать администратора Azure AD (как описано ниже) и создать новых пользователей Azure AD.

## 4\. Создание администратора Azure Active Directory для сервера Azure SQL Server

Каждый сервер Azure SQL Server сначала имеет одну учетную запись администратора сервера, который является администратором всего сервера Azure SQL Server. Необходимо создать второго администратора сервера, то есть учетную запись Azure AD. Этот участник создается как пользователь автономной базы данных в базе данных master. Администраторы с учетными записями администратора сервера являются членами роли **db\_owner** в каждой пользовательской базе данных. Они входят в каждую такую базу данных как пользователи **dbo**. Дополнительные сведения об учетных записях администраторов сервера см. в статье [Управление базами данных и учетными записями в базе данных SQL Azure](sql-database-manage-logins.md) и в разделе **Имена входа и пользователи** статьи [Безопасность в базе данных SQL Azure: рекомендации и ограничения](sql-database-security-guidelines.md).

> [AZURE.NOTE] Пользователи без учетной записи Azure AD (включая учетную запись администратора Azure SQL Server) не могут создавать пользователей на основе Azure AD, так как у них нет разрешения на проверку предложенных пользователей базы данных с помощью Azure AD.

### Подготовка администратора Azure Active Directory для сервера Azure SQL Server с помощью классического портала Azure

1. В правом верхнем углу [классического портала Azure](https://portal.azure.com/) щелкните имя подключения, чтобы открыть список доступных каталогов Active Directory. Выберите нужный экземпляр Active Directory в качестве Azure AD по умолчанию. С помощью этого шага можно связать подписку Active Directory с базой данных SQL Azure, чтобы одна и та же подписка использовалась для Azure AD и SQL Server.

	![choose-ad][8]
2. В области слева выберите **Серверы SQL Server**, выберите нужное значение **SQL Server**, а затем в верхней части колонки **SQL Server** щелкните **Параметры**.

	![параметры ad][9]
3. В колонке **Параметры** щелкните **Администратор Active Directory (предварительная версия)** и примите условия предварительной версии.
4. В колонке **Администратор Active Directory (предварительная версия)** щелкните, чтобы ознакомиться с условиями предварительной версии, а затем нажмите кнопку **ОК**, чтобы принять их.
5. В колонке **Администратор Active Directory (предварительная версия)** щелкните **Администратор Active Directory**, а затем щелкните **Задать администратора** вверху.
6. В колонке **Добавление администратора** найдите пользователя, выберите пользователя (или группу), чтобы назначить его администратором, и щелкните **Выбрать**. (В колонке «Администратор Active Directory» отобразятся все члены и группы Active Directory. Пользователей или группы, которые выделены серым цветом, нельзя выбрать; они не поддерживаются ролью администратора Azure AD. См. список поддерживаемых администраторов в разделе **Функции и ограничения Azure AD** выше.) Управление доступом на основе ролей (RBAC) применяется только к порталу и не распространяется на SQL Server.
7. В верхней части колонки **Администратор Active Directory** нажмите кнопку **Сохранить**. ![выбор администратора][10]

	Изменение администратора может занять несколько минут. После этого новый администратор появится в поле **Администратор Active Directory**.

> [AZURE.NOTE] При настройке администратора Azure AD новое имя администратора (пользователя или группы) не может уже присутствовать в базе данных master как имя для входа с аутентификацией SQL Server. Если оно присутствует, настройка администратора Azure AD завершится ошибкой. Будет выполнен откат его создания и появится сообщение, что такой администратор (имя) уже существует. Поскольку такое имя для входа с аутентификацией SQL Server не входит в Azure AD, любая попытка подключиться к серверу с помощью аутентификации Azure AD завершится неудачей.

В дальнейшем удалить администратора можно нажатием кнопки **Удалить администратора** в верхней части колонки **Администратор Active Directory**.

### Подготовка администратора Azure AD для сервера Azure SQL Server с помощью PowerShell



Чтобы выполнять командлеты PowerShell, необходимо установить и запустить Azure PowerShell. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

Для подготовки администратора Azure AD необходимо выполнить следующие команды Azure PowerShell:

- Add-AzureRmAccount
- Select-AzureRmSubscription


Командлеты, используемые для подготовки администратора Azure AD и управления им:

| Имя командлета | Описание |
|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx) | Подготавливает администратора Azure Active Directory для сервера Azure SQL Server (должен входить в текущую подписку). |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx) | Удаляет администратора Azure Active Directory для сервера Azure SQL Server. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx) | Возвращает сведения о текущем администраторе Azure Active Directory, настроенном для сервера Azure SQL Server. |

Чтобы получить дополнительные сведения о каждой из этих команд, используйте команду PowerShell get-help, например ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

Приведенный ниже сценарий выполняет подготовку группы администраторов Azure AD с именем **DBA\_Group** (идентификатор объекта `40b79501-b343-44ed-9ce7-da4c8cc7353f`) для сервера **demo\_server** в группе ресурсов с именем **Group-23**.

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group"
```

Входной параметр **DisplayName** принимает отображаемое имя Azure AD или имя участника-пользователя. Например, ``DisplayName="John Smith"`` и ``DisplayName="johns@contoso.com"``. Для групп Azure AD поддерживается только отображаемое имя Azure AD.

> [AZURE.NOTE] Команда Azure PowerShell ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` не запрещает подготовку администраторов Azure AD для неподдерживаемых пользователей. Неподдерживаемого пользователя можно подготовить, но он не сможет подключиться к базе данных (См. список поддерживаемых администраторов в разделе **Функции и ограничения Azure AD** выше.)

В следующем примере используется необязательный параметр **ObjectID**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [AZURE.NOTE] Параметр **ObjectID** Azure AD является обязательным, если параметр **DisplayName** не уникален. Чтобы получить значения **ObjectID** и **DisplayName**, используйте раздел Active Directory классического портала Azure. Там можно просмотреть свойства пользователя или группы.

Следующий пример возвращает сведения о текущем администраторе Azure AD для сервера Azure SQL Server:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" –ServerName "demo_server" | Format-List
```

В следующем примере выполняется удаление администратора Azure AD: 
```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" –ServerName "demo_server"
```

## 5\. Настройка клиентских компьютеров

Приложения или пользователи подключаются к базе данных SQL Azure, используя удостоверения Azure AD, с клиентских компьютеров. На эти компьютеры необходимо установить указанное ниже программное обеспечение.

- .NET Framework 4.6 или более поздней версии можно скачать на веб-странице [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Библиотека аутентификации Azure Active Directory для SQL Server (**ADALSQL.DLL**) доступна на нескольких языках (версии x86 и amd64) в центре загрузки в [библиотеке аутентификации Microsoft Active Directory для Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

### Средства

- Установка [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) или [SQL Server Data Tools для Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) соответствует требованиям .NET Framework 4.6.
- SSMS устанавливает **ADALSQL.DLL** версии x86. (В настоящий момент SSMS не запрашивает перезагрузку после установки. Это будет исправлено в будущей CTP-версии.)
- SSDT устанавливает **ADALSQL.DLL** версии amd64. SSDT только частично поддерживает проверку подлинности Azure AD.
- Последняя версия Visual Studio со страницы [скачивания Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) соответствует требованиям .NET Framework 4.6, но не устанавливает необходимую версию amd64 **ADALSQL.DLL**.

## 6\. Создание пользователей автономной базы данных в базе данных, сопоставленной с удостоверениями Azure AD

### Сведения о пользователях автономной базы данных

Для проверки подлинности Azure Active Directory необходимо, чтобы пользователи базы данных создавались как пользователи автономной базы данных. Пользователь автономной базы данных на основе удостоверения Azure AD —это пользователь, у которого нет имени для входа в базу данных master и который сопоставляется с удостоверением в каталоге Azure AD, связанном с базой данных. Удостоверение Azure AD может быть учетной записью отдельного пользователя или группы. Дополнительные сведения о пользователях автономной базы данных см. в статье [Пользователи автономной базы данных — создание переносимой базы данных](https://msdn.microsoft.com/library/ff929188.aspx). Пользователей баз данных (за исключением администраторов) невозможно создавать с помощью портала, а действие ролей RBAC не распространяется на SQL Server.

### Подключение к пользовательской базе данных с помощью SQL Server Management Studio

Чтобы убедиться, что администратор Azure AD настроен правильно, подключитесь к базе данных **master** с помощью учетной записи администратора Azure AD. Вы можете подготовить пользователя автономной базы данных, использующей Azure AD (кроме администратора сервера, который является владельцем базы данных). Для этого подключитесь к базе данных с помощью удостоверения Azure AD, у которого есть доступ к базе данных.

> [AZURE.IMPORTANT] Поддержка аутентификации Azure Active Directory доступна в [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

#### Подключение с помощью встроенной проверки подлинности Active Directory

Этот метод следует использовать, если вы входите в систему Windows с помощью учетных данных Azure Active Directory из федеративного домена.

1. Запустите Management Studio, и в диалоговом окне **Подключение к ядру СУБД** (или **Подключение к серверу**) в поле **Проверка подлинности** выберите пункт **Встроенная проверка подлинности Active Directory**. Пароль не требуется (или его нельзя ввести), поскольку для подключения используются существующие учетные данные.
2. Нажмите кнопку **Параметры** и на странице **Свойства соединения** в поле **Подключение к базе данных** введите имя пользовательской базы данных для подключения.

#### Подключение с помощью проверки пароля Active Directory

Используйте этот метод при подключении с помощью имени участника Azure AD, в котором используется управляемый домен Azure AD. Его также можно использовать для федеративной учетной записи без доступа к домену, например при удаленной работе.

Используйте этот метод, если вы вошли в ОС Windows, используя учетные данные домена, для которого не настроена федерация с Azure. Или если используете проверку подлинности Azure AD с помощью Azure AD на основе исходного домена или домена клиента.

1. Запустите Management Studio, и в диалоговом окне **Подключение к ядру СУБД** (или **Подключение к серверу**) в поле **Проверка подлинности** выберите пункт **Проверка пароля Active Directory**.
2. В поле **Имя пользователя** введите свое имя пользователя Azure Active Directory в формате **username@domain.com**. Это должна быть учетная запись из Azure Active Directory или учетная запись из федерации домена с Azure Active Directory.
3. В поле **Пароль** введите пароль пользователя для учетной записи Azure Active Directory или учетной записи федеративного домена.
4. Нажмите кнопку **Параметры**, и на странице **Свойства подключения** в поле **Подключение к базе данных** введите имя пользовательской базы данных для подключения.


### Создание пользователя автономной базы данных Azure AD в пользовательской базе данных

Вы можете создать пользователя автономной базы данных на основе Azure AD (кроме администратора сервера, который является владельцем базы данных). Для этого подключитесь к базе данных с помощью удостоверения Azure AD (как описано в предыдущей процедуре) от имени пользователя с уровнем разрешений не ниже, чем **ИЗМЕНЕНИЕ ЛЮБОГО ПОЛЬЗОВАТЕЛЯ**. Затем используйте следующий синтаксис Transact-SQL:

	CREATE USER Azure_AD_principal_name
	FROM EXTERNAL PROVIDER;


Значением параметра *Azure\_AD\_principal\_name* может быть имя участника-пользователя Azure AD или отображаемое имя группы Azure AD.

**Примеры.** Создание пользователя автономной базы данных, представляющего собой пользователя федеративного или управляемого домена Azure AD:

	CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
	CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;

Создание пользователя автономной базы данных, представляющего собой группу Azure AD или группу федеративного домена:

	CREATE USER [Nurses] FROM EXTERNAL PROVIDER;


Подробнее о создании пользователей автономной базы данных на основе удостоверений Azure Active Directory см. в статье [СОЗДАНИЕ ПОЛЬЗОВАТЕЛЯ (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).

При создании пользователя базы данных пользователь получает разрешение **ПОДКЛЮЧЕНИЕ** и может подключаться к этой базе данных как участник роли **PUBLIC**. Изначально пользователю доступны только разрешения, предоставленные для роли **PUBLIC**, или любые разрешения, предоставленные любой группе Windows, в которую входит пользователь. Подготовив пользователя автономной базы данных, использующей Azure AD, можно предоставить ему дополнительные разрешения — так же, как разрешения для любого другого типа пользователя. Обычно разрешения предоставляются ролям базы данных, а затем эти роли назначаются пользователям. Подробнее об этом см. в статье [Основные сведения о разрешениях ядра СУБД](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Дополнительные сведения о специальных ролях базы данных SQL см. в статье [Управление базами данных и учетными записями в базе данных SQL Azure](sql-database-manage-logins.md). Пользователь федеративного домена, импортируемый в управляемый домен, должен использовать идентификатор управляемого домена.

> [AZURE.NOTE] Пользователи Azure AD помечаются в метаданных базы данных как тип E (EXTERNAL\_USER), а группы — как тип X (EXTERNAL\_GROUPS). Дополнительные сведения см. в статье [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx).


## 7\. Подключение к базе данных с помощью удостоверений Azure Active Directory

Проверка подлинности Azure Active Directory поддерживает следующие способы подключения к базе данных с помощью удостоверений Azure AD:

- с использованием встроенной проверки подлинности Windows;
- с использованием имени участника-пользователя и пароля Azure AD.

### 7\.1. Подключение с использованием встроенной проверки подлинности (Windows)

Вы можете использовать встроенную проверку подлинности Windows. Для этого выполните федерацию каталога Active Directory своего домена с Azure Active Directory и запустите клиентское приложение (или службу), подключающееся к базе данных, на компьютере, присоединенном к домену, с помощью учетных данных домена пользователя.

Чтобы подключиться к базе данных с помощью встроенной проверки подлинности и удостоверения Azure AD, ключевое слово проверки подлинности в строке подключения к базе данных должно иметь значение «Active Directory Integrated». В следующем примере кода C# используется ADO .NET.

	string ConnectionString =
	@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated;";
	SqlConnection conn = new SqlConnection(ConnectionString);
	conn.Open();

Обратите внимание, что ключевое слово ``Integrated Security=True`` строки подключения не используется для подключения к базе данных SQL Azure.

### 7\.2. Подключение с использованием имени участника-пользователя и пароля Azure AD
Вы можете подключиться к базе данных с использованием встроенной проверки подлинности и удостоверения Azure AD. Для этого ключевое слово проверки должно иметь значение «Active Directory Password», а строка подключения должна содержать ключевые слова и значения «User ID/UID» и «Password/PWD». В следующем примере кода C# используется ADO .NET.

	string ConnectionString =
	  @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
	SqlConnection conn = new SqlConnection(ConnectionString);
	conn.Open();

Соответствующие примеры кода, связанные с аутентификацией Azure AD, см. в [блоге о безопасности SQL Server](http://blogs.msdn.com/b/sqlsecurity/) на сайте MSDN.

## См. также

[Управление базами данных и учетными записями в базе данных SQL Azure](sql-database-manage-logins.md)

[Пользователи автономной базы данных](https://msdn.microsoft.com/library/ff929071.aspx)

[СОЗДАНИЕ ПОЛЬЗОВАТЕЛЯ (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)


<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png

<!----HONumber=AcomDC_0204_2016--->