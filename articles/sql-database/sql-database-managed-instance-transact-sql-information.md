---
title: Отличия SQL управляемого экземпляра T-SQL базы данных Azure | Документация Майкрософт
description: В этой статье обсуждаются различия T-SQL между управляемым экземпляром в Базе данных SQL Azure и SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
manager: craigg
ms.date: 03/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 17609212fcc7620dc0d6d617e7626d12c8bb0592
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65852147"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Различия T-SQL между Управляемым экземпляром Базы данных SQL Azure и SQL Server

В этой статье перечислены и описаны различия в синтаксисе и поведении между базы данных управляемого экземпляра SQL Azure и локальным SQL Server Database Engine. Рассматриваются следующие темы: <a name="Differences"></a>

- [Доступность](#availability) включает различия в [Always-On](#always-on-availability) и [резервные копии](#backup).
- [Безопасность](#security) включает различия в [аудита](#auditing), [сертификаты](#certificates), [учетные данные](#credential), [поставщиков служб шифрования](#cryptographic-providers), [имена входа и пользователи](#logins-and-users)и [ключ службы и главный ключ службы](#service-key-and-service-master-key).
- [Конфигурация](#configuration) включает различия в [расширение буферного пула](#buffer-pool-extension), [параметры сортировки](#collation), [уровни совместимости](#compatibility-levels), [зеркального отображения базы данных ](#database-mirroring), [параметры базы данных](#database-options), [агента SQL Server](#sql-server-agent), и [параметры таблицы](#tables).
- [Функциональные возможности](#functionalities) включает в себя [BULK INSERT или OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [распределенные транзакции](#distributed-transactions), [расширенные события](#extended-events), [внешние библиотеки](#external-libraries), [filestream и FileTable](#filestream-and-filetable), [полнотекстовый семантический поиск](#full-text-semantic-search), [связанных серверов](#linked-servers), [PolyBase](#polybase), [репликации](#replication), [ВОССТАНОВИТЬ](#restore-statement), [компонента Service Broker](#service-broker), [хранимых процедур, функций и триггеров](#stored-procedures-functions-and-triggers).
- [Функции, которые имеют разное поведение в управляемых экземплярах](#Changes).
- [Временные ограничения и известные проблемы](#Issues).

Вариант развертывания в виде управляемого экземпляра обеспечивает высокий уровень совместимости с локальным ядром СУБД SQL Server. В управляемом экземпляре поддерживается большинство функций ядра СУБД SQL Server.

![Перенос](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>Доступность

### <a name="always-on-availability"></a>Always On

[Высокий уровень доступности](sql-database-high-availability.md) встроен в управляемый экземпляр и не контролируется пользователей. Не поддерживаются следующие инструкции:

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql);
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql);
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql);
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql);
- [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) предложении [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) инструкции

### <a name="backup"></a>Azure Backup

Управляемые экземпляры имеют автоматического резервного копирования, поэтому пользователи могут создавать всей базы данных `COPY_ONLY` резервных копий. Разностные резервные копии, журнала и резервных копий моментальных снимков файлов не поддерживаются.

- С помощью управляемого экземпляра можно выполнять архивацию базы данных экземпляра только в учетную запись хранилища BLOB-объектов Azure:
  - Поддерживается только `BACKUP TO URL`.
  - `FILE`, `TAPE`, и устройства резервного копирования не поддерживаются.
- Большинство общих `WITH` поддерживаются параметры.
  - `COPY_ONLY` является обязательным.
  - `FILE_SNAPSHOT` не поддерживается.
  - Параметры ленты: `REWIND`, `NOREWIND`, `UNLOAD`, и `NOUNLOAD` не поддерживаются.
  - Параметры журналов: `NORECOVERY`, `STANDBY`, и `NO_TRUNCATE` не поддерживаются.

Ограничения: 

- Управляемый экземпляр, позволяет создавать резервные копии базы данных экземпляра из резервной копии с до 32 чередующимися наборами, чего достаточно для баз данных размером до 4 ТБ, если используется сжатие резервных копий.
- Размер максимального чередующегося резервного копирования с помощью `BACKUP` команд в управляемом экземпляре составляет 195 ГБ, размер максимального BLOB-объектов. Увеличьте количество полосковых линий в команде резервного копирования, чтобы уменьшить размер отдельных полосковых линий и не превышать это ограничение.

    > [!TIP]
    > Чтобы обойти это ограничение, при создании резервной копии базы данных из SQL Server в локальной среде или на виртуальной машине, вы можете:
    >
    > - Создать резервную копию на `DISK` вместо резервному копированию `URL`.
    > - Передача файлов резервных копий в хранилище BLOB-объектов.
    > - Восстановите в управляемом экземпляре.
    >
    > `Restore` Команда в управляемом экземпляре поддерживает большие размеры больших двоичных объектов в файлах резервных копий, так как тип другой BLOB-объекта используется для хранения загружаемых файлов резервной копии.

Сведения о резервном копировании с помощью T-SQL см. в статье [BACKUP (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Безопасность

### <a name="auditing"></a>Аудит

Ниже перечислены основные различия между аудитом в базах данных Базы данных SQL Azure и SQL Server.

- С помощью параметра развертывания управляемого экземпляра базы данных SQL Azure аудит работает на уровне сервера. `.xel` Файлы журналов хранятся в хранилище BLOB-объектов Azure.
- При использовании варианта развертывания в виде отдельной базы данных и эластичного пула в Базе данных SQL Azure аудит работает на уровне базы данных.
- В SQL Server в локальной или виртуальных машин аудит работает на уровне сервера. События хранятся в файловой системе или журналы событий Windows.
 
Аудит XEvent в управляемом экземпляре поддерживает целевые объекты хранилища BLOB-объектов Azure. Журналы файлов и Windows не поддерживаются.

Основные различия в синтаксисе `CREATE AUDIT` для аудита в хранилище BLOB-объектов Azure:

- Новый синтаксис `TO URL` можно использовать для указания URL-адрес контейнера хранилища BLOB-объектов Azure, где `.xel` файлы помещаются.
- Синтаксис `TO FILE` не поддерживается, так как управляемый экземпляр не может обращаться к файловым ресурсам Windows.

Дополнительные сведения можно найти в разделе  

- [CREATE SERVER AUDIT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Аудит](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Сертификаты

Управляемого экземпляра нет доступа к общим папкам и папкам Windows, поэтому действуют следующие ограничения:

- `CREATE FROM` / `BACKUP TO` Файл не поддерживается для сертификатов.
- `CREATE` / `BACKUP` Сертификат от `FILE` / `ASSEMBLY` не поддерживается. Невозможно использовать файлы закрытых ключей. 

См. статьи [Инструкция CREATE CERTIFICATE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) и [BACKUP CERTIFICATE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Возможное решение**: Скрипт для сертификата или закрытого ключа, хранятся в виде SQL-файл и создайте из двоичного файла:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Учетные данные

Поддерживаются удостоверения только Azure Key Vault и `SHARED ACCESS SIGNATURE`. Пользователи Windows не поддерживаются.

См. статьи [CREATE CREDENTIAL (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) и [ALTER CREDENTIAL (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Поставщики служб шифрования

Управляемого экземпляра нет доступа к файлам, поэтому невозможно создать поставщики служб шифрования:

- `CREATE CRYPTOGRAPHIC PROVIDER` не поддерживается. См. статью [CREATE CRYPTOGRAPHIC PROVIDER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` не поддерживается. См. статью [ALTER CRYPTOGRAPHIC PROVIDER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Имена входа и пользователи

- Имена входа SQL, созданные с помощью `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, и `FROM SID` поддерживаются. См. статью [CREATE LOGIN (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Azure участникам сервера Active Directory (Azure AD) (имена входа), созданные с помощью [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) синтаксис или [CREATE USER из LOGIN [входа Azure AD]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) синтаксис поддерживаются (Предварительная версия). Эти имена входа создаются на уровне сервера.

    Управляемый экземпляр поддерживает субъекты базы данных Azure AD, созданные с помощью синтаксиса `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Эта функция также называется Azure AD содержащихся пользователей базы данных.

- Имена входа Windows, созданные с помощью `CREATE LOGIN ... FROM WINDOWS` синтаксис не поддерживаются. Используйте пользователей и имена для входа Azure Active Directory.
- У пользователя Azure AD, который создал экземпляр [неограниченными правами доступа администратора](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Пользователи без прав администратора Azure AD на уровне базы данных могут быть созданы с помощью `CREATE USER ... FROM EXTERNAL PROVIDER` синтаксис. См. раздел [Пользователи без FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users).
- Azure AD участников на уровне сервера (имена входа) поддержки функций SQL в рамках только один управляемый экземпляр. Функции, требующие взаимодействия между несколькими экземплярами, независимо от того, ли они выполняется в том же Azure AD клиента или разных клиентов, не поддерживаются для пользователей Azure AD. Ниже приведены примеры таких функций:

  - Репликация транзакций SQL.
  - Сервер связей.

- Установка имени для входа Azure AD, сопоставленного с группой Azure AD, в качестве владельца базы данных, не поддерживается.
- Олицетворение участников уровня сервера Azure AD с помощью других субъектов Azure AD поддерживается, такие как [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) предложение. Ниже приведены ограничения EXECUTE AS.

  - EXECUTE AS USER не поддерживается для пользователей Azure AD, когда имя отличается от имени входа. Например, если пользователь создается при использовании синтаксиса CREATE USER [myAadUser] из входа [john@contoso.com] и производится попытка олицетворения через EXEC AS USER = _myAadUser_. При создании **пользователя** из субъекта сервера Azure AD (имя входа) укажите user_name как же login_name из **входа**.
  - Только SQL субъекты уровня сервера (имена входа), которые являются частью `sysadmin` роли могут выполнять следующие операции, предназначенных для субъектов Azure AD:

    - EXECUTE AS USER;
    - EXECUTE AS LOGIN.

- Ограничения общедоступной предварительной версии для участников на уровне для сервера Azure AD (имена входа):

  - Ограничения администратора Active Directory для управляемого экземпляра.

    - Администратор Azure AD, используемые в управляемый экземпляр не может использоваться для создания Azure AD сервера участника (имя входа) в управляемом экземпляре. Первый Azure необходимо создать субъект-сервер AD (вход) с помощью учетной записи SQL Server на `sysadmin` роли. Это временное ограничение будет удален после участникам сервера Azure AD (имена входа) становится общедоступной. Если вы попытаетесь использовать учетную запись администратора Azure AD, чтобы создать имя входа, вы увидите следующую ошибку: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - В настоящее время первого входа Azure AD, созданных в базе данных master должна быть создана с стандартной учетной записи SQL Server (не Azure AD) на `sysadmin` роли с помощью [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) из ВНЕШНЕГО ПОСТАВЩИКА. После выпуска общедоступной версии это ограничение будет снято. Затем вы можете создать исходное имя для входа Azure AD с помощью администратора Active Directory для управляемого экземпляра.
    - Для использования с SQL Server Management Studio или SqlPackage DacFx (экспорта и импорта) не поддерживается для имен входа Azure AD. Это ограничение будет удален после участникам сервера Azure AD (имена входа) становится общедоступной.
    - Использование субъектов сервера Azure AD (имена входа) с SQL Server Management Studio:

      - Создание скриптов для имен входа Azure AD, использующих любого прошедшего проверку подлинности имени входа не поддерживается.
      - IntelliSense не распознает инструкции CREATE LOGIN из ВНЕШНИХ PROVIDER и красной линией.

- Только на уровне сервера имя входа субъекта, который создается на управляемом экземпляре, подготовка, члены ролей сервера, таких как `securityadmin` или `sysadmin`, или других имен входа с разрешением ALTER ANY LOGIN на уровне сервера можно создать в Azure AD участникам сервера (имена входа) в базе данных master для управляемого экземпляра.
- Если имя входа является субъектом SQL, только имена входа, которые являются частью `sysadmin` роли можно использовать команду create, чтобы создать имена входа для учетной записи Azure AD.
- Войдите в Azure AD должна входить в Azure AD в том же каталоге, который используется для базы данных управляемого экземпляра SQL Azure.
- Azure AD участников на уровне сервера (имена входа), отображаются в обозревателе объектов, начиная с SQL Server Management Studio 18.0 preview 5.
- Допускается перекрытие субъектов сервера (имен для входа) Azure AD с учетной записью администратора Azure AD. Azure AD участников на уровне сервера (имена входа) имеют приоритет над администратора Azure AD при разрешения участника и разрешения в управляемый экземпляр.
- Во время проверки подлинности для разрешения проверки подлинности участника применяется следующая последовательность:

    1. Если у учетной записи Azure AD существует как непосредственно сопоставляются с Azure AD участника на уровне сервера (имя входа), которая присутствует в таблице sys.server_principals как тип «E», предоставления доступа и применить разрешения для участника на уровне сервера Azure AD (имя входа).
    2. Если у учетной записи Azure AD является членом группы Azure AD, сопоставленный с Azure AD участника на уровне сервера (имя входа), которая присутствует в таблице sys.server_principals при вводе «X», предоставления доступа и применить разрешения для имени входа группы Azure AD.
    3. Если учетная запись Azure AD особую настроен портал администратора Azure AD для управляемого экземпляра, который не существует в системных представлениях управляемый экземпляр, применяются специальные разрешения предопределенной администратора Azure AD для управляемого экземпляра (устаревший режим).
    4. Если у учетной записи Azure AD существует как непосредственно сопоставляется с пользователем Azure AD в базе данных, которая присутствует в sys.database_principals как тип «E», предоставления доступа и применить разрешения для пользователя базы данных Azure AD.
    5. Если у учетной записи Azure AD является членом группы Azure AD, сопоставленный с пользователем Azure AD в базе данных, которая присутствует в sys.database_principals как тип «X», предоставления доступа и применить разрешения для имени входа группы Azure AD.
    6. Если имеется сопоставляется учетной записи пользователя Azure AD или учетной записи группы Azure AD, который разрешает пользователю, который проходит проверку подлинности, войдите в Azure AD применяются все разрешения из этого имени входа Azure AD.

### <a name="service-key-and-service-master-key"></a>Ключ службы и главный ключ службы

- [Резервное копирование главного ключа](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) не поддерживается (управляется службой базы данных SQL).
- [Восстановление главного ключа](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) не поддерживается (управляется службой базы данных SQL).
- [Резервное копирование главного ключа службы](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) не поддерживается (управляется службой базы данных SQL).
- [Восстановление главного ключа службы](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) не поддерживается (управляется службой базы данных SQL).

## <a name="configuration"></a>Параметр Configuration

### <a name="buffer-pool-extension"></a>Расширение буферного пула

- [Расширение буферного пула](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) не поддерживается.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` не поддерживается. См. статью [ALTER SERVER CONFIGURATION (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Параметры сортировки

Параметр сортировки экземпляра по умолчанию — `SQL_Latin1_General_CP1_CI_AS`. Этот параметр можно указать как параметр создания. См. статью [Параметры сортировки](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Уровни совместимости

- Поддерживаемые уровни совместимости, 100, 110, 120, 130 и 140.
- Уровни совместимости ниже 100 не поддерживаются.
- Уровень совместимости по умолчанию для новых баз данных — 140. Для восстановленных баз данных уровень совместимости остается неизменным, если он был 100 и более поздних версий.

См. статью [Уровень совместимости инструкции ALTER DATABASE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)

### <a name="database-mirroring"></a>Зеркальное отображение базы данных

Зеркальное отображение базы данных не поддерживается.

- Параметры `ALTER DATABASE SET PARTNER` и `SET WITNESS` не поддерживаются.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` не поддерживается.

Дополнительные сведения см. в статьях [Зеркальное отображение базы данных ALTER DATABASE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) и [CREATE ENDPOINT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Параметры базы данных

- Несколько файлов журнала не поддерживаются.
- Объекты в памяти не поддерживаются на уровне служб "Общего назначения". 
- Имеется ограничение в 280 файлов на один экземпляр общего назначения, который подразумевает более 280 файлов на каждую базу данных. Файлы данных и журналов на уровне общего назначения, учитываются в этом пределе. [Уровня "критически важный для бизнеса" поддерживает 32 767 файлов на каждую базу данных](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- Базы данных не может содержать файловые группы, содержащие данные filestream. Восстановление завершается сбоем, если BAK-файл содержит `FILESTREAM` данных. 
- Каждый файл помещается в хранилище BLOB-объектов Azure. Операции ввода-вывода и пропускная способность каждого файла зависят от размера каждого файла.

#### <a name="create-database-statement"></a>Инструкция CREATE DATABASE

Следующие ограничения применяются к `CREATE DATABASE`:

- Невозможно определить файлы и файловые группы. 
- `CONTAINMENT` Возможность не поддерживается. 
- `WITH` параметры не поддерживаются. 
   > [!TIP]
   > Чтобы обойти это ограничение, используйте `ALTER DATABASE` после `CREATE DATABASE` задавать параметры базы данных для добавления файлов или задать вложение. 

- `FOR ATTACH` Возможность не поддерживается.
- `AS SNAPSHOT OF` Возможность не поддерживается.

Дополнительные сведения см. в статье [CREATE DATABASE (SQL Server Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Инструкция ALTER DATABASE

Невозможно задать или изменить некоторые свойства файла:

- Путь к файлу не может указываться в `ALTER DATABASE ADD FILE (FILENAME='path')` инструкцию T-SQL. Удалите `FILENAME` из сценария, так как управляемый экземпляр автоматически помещает файлы. 
- Имя файла не может изменяться с помощью `ALTER DATABASE` инструкции.

Следующие параметры задаются по умолчанию и не могут быть изменены.

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

Невозможно изменить следующие параметры:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Дополнительные сведения см. в статье [Параметры инструкции ALTER DATABASE для файлов и файловых групп (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>Агент SQL Server

- Параметры агента SQL Server доступны только для чтения. Процедура `sp_set_agent_properties` не поддерживается в управляемом экземпляре. 
- Задания (job)
  - Шаги задания T-SQL поддерживаются.
  - Поддерживаются следующие задания репликации:
    - Читатель журнала транзакций.
    - Снимок
    - Распространитель
  - Шаги задания служб SSIS поддерживаются.
  - Сейчас не поддерживаются другие типы шагов заданий:
    - Шаг задания репликации слиянием не поддерживается. 
    - Читатель очереди пока не поддерживается. 
    - Командная оболочка пока не поддерживается.
  - Управляемых экземпляров не может получить доступ к внешним ресурсам, например, общие сетевые ресурсы с помощью robocopy. 
  - SQL Server Analysis Services не поддерживаются.
- Уведомления поддерживаются частично.
- Поддерживается уведомление по электронной почте, несмотря на то, что его необходимо настроить профиль компонента Database Mail. Агент SQL Server можно использовать только один профиль компонента Database Mail, и ему должно быть присвоено `AzureManagedInstance_dbmail_profile`. 
  - Пейджер не поддерживается. 
  - NetSend не поддерживается.
  - Оповещения еще не поддерживаются.
  - Прокси-серверы не поддерживаются. 
- EventLog не поддерживается.

Следующие функции сейчас не поддерживаются, но будут добавлены в будущем:

- Прокси-серверы
- Планирование заданий простоя ЦП
- Включение или отключение агента
- Оповещения

Сведения об агенте SQL Server см. в статье [Агент SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Таблицы

Следующие таблицы не поддерживаются:

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED` 

Сведения о способах создания и изменения таблиц, см. в разделе [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) и [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Функциональные возможности

### <a name="bulk-insert--openrowset"></a>Массовая вставка или OpenRowset

Управляемого экземпляра нет доступа к общим папкам и папкам Windows, поэтому файлы необходимо импортировать из хранилища BLOB-объектов Azure:

- `DATASOURCE` требуется в `BULK INSERT` команды во время импорта файлов из хранилища BLOB-объектов Azure. См. статью [BULK INSERT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` требуется в `OPENROWSET` работать при считывании содержимого файла из хранилища BLOB-объектов Azure. См. статью [OPENROWSET (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>Среда CLR

Управляемого экземпляра нет доступа к общим папкам и папкам Windows, поэтому действуют следующие ограничения:

- Поддерживается только `CREATE ASSEMBLY FROM BINARY`. См. статью [CREATE ASSEMBLY (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` не поддерживается. См. статью [CREATE ASSEMBLY (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` не может ссылаться на файлы. См. статью [ALTER ASSEMBLY (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="dbcc"></a>DBCC

Недокументированные инструкции DBCC, которые включены в SQL Server не поддерживаются в управляемых экземплярах.

- `Trace flags` не поддерживаются. См. в разделе [флаги трассировки](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` не поддерживается. См. статью [DBCC TRACEOFF (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` не поддерживается. См. статью [DBCC TRACEON (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Распределенные транзакции

MSDTC и [эластичные транзакции](sql-database-elastic-transactions-overview.md) в настоящее время не поддерживаются в управляемых экземплярах.

### <a name="extended-events"></a>Расширенные события

Для расширенных событий (XEvents), некоторые из Windows конкретных целевых объектов не поддерживаются:

- `etw_classic_sync` Целевой объект не поддерживается. Store `.xel` файлы в хранилище BLOB-объектов Azure. См. раздел [Целевой объект etw_classic_sync_target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- `event_file` Целевой объект не поддерживается. Store `.xel` файлы в хранилище BLOB-объектов Azure. См. раздел [Целевой объект event_file](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Внешние библиотеки

В базе данных R и Python, не внешние библиотеки, но поддерживается. См. статью [Изучение служб машины SQL Server](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream и FileTable

- данные FileStream не поддерживается.
- База данных не может содержать файловые группы с `FILESTREAM` данных.
- `FILETABLE` не поддерживается.
- Таблицы не могут содержать типы `FILESTREAM`.
- Следующие функции не поддерживаются:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Дополнительные сведения см. в статьях [FILESTREAM (SQL Server)](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) и [Таблицы FileTable (SQL Server)](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Полнотекстовый семантический поиск

[Семантический поиск](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) не поддерживается.

### <a name="linked-servers"></a>Связанные службы

Связанные серверы в управляемых экземплярах поддерживают ограниченное число целевых объектов.

- Поддерживаемые целевые объекты: SQL Server и базы данных SQL.
- Целевые объекты, которые не поддерживаются: файлы, службы Analysis Services и другие реляционные СУБД.

Операции

- Транзакции записи между несколькими экземплярами не поддерживаются.
- `sp_dropserver` поддерживается для удаления связанного сервера. См. статью [sp_dropserver (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- `OPENROWSET` Функция может использоваться для выполнения запросов только на экземплярах SQL Server. Они могут быть либо управляемые, локально, или на виртуальных машинах. См. статью [OPENROWSET (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- `OPENDATASOURCE` Функция может использоваться для выполнения запросов только на экземплярах SQL Server. Они могут быть либо управляемые, локально, или на виртуальных машинах. Только `SQLNCLI`, `SQLNCLI11`, и `SQLOLEDB` значения поддерживаются в качестве поставщика. Например, `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. См. статью [OPENDATASOURCE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="polybase"></a>PolyBase

Внешние таблицы, которые не поддерживаются файлы в HDFS или BLOB-объектов Azure в хранилище ссылаются. Сведения о PolyBase см. в разделе [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Репликация

Репликация доступна в общедоступной предварительной версии управляемых экземпляров. Сведения о репликации см. в разделе [репликации SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance).

### <a name="restore-statement"></a>Инструкция RESTORE 

- Поддерживается следующий синтаксис:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Неподдерживаемый синтаксис:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Источник: 
  - `FROM URL` (Хранилище больших двоичных объектов) — это единственный поддерживаемый параметр.
  - `FROM DISK`/`TAPE` или устройство резервного копирования не поддерживаются.
  - Резервные наборы данных не поддерживаются.
- `WITH` параметры не поддерживаются, например нет `DIFFERENTIAL` или `STATS`.
- `ASYNC RESTORE`: Восстановление продолжится даже в случае разрыва соединения с клиентом. При разрыве соединения, можно проверить `sys.dm_operation_status` представления состояние операции восстановления, а также для СОЗДАНИЯ и удаления базы данных. См. статью [sys.dm_operation_status (база данных SQL Azure)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Следующие параметры базы данных установлены или переопределении и нельзя будет изменить позже: 

- `NEW_BROKER` Если брокер не включен в BAK-файле. 
- `ENABLE_BROKER` Если брокер не включен в BAK-файле. 
- `AUTO_CLOSE=OFF` Если базы данных в BAK-файле `AUTO_CLOSE=ON`. 
- `RECOVERY FULL` Если базы данных в BAK-файле `SIMPLE` или `BULK_LOGGED` режим восстановления.
- Оптимизированная для памяти файловая группа добавляется и названием XTP, это не было в исходном BAK-файле. 
- Любой существующей файловой группы оптимизированных для памяти переименовываются в XTP. 
- `SINGLE_USER` и `RESTRICTED_USER` параметры преобразуются в `MULTI_USER`.

Ограничения: 

- `.BAK` Невозможно восстановить файлы, содержащие несколько резервных наборов данных. 
- `.BAK` Невозможно восстановить файлы, содержащие несколько файлов журналов.
- Восстановление завершается сбоем, если BAK-файл содержит `FILESTREAM` данных.
- Невозможно восстановить резервные копии, которые содержат базы данных активных объектов в памяти на экземпляр общего назначения. Сведения об инструкции restore см. в разделе [инструкций RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Компонент Service broker

Компонент Service Broker между экземплярами не поддерживается:

- `sys.routes`: Перед началом необходимо выбрать адрес из sys.routes. Адрес должен быть ЛОКАЛЬНЫМ для каждого маршрута. См. статью [sys.routes (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Нельзя использовать `CREATE ROUTE` с `ADDRESS` отличное от `LOCAL`. См. статью [CREATE ROUTE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Нельзя использовать `ALTER ROUTE` с `ADDRESS` отличное от `LOCAL`. См. статью [ALTER ROUTE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Хранимые процедуры, функции и триггеры

- `NATIVE_COMPILATION` не поддерживается на уровне общего назначения.
- Следующие параметры [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) не поддерживаются: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` не поддерживается. См. раздел [Примеры](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` не поддерживается. См. раздел [xp_cmdshell (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` не поддерживаются, включая `sp_addextendedproc`  и `sp_dropextendedproc`. См. в разделе [расширенные хранимые процедуры](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` и `sp_detach_db` не поддерживаются. См. статьи [sp_attach_db (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) и [sp_detach_db (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

## <a name="Changes"></a> Изменения в поведении

Следующие переменные, функции и представления возвращают различные результаты:

- `SERVERPROPERTY('EngineEdition')` Возвращает значение 8. Это свойство уникально идентифицирует управляемый экземпляр. См. статью [SERVERPROPERTY (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` Возвращает значение NULL, так как концепция экземпляра, так как он существует для SQL Server не применяется к управляемому экземпляру. См. статью [SERVERPROPERTY (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Возвращает полное DNS-имя «доступный» для подключения, например, my-managed-instance.wcus17662feb9ce98.database.windows.net. См. статью [@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` Возвращает полное DNS-имя «доступный» для подключения, такие как `myinstance.domain.database.windows.net` для свойства «name» и «источник_данных.» См. статью [sys.servers (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` Возвращает значение NULL, так как существует понятие службы, когда оно для SQL Server не применяется к управляемому экземпляру. См. статью [@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` поддерживается. Если имя входа Azure AD не содержится в sys.syslogins, то возвращается значение NULL. См. статью [Идентификатор SUSER_ID (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` не поддерживается. Возвращаются неверные данные, это временный известная проблема. См. статью [SUSER_SID (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Issues"></a> Известные проблемы и ограничения

### <a name="tempdb-size"></a>Размер tempdb

Максимальный размер файла `tempdb` не может превышать 24 ГБ на ядро, на уровне общего назначения. Максимально `tempdb` размер критически важный для бизнеса уровень не с размером экземпляра хранилища. `tempdb` Базы данных всегда состоит из 12 файлов. Этот максимальный размер каждого файла нельзя изменить, и невозможно добавить новые файлы `tempdb`. Некоторые запросы могут возвращать ошибку, если им требуется более чем 24 ГБ на каждое ядро в `tempdb`. `tempdb` всегда повторно создается как пустой базы данных при запуск экземпляра или отработки отказа и любые изменения внесены в `tempdb` не будет сохранен. 

### <a name="cant-restore-contained-database"></a>Не удается восстановить автономной базы данных

Управляемый экземпляр не удается восстановить [автономные базы данных](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Восстановление на определенный момент существующих автономных баз данных не работает в управляемом экземпляре. Скоро эта проблема будет устранена. В то же время рекомендуется удалить параметр включения из баз данных, размещаемые в управляемом экземпляре. Не используйте параметр включения для рабочих баз данных. 

### <a name="exceeding-storage-space-with-small-database-files"></a>Превышение дискового пространства с небольшими файлами баз данных

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`, и `RESTORE DATABASE` инструкций может завершиться ошибкой, так как экземпляр можно достигнуть предела хранилища Azure.

Каждый управляемый экземпляр общего назначения имеет до 35 ТБ хранилища, зарезервированный для Azure уровня "премиум" на диске. Каждый файл базы данных размещается на отдельном физическом диске. Поддерживаются диски размером 128 ГБ, 256 ГБ, 512 ГБ, 1 ТБ или 4 ТБ. Ничего не оплачивают неиспользуемое пространство на диске, но общую сумму размеров диска Azure уровня "премиум" не может превышать 35 ТБ. В некоторых случаях управляемый экземпляр, который не требуется 8 ТБ дискового может превысить ограничение 35 ТБ на из-за внутренней фрагментации размер хранилища.

Например общего назначения управляемого экземпляра может иметь один файл, 1,2 ТБ, размер которого размещены на диске размером 4 ТБ. Он также возможно 248 файлов, каждый размером 1 ГБ, которые размещаются на разных дисках 128 ГБ. В данном примере:

- общий размер выделенного дискового хранилища составляет 1 x 4 ТБ + 248 x 128 ГБ = 35 ТБ;
- общий объем зарезервированного пространства для баз данных в экземпляре составляет 1 x 1,2 ТБ + 248 x 1 ГБ = 1,4 ТБ.

Этот пример показывает, что при определенных обстоятельствах, из-за определенного распределения файлов, управляемый экземпляр может достигает предела 35 ТБ, зарезервирован для подключенный диск Azure уровня "премиум", на которые вы не ожидалось.

В этом примере существующих баз данных по-прежнему работать, а не все проблемы до тех пор, пока не добавлены новые файлы. Невозможно создать новые базы данных или восстановить, так как недостаточно места для новых дисков, даже если общий размер всех баз данных не достигает предельного размера экземпляра. Ошибка, которая возвращается в этом случае не очистить.

Вы можете [определить количество оставшихся файлов](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) с помощью системных представлений. Если этот предел достигнут, попробуйте [пустые и удалите несколько файлов меньшего размера с помощью инструкции DBCC SHRINKFILE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) или переключиться в режим [критически важный для бизнеса уровень, который не имеет этого ограничения](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="incorrect-configuration-of-the-sas-key-during-database-restore"></a>Неправильная конфигурация ключа SAS во время базы данных восстановления

`RESTORE DATABASE` что операции чтения, BAK-файл может постоянно выполнять повторные попытки читать BAK-файл, файл и возвращать ошибку после длительного периода времени, если подпись общего доступа в `CREDENTIAL` неверен. Выполните инструкцию RESTORE HEADERONLY перед восстановлением базы данных, чтобы убедиться в правильности ключа SAS.
Убедитесь, что вы удалите начальные `?` из ключа SAS, который создается с помощью портала Azure.

### <a name="tooling"></a>Инструментарий

SQL Server Management Studio и SQL Server Data Tools могут возникнуть некоторые проблемы при доступе к управляемым экземпляром.

- С помощью субъектов Azure AD сервера (имена входа) и пользователей (Предварительная версия) с SQL Server Data Tools в настоящее время не поддерживается.
- Создание сценариев для участников на уровне для сервера Azure AD (имена входа) и пользователей (Предварительная версия) не поддерживается в SQL Server Management Studio.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Неправильные имена базы данных в некоторых представлениях, журналах и сообщениях

Несколько системных представлений, счетчиков производительности, сообщений об ошибках, XEvents и записей в журнале ошибок отображают идентификаторы базы данных GUID вместо фактических имен базы данных. Не полагайтесь на эти идентификаторы GUID, так как они все заменены баз данных в будущем.

### <a name="database-mail"></a>Компонент Database Mail

`@query` Параметр в [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) процедура не работает.

### <a name="database-mail-profile"></a>Профиль компонента Database Mail

Профиль компонента Database Mail, используемый агентом SQL Server должен вызываться `AzureManagedInstance_dbmail_profile`. Другие имена профилей компонента Database Mail не ограничены.

### <a name="error-logs-arent-persisted"></a>Журналы ошибок не сохраняются

Журналы ошибок, которые доступны в управляемом экземпляре не сохраняются, и их размер не включен в максимальное ограничение хранилища. Журналы ошибок могут быть автоматически удаляются при отработке отказа.

### <a name="error-logs-are-verbose"></a>Журналы ошибок с подробной информацией

Управляемый экземпляр помещает подробную информацию в журналы ошибок и значительная их часть не имеет значения. В будущем уменьшается объем сведений в журналы ошибок.

**Решение:** Используйте пользовательскую процедуру для чтения журналов ошибок, отфильтровывает некоторые записи не имеет значения. Дополнительные сведения см. в разделе [управляемый экземпляр — sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Область транзакции на две базы данных внутри одного экземпляра не поддерживается

`TransactionScope` Класса в .NET не работает, если две базы данных в одном экземпляре в той же области транзакции отправляются два запроса:

```C#
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

Несмотря на то, что этот код работает с данными в одном экземпляре, требовалось MSDTC.

**Решение:** Используйте [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) для использования другой базы данных в контекст соединения, а не с помощью двух подключений.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Модули среды CLR и иногда для связанных серверов не может ссылаться на локальный IP-адрес

Модулей среды CLR, размещенные в управляемый экземпляр и связанных серверов или распределенные запросы, которые ссылаются на текущий экземпляр, иногда не удается разрешить IP-адрес локального экземпляра. Это временная ошибка.

**Решение:** По возможности используйте контекстные соединения в модуль среды CLR.

### <a name="tde-encrypted-databases-with-a-service-managed-key-dont-support-user-initiated-backups"></a>Баз данных с шифрованием TDE с управляемого службой ключа не поддерживают резервных копий, инициируемых пользователем

Не удается выполнить `BACKUP DATABASE ... WITH COPY_ONLY` в базе данных, зашифрованного с управляемого службой прозрачным шифрованием данных (TDE). Управляемое службой прозрачное шифрование данных заставляет резервные копии шифруются внутреннего ключа прозрачного шифрования данных. Невозможно экспортировать ключ, поэтому невозможно восстановить резервную копию.

**Решение:** Использовать автоматическое резервное копирование и восстановление на момент времени или [управлением клиентом (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) вместо этого. Также можно отключить шифрование в базе данных.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения об управляемых экземплярах см. в разделе [что такое управляемый экземпляр?](sql-database-managed-instance.md)
- Для функций и список сравнения см. в разделе [сравнение функций базы данных SQL Azure](sql-database-features.md).
- Краткое руководство, которое показывает, как создать новый управляемый экземпляр, см. в разделе [Создание управляемого экземпляра](sql-database-managed-instance-get-started.md).
