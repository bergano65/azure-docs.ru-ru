---
title: Перенос локальных пользователей и групп Windows, настроенных для SQL Server, в управляемый экземпляр Базы данных SQL Azure с использованием синтаксиса T-SQL DDL | Документация Майкрософт
description: Сведения о том, как перенести локальных пользователей и группы Windows, настроенных для SQL Server, в управляемый экземпляр
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/22/2019
ms.openlocfilehash: ca0997010fef40c0927960c04588c031dd85fff8
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795065"
---
# <a name="tutorial-migrate-sql-server-on-premises-windows-users-and-groups-to-azure-sql-database-managed-instance-using-t-sql-ddl-syntax"></a>Руководство по Перенос локальных пользователей и групп Windows, настроенных для SQL Server, в управляемый экземпляр Базы данных SQL Azure с использованием синтаксиса T-SQL DDL

> [!NOTE]
> Поддерживается **общедоступная предварительная версия** синтаксиса, который используется в рамках этой статьи для переноса локальных пользователей и групп в управляемый экземпляр.

В этой статье описан перенос локальных пользователей и групп Windows, настроенных для SQL Server, в существующий управляемый экземпляр Базы данных SQL Azure с использованием синтаксиса T-SQL.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> - создание имен для входа в SQL Server;
> - создание тестовой базы данных для миграции;
> - создание имен для входа, пользователей и ролей;
> - резервное копирование и восстановление базы данных в управляемом экземпляре;
> - перенос пользователей в управляемый экземпляр вручную с использованием синтаксиса ALTER USER;
> - тестирование проверки подлинности для новых сопоставленных пользователей.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

- федеративная связь домена Windows с Azure Active Directory (AAD);
- доступ к Active Directory для создания пользователей и групп;
- SQL Server в локальной среде;
- управляемый экземпляр. См. [Краткое руководство. Создание управляемого экземпляра Базы данных SQL Azure](sql-database-managed-instance-get-started.md).
- Вы можете подключиться к управляемому экземпляру из локальной сети. Дополнительные сведения см. в следующих статьях: 
    - [Connect your application to Azure SQL Database managed instance](sql-database-managed-instance-connect-app.md) (Подключение приложения к управляемому экземпляру Базы данных SQL Azure)
    - [Краткое руководство Настройка подключения "точка — сеть" к Управляемому экземпляру Базы данных SQL Azure с локального компьютера](sql-database-managed-instance-configure-p2s.md)
    - [Настройка общедоступной конечной точки в Управляемом экземпляре Базы данных SQL Azure](sql-database-managed-instance-public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>Синтаксис T-SQL DDL

Ниже описан синтаксис T-SQL DDL, который используется для переноса локальных пользователей и групп Windows, настроенных для SQL Server, в управляемый экземпляр с проверкой подлинности Azure AD.

```sql
-- For individual Windows users with logins 
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com]; 

--For individual groups with logins 
ALTER USER [domainName\groupName] WITH LOGIN=[groupName] 
```

## <a name="arguments"></a>Аргументы

_domainName_</br>
Указывает имя домена для пользователя.

_userName_</br>
Указывает имя пользователя, найденного в базе данных.

_= loginName\@domainName.com_</br>
Сопоставляет пользователя с именем для входа в AAD

_groupName_</br>
Указывает имя группы, найденной в базе данных.

## <a name="part-1-create-logins-for-sql-server-on-premises-users-and-groups"></a>Часть 1. Создание имен для локальных пользователей и групп SQL Server

> [!IMPORTANT]
> Описанный ниже синтаксис позволяет создать в SQL Server имена пользователей и групп. Перед выполнением описанного ниже синтаксиса необходимо убедиться, что пользователь и группа есть в Active Directory (AD). </br> </br>
> Пользователи: testUser1, testGroupUser </br>
> Группа: migration, в которую должен входить пользователь testGroupUser в AD

В приведенном ниже примере в SQL Server создается имя входа для учетной записи _testUser1_ в домене _aadsqlmi_. 

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases
 
use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;   
go; 

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser]. 
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;    
go; 


-- Check logins were created
select * from sys.server_principals; 
go; 
```

Создайте базу данных для этого теста.

```sql
-- Create a database called [migration]
create database migration 
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>Часть 2. Создание пользователей и групп Windows, добавление ролей и разрешений

Используйте описанный ниже синтаксис, чтобы создать тестового пользователя.

```sql
use migration;  
go 
 
-- Create Windows user [aadsqlmi\testUser1] with login 
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1]; 
go 
```

Проверьте разрешения пользователя:

```sql
-- Check the user in the Metadata 
select * from sys.database_principals; 
go 
 
-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions; 
go
```

Создайте роль и назначьте тестового пользователя для этой роли:

```sql 
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole; 
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole; 
go 

alter role UserMigrationRole add member [aadsqlmi\testUser1]; 
go 
``` 

По следующему запросу отображаются имена пользователей, которым назначена указанная роль:

```sql
-- Display user name assigned to a specific role 
SELECT DP1.name AS DatabaseRoleName,    
   isnull (DP2.name, 'No members') AS DatabaseUserName    
 FROM sys.database_role_members AS DRM   
 RIGHT OUTER JOIN sys.database_principals AS DP1   
   ON DRM.role_principal_id = DP1.principal_id   
 LEFT OUTER JOIN sys.database_principals AS DP2   
   ON DRM.member_principal_id = DP2.principal_id   
WHERE DP1.type = 'R' 
ORDER BY DP1.name; 
```

Используйте описанный ниже синтаксис, чтобы создать группу. Затем добавьте группу в роль `db_owner`.

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration]; 
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration'; 
go 

--Check the db_owner role for 'aadsqlmi\migration' group 
select is_rolemember('db_owner', 'aadsqlmi\migration')   
go 
-- Output  ( 1 means YES) 
```

Создайте тестовую таблицу и добавьте в нее данные с помощью следующего синтаксиса:

```sql
-- Create a table and add data 
create table test ( a int, b int); 
go 

insert into test values (1,10) 
go 

-- Check the table values
select * from test; 
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-managed-instance"></a>Часть 3. Резервное копирование и восстановление базы данных пользователей в управляемом экземпляре

Создайте резервную копию базы данных, для которой выполняется миграция, используя инструкции из [этой статьи](/sql/relational-databases/databases/copy-databases-with-backup-and-restore) или следующий синтаксис:

```sql
use master; 
go 
backup database migration to disk = 'C:\Migration\migration.bak'; 
go
```

Выполните инструкции из статьи [Краткое руководство. Восстановление базы данных в Управляемый экземпляр](sql-database-managed-instance-get-started-restore.md).

## <a name="part-4-migrate-users-to-managed-instance"></a>Часть 4. Перенос пользователей в управляемый экземпляр

Выполните команду ALTER USER, чтобы завершить миграцию в управляемом экземпляре.

1. Подключитесь к управляемому экземпляру с учетной записью администратора SQL. Затем создайте в управляемом экземпляре имя для входа в AAD, используя следующий синтаксис:

    ```sql
    use master 
    go 
    
    -- Create login for AAD user [testUser1@aadsqlmi.net] 
    create login [testUser1@aadsqlmi.net] from external provider 
    go
    
    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net] 
    create login [migration] from external provider 
    go 
     
    --Check the two new logins 
    select * from sys.server_principals 
    go
    ```

1. Убедитесь, что база данных, таблица и субъекты успешно перенесены.

    ```sql
    -- Switch to the database migration that is already restored for MI 
    use migration;  
    go 
     
    --Check if the restored table test exist and contain a row 
    select * from test; 
    go 
     
    -- Check that the SQL on-premise Windows user/group exists  
    select * from sys.database_principals; 
    go 
    -- the old user aadsqlmi\testUser1 should be there 
    -- the old group aadsqlmi\migration should be there
    ```

1. Используйте синтаксис ALTER USER, чтобы связать локального пользователя с именем для входа в Azure AD.

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net]; 
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
    -- New user testUser1@aadsqlmi.net should be there instead 
     
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go

    -- Check a specific role   
    -- Display Db user name assigned to a specific role 
    SELECT DP1.name AS DatabaseRoleName,    
    isnull (DP2.name, 'No members') AS DatabaseUserName    
    FROM sys.database_role_members AS DRM   
    RIGHT OUTER JOIN sys.database_principals AS DP1   
    ON DRM.role_principal_id = DP1.principal_id   
    LEFT OUTER JOIN sys.database_principals AS DP2   
    ON DRM.member_principal_id = DP2.principal_id   
    WHERE DP1.type = 'R' 
    ORDER BY DP1.name;
    ```

1. Используйте синтаксис ALTER USER, чтобы связать локальную группу с именем для входа в Azure AD.

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration]; 
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
     
    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go 
     
    --Check the db_owner role for 'aadsqlmi\migration' user 
    select is_rolemember('db_owner', 'migration')   
    go 
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>Часть 5. Тестирование проверки подлинности для пользователя или группы Azure AD

Проверьте аутентификацию в управляемом экземпляре для пользователя, которого вы сопоставили с именем для входа AAD с помощью синтаксиса ALTER USER.
 
1. Войдите на федеративную виртуальную машину, используя подписку Управляемого экземпляра в качестве `aadsqlmi\testUser1`.
1. Из SQL Server Management Studio (SSMS) войдите в управляемый экземпляр, используя **встроенную в Active Directory** проверку подлинности и подключитесь к базе данных `migration`.
    1. Кроме того, вы можете войти с помощью учетных данных testUser1@aadsqlmi.net, используя параметр SSMS **Active Directory — универсальная с поддержкой MFA**. Но в этом случае вы не сможете использовать механизм единого входа и вам придется ввести пароль. Для входа в управляемый экземпляр вам не потребуется использовать федеративную виртуальную машину.
1. Являясь членом роли **SELECT**, вы можете получать данные из таблицы `test`.

    ```sql
    Select * from test  --  and see one row (1,10)
    ```


Проверьте аутентификацию в управляемом экземпляре для члена группы Windows `migration`. Перед началом миграции пользователя `aadsqlmi\testGroupUser` нужно добавить в группу `migration`.

1. Войдите на федеративную виртуальную машину, используя подписку Управляемого экземпляра в качестве `aadsqlmi\testGroupUser`. 
1. Используя SSMS со **встроенной в Active Directory** проверкой подлинности, подключитесь к серверу Управляемого экземпляра и к базе данных `migration`.
    1. Кроме того, вы можете войти с помощью учетных данных testGroupUser@aadsqlmi.net, используя параметр SSMS **Active Directory — универсальная с поддержкой MFA**. Но в этом случае вы не сможете использовать механизм единого входа и вам придется ввести пароль. Для входа в управляемый экземпляр вам не потребуется использовать федеративную виртуальную машину. 
1. Являясь членом роли `db_owner`, вы можете создать новую таблицу.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```
                             
> [!NOTE] 
> В базе данных SQL Azure есть известная проблема с проектированием, из-за которой инструкция CREATE TABLE завершается ошибкой, если ее выполняет член группы: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4 
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> Сейчас можно использовать обходное решение, создавая таблицу с существующей схемой, например <dbo.new> для примера выше.

## <a name="next-steps"></a>Дополнительная информация

- [Руководство. Перенос баз данных SQL Server в управляемый экземпляр Базы данных SQL Azure с помощью DMS в автономном режиме](../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)