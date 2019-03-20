---
title: Различия T-SQL Управляемого экземпляра Базы данных SQL Azure | Документация Майкрософт
description: В этой статье рассматриваются различия T-SQL между управляемым экземпляром в Базе данных SQL Azure и SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 03/13/2019
ms.openlocfilehash: 8654899e0a6dfce8f25855eba6c5f4a88af78665
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57903136"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Различия T-SQL между Управляемым экземпляром Базы данных SQL Azure и SQL Server

Вариант развертывания в виде управляемого экземпляра обеспечивает высокий уровень совместимости с локальным ядром СУБД SQL Server. В управляемом экземпляре поддерживается большинство функций ядра СУБД SQL Server.

![Миграция](./media/sql-database-managed-instance/migration.png)

Так как в синтаксисе и поведении все еще есть некоторые различия, они перечислены и описаны в этой статье. <a name="Differences"></a>

- [Доступность](#availability), включая различия в [Always-On](#always-on-availability) и [Azure Backup](#backup).
- [Безопасность](#security), включая различия в [аудите](#auditing), [сертификатах](#certificates), [учетных данных](#credential), [поставщиках служб шифрования](#cryptographic-providers), [именах входа и пользователях](#logins--users), [ключе службы и главном ключе службы](#service-key-and-service-master-key).
- [Конфигурация](#configuration), включая различия в [расширении буферного пула](#buffer-pool-extension), [параметрах сортировки](#collation), [уровнях совместимости](#compatibility-levels), [зеркальном отображении базы данных](#database-mirroring), [параметрах базы данных](#database-options), [агенте SQL Server](#sql-server-agent), [параметрах таблицы](#tables).
- [Функциональные возможности](#functionalities), включая [BULK INSERT или OPENROWSET](#bulk-insert--openrowset), [среды CLR](#clr), [DBCC](#dbcc), [распределенные транзакции](#distributed-transactions), [расширенные события](#extended-events), [внешние библиотеки](#external-libraries), [файловый поток и FileTable](#filestream-and-filetable), [полнотекстовый семантический поиск](#full-text-semantic-search), [связанные службы](#linked-servers), [PolyBase](#polybase), [репликацию](#replication), [инструкцию RESTORE](#restore-statement), [Service Broker](#service-broker), [хранимые процедуры, функции и триггеры](#stored-procedures-functions-triggers).
- [Функции с другим поведением в управляемых экземплярах](#Changes).
- [Временные ограничения и известные проблемы](#Issues)

## <a name="availability"></a>Доступность

### <a name="always-on-availability"></a>Always-On

[Высокий уровень доступности](sql-database-high-availability.md) встроен в Управляемый экземпляр, и пользователи не могут им управлять. Следующие инструкции не поддерживаются:

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql);
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql);
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql);
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql);
- предложение [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) инструкции [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql).

### <a name="backup"></a>Azure Backup

В управляемых экземплярах предусмотрено автоматическое резервное копирование, и пользователи могут создавать полные резервные копии `COPY_ONLY` базы данных. Резервные копирования моментальных снимков разностных данных, журналов и файлов не поддерживаются.

- С помощью Управляемого экземпляра вы можете выполнить резервное копирование базы данных экземпляра только в учетную запись хранения BLOB-объектов Azure.
  - Поддерживается только `BACKUP TO URL`.
  - `FILE`, `TAPE` и устройства резервного копирования не поддерживаются.  
- Большинство общих параметров `WITH` поддерживаются:
  - `COPY_ONLY` является обязательным параметром.
  - `FILE_SNAPSHOT` не поддерживается.
  - Параметры ленты: `REWIND`, `NOREWIND`, `UNLOAD` и `NOUNLOAD` не поддерживаются.
  - Параметры журналов: `NORECOVERY`, `STANDBY` и `NO_TRUNCATE` не поддерживаются.

Ограничения:  

- С помощью Управляемого экземпляра вы можете выполнять резервное копирование в базу данных экземпляра, содержащую до 32 полосковых линий. Такого количества достаточно для баз данных объемом до 4 ТБ, если используется сжатие резервных копий.
- Максимальный размер блока чередования резервного копирования с помощью `BACKUP` команд в управляемом экземпляре составляет 195 ГБ (максимальный размер такого объекта). Увеличьте количество полосковых линий в команде резервного копирования, чтобы уменьшить размер отдельных полосковых линий и не превышать это ограничение.

    > [!TIP]
    > Чтобы обойти это ограничение, при резервном копировании базы данных из SQL Server в локальной среде или на виртуальной машине, можно сделать следующее:
    >
    > - Резервное копирование в `DISK` вместо резервного копирования в `URL`
    > - Отправка файлов резервных копий в хранилище BLOB-объектов
    > - Восстановить в управляемый экземпляр
    >
    > `Restore` Команда в управляемых экземплярах поддерживает большие размеры больших двоичных объектов в файлах резервных копий, так как тип другой BLOB-объекта используется для хранения загружаемых файлов резервных копий.

Сведения о резервном копировании с помощью T-SQL см. в статье [BACKUP (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Безопасность

### <a name="auditing"></a>Аудит

Ниже перечислены основные различия между аудитом в базах данных Базы данных SQL Azure и SQL Server.

- При использовании варианта развертывания в виде управляемого экземпляра в Базе данных SQL Azure аудит выполняется на уровне сервера и сохраняет файлы журнала `.xel` в хранилище BLOB-объектов Azure.
- При использовании варианта развертывания в виде отдельной базы данных и эластичного пула в Базе данных SQL Azure аудит работает на уровне базы данных.
- В SQL Server на локальном компьютере или виртуальной машине аудит выполняется на уровне сервера, но события сохраняются в журналы событий файловой системы или Windows.
  
Аудит XEvent в управляемом экземпляре поддерживает целевые объекты хранилища BLOB-объектов Azure. Журналы файлов и Windows не поддерживаются.

Основные различия в синтаксисе `CREATE AUDIT` для аудита в хранилище BLOB-объектов Azure:

- Предоставляется новый синтаксис `TO URL`, который позволяет указать URL-адрес контейнера хранилища BLOB-объектов Azure, куда будут помещены файлы `.xel`.
- Синтаксис `TO FILE` не поддерживается, так как управляемый экземпляр не может использовать общие папки Windows.

Дополнительные сведения можно найти в разделе   

- [CREATE SERVER AUDIT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [ALTER SERVER AUDIT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Аудит](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Сертификаты

Управляемый экземпляр не может получить доступ к общим папкам и папкам Windows, поэтому действуют следующие ограничения.

- Операции `CREATE FROM`/`BACKUP TO` с файлом не поддерживаются для сертификатов.
- Операции `CREATE`/`BACKUP` с сертификатом из `FILE`/`ASSEMBLY` не поддерживаются. Невозможно использовать файлы закрытых ключей.  

См. статьи [Инструкция CREATE CERTIFICATE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) и [BACKUP CERTIFICATE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql).  
  
**Обходной путь**: создайте скрипт для сертификата и закрытого ключа, сохраните в виде SQL-файла и создайте из двоичного файла:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Учетные данные

Поддерживаются удостоверения только Azure Key Vault и `SHARED ACCESS SIGNATURE`. Пользователи Windows не поддерживаются.

См. статьи [CREATE CREDENTIAL (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) и [ALTER CREDENTIAL (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Поставщики служб шифрования

Управляемый экземпляр не может получить доступ к файлам, поэтому невозможно создать поставщики служб шифрования.

- `CREATE CRYPTOGRAPHIC PROVIDER` не поддерживается. См. статью [CREATE CRYPTOGRAPHIC PROVIDER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` не поддерживается. См. статью [ALTER CRYPTOGRAPHIC PROVIDER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins--users"></a>Имена входа и пользователи

- Поддерживаются имена входа SQL, созданные с помощью `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY` и `FROM SID`. См. статью [CREATE LOGIN (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Субъекты сервера (имена для входа) Azure Active Directory (Azure AD), созданные с помощью синтаксиса [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) или [CREATE USER FROM LOGIN [имя для входа Azure AD]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current), поддерживаются (**общедоступная предварительная версия**). Это имена для входа, созданные на уровне сервера.

    Управляемый экземпляр поддерживает субъекты базы данных Azure AD, созданные с помощью синтаксиса `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Они также называются пользователями автономной базы данных Azure AD.

- Имена для входа Windows, созданные с помощью синтаксиса `CREATE LOGIN ... FROM WINDOWS`, не поддерживаются. Используйте пользователей и имена для входа Azure Active Directory.
- Пользователь Azure AD, создавший экземпляр, обладает [неограниченными правами администратора](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Пользователей уровня базы данных Azure Active Directory (Azure AD) без прав администратора можно создать, используя синтаксис `CREATE USER ... FROM EXTERNAL PROVIDER`. См. раздел [Пользователи без FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users).
- Субъекты сервера (имена для входа) Azure AD поддерживают функции SQL только в пределах одного экземпляра MI. Функции, требующие взаимодействия между несколькими экземплярами, как в одном, так и в разных клиентах Azure AD, не поддерживаются для пользователей Azure AD. Ниже приведены примеры таких функций:

  - репликация транзакций SQL и
  - связанный сервер.

- Установка имени для входа Azure AD, сопоставленного с группой Azure AD, в качестве владельца базы данных, не поддерживается.
- Олицетворение субъектов серверного уровня Azure AD с помощью других субъектов Azure AD поддерживается, включая предложение [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql). Ограничение EXECUTE AS:

  - EXECUTE AS USER не поддерживается для пользователей Azure AD, если имя отличается от имени для входа. Например, если для создания пользователя был использован синтаксис CREATE USER [myAadUser] FROM LOGIN [john@contoso.com], после чего производится попытка олицетворения с помощью синтаксиса EXEC AS USER = _myAadUser_. При создании **пользователя** на основе субъекта сервера (имени для входа) Azure AD необходимо указать user_name, совпадающее с login_name **имени для входа**.
  - Только субъекты серверного уровня SQL (имена для входа), которые являются частью роли `sysadmin`, могут выполнять следующие операции, предназначенные для субъектов Azure AD:

    - EXECUTE AS USER;
    - EXECUTE AS LOGIN.

- **Ограничения общедоступной предварительной версии** в отношении субъектов сервера (имен для входа) Azure AD:

  - Ограничения администратора Active Directory в отношении управляемого экземпляра:

    - Учетную запись администратора Azure AD, используемую для настройки управляемого экземпляра, невозможно применять для создания субъекта сервера (имени для входа) Azure AD в управляемом экземпляре. Необходимо создать первый субъект сервера (имя для входа) Azure AD с помощью учетной записи SQL Server — `sysadmin`. Это временное ограничение, которое будет устранено, как только субъекты сервера (имена для входа) Azure AD станут общедоступными. При попытке использования учетной записи администратора Azure AD для создания имени для входа отобразится следующая ошибка: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - В настоящее время первое имя для входа Azure AD, создаваемое в базе данных master, должно быть создано с помощью стандартной учетной записи SQL Server (а не учетной записи Azure AD). Это должно быть имя для входа `sysadmin`, которое нужно создать с помощью синтаксиса [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) FROM EXTERNAL PROVIDER. После выпуска общедоступной версии это ограничение будет снято и начальное имя для входа Azure AD сможет создавать администратор Active Directory для управляемого экземпляра.
    - Платформа DACFx (экспорт и импорт), используемая с SQL Server Management Studio (SSMS) или SqlPackage, не поддерживается для имен для входа Azure AD. Это ограничение будет устранено, как только субъекты сервера (имена для входа) Azure AD станут общедоступными.
    - Использование субъектов сервера (имен для входа) Azure AD с SSMS:

      - Написание скриптов для имен для входа Azure AD (с помощью любого аутентифицированного имени для входа) не поддерживается.
      - IntelliSense не распознает инструкцию **CREATE LOGIN FROM EXTERNAL PROVIDER** и подчеркнет ее красной линией.

- Только субъект серверного уровня (имя для входа, созданное процессом подготовки управляемого экземпляра), участники ролей сервера (`securityadmin` или `sysadmin`) либо другие имена для входа с разрешением ALTER ANY LOGIN на уровне сервера позволяют создавать субъекты сервера (имена для входа) Azure AD в базе данных master для управляемого экземпляра.
- Если имя для входа представляет собой субъект SQL, команду create для создания имен для входа для учетной записи Azure AD можно использовать только с именами для входа, которые являются частью роли `sysadmin`.
- Имя для входа Azure AD должно входить в Azure AD в каталоге, используемом для управляемого экземпляра SQL Azure.
- Субъекты сервера (имена для входа) Azure AD отображаются в обозревателе объектов, начиная с предварительной версии 5 SSMS 18.0.
- Допускается перекрытие субъектов сервера (имен для входа) Azure AD с учетной записью администратора Azure AD. Субъекты сервера (имена для входа) Azure AD имеют приоритет над администратором Azure AD при разрешении субъекта и применении разрешений к управляемому экземпляру.
- Во время аутентификации для проверки подлинности субъекта применяется следующая последовательность:

    1. Если существующая учетная запись Azure AD напрямую сопоставлена с субъектом сервера (именем для входа) Azure AD (указанным в sys.server_principals как тип "E"), предоставляется доступ и применяются разрешения субъекта сервера (имени для входа) Azure AD.
    2. Если существующая учетная запись Azure AD входит в группу Azure AD, которая напрямую сопоставлена с субъектом сервера (именем для входа) Azure AD (указанным в sys.server_principals как тип "X"), предоставляется доступ и применяются разрешения имени для входа группы Azure AD.
    3. Если учетной записью Azure AD является специально настроенный на портале администратор Azure AD для управляемого экземпляра (который не существует в системных представлениях управляемого экземпляра), то применяются специальные установленные разрешения администратора Azure AD для управляемого экземпляра (устаревший режим).
    4. Если существующая учетная запись Azure AD напрямую сопоставлена с пользователем Azure AD в базе данных (указанным в sys.database_principals как тип "E"), предоставляется доступ и применяются разрешения пользователя базы данных Azure AD.
    5. Если учетная запись Azure AD входит в группу Azure AD, сопоставленную с пользователем Azure AD в базе данных (указанным в sys.database_principals как тип "X"), предоставляется доступ и применяются разрешения имени для входа группы Azure AD.
    6. Если имя для входа Azure AD сопоставлено с учетной записью пользователя или группы Azure AD, то при разрешении аутентификации пользователя будут применены все разрешения этого имени для входа Azure AD.

### <a name="service-key-and-service-master-key"></a>Ключ службы и главный ключ службы

- [Резервное копирование главного ключа](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) не поддерживается (управляется службой базы данных SQL).
- [Восстановление главного ключа](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) не поддерживается (управляется службой базы данных SQL).
- [Резервное копирование главного ключа службы](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) не поддерживается (управляется службой базы данных SQL).
- [Восстановление главного ключа службы](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) не поддерживается (управляется службой базы данных SQL).

## <a name="configuration"></a>Параметр Configuration

### <a name="buffer-pool-extension"></a>Расширение буферного пула

- [Расширение буферного пула](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) не поддерживается.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` не поддерживается. См. статью [ALTER SERVER CONFIGURATION (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Collation

Параметр сортировки экземпляра по умолчанию — `SQL_Latin1_General_CP1_CI_AS`. Этот параметр можно указать как параметр создания. См. статью [Параметры сортировки](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Уровни совместимости

- Поддерживаемые уровни совместимости: 100, 110, 120, 130 и 140.  
- Уровни совместимости ниже 100 не поддерживаются.
- Уровень совместимости по умолчанию для новых баз данных — 140. Для восстановленных баз данных уровень совместимости остается неизменным, если он был 100 и выше.

См. статью [Уровень совместимости инструкции ALTER DATABASE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)

### <a name="database-mirroring"></a>Зеркальное отображение базы данных

Зеркальное отображение базы данных не поддерживается.

- Параметры `ALTER DATABASE SET PARTNER` и `SET WITNESS` не поддерживаются.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` не поддерживается.

Дополнительные сведения см. в статьях [Зеркальное отображение базы данных ALTER DATABASE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) и [CREATE ENDPOINT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Параметры базы данных

- Несколько файлов журнала не поддерживаются.
- Объекты в памяти не поддерживаются на уровне служб "Общего назначения".  
- Для одного экземпляра действует ограничение в 280 файлов. Это значит, что в каждой базе данных может быть не больше 280 файлов. Это ограничение распространяется на файлы данных и файлы журнала.  
- База данных не может содержать файловые группы, содержащие данные файлового потока.  Восстановление завершится со сбоем, если BAK-файл содержит данные `FILESTREAM`.  
- Каждый файл помещается в хранилище BLOB-объектов Azure. Операции ввода-вывода и пропускная способность каждого файла зависят от размера каждого файла.  

#### <a name="create-database-statement"></a>Инструкция CREATE DATABASE

Ниже приведены ограничения `CREATE DATABASE`:

- Невозможно определить файлы и файловые группы.  
- Параметр `CONTAINMENT` не поддерживается.  
- Параметры `WITH` не поддерживаются.  
   > [!TIP]
   > Чтобы обойти эту проблему, используйте `ALTER DATABASE` после `CREATE DATABASE`, чтобы задать параметры базы данных для добавления файлов или задать вложение.  

- Параметр `FOR ATTACH` не поддерживается.
- Параметр `AS SNAPSHOT OF` не поддерживается.

Дополнительные сведения см. в статье [CREATE DATABASE (SQL Server Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Инструкция ALTER DATABASE

Невозможно задать или изменить некоторые свойства файла:

- Путь к файлу невозможно указать в инструкции T-SQL `ALTER DATABASE ADD FILE (FILENAME='path')`. Удалите `FILENAME` из сценария, так как управляемый экземпляр автоматически размещает файлы.  
- Невозможно изменить имя файла с помощью инструкции `ALTER DATABASE`.

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

- Параметры агента SQL Server доступны только для чтения. Процедура `sp_set_agent_properties` не поддерживается в Управляемом экземпляре.  
- Задания
  - Шаги задания T-SQL поддерживаются.
  - Поддерживаются следующие задания репликации:
    - Читатель журнала транзакций.
    - Снимок
    - Распространитель.
  - Шаги задания SSIS поддерживаются.
  - Другие типы шагов заданий пока не поддерживаются, включая:
    - Шаг задания репликации слиянием не поддерживается.  
    - Читатель очереди пока не поддерживается.  
    - Командная оболочка пока не поддерживается.
  - Управляемые экземпляры не могут получить доступ к внешним ресурсам (например, к сетевым папкам с помощью robocopy).  
  - PowerShell пока не поддерживается.
  - Службы Analysis Services не поддерживаются.
- Уведомления поддерживаются частично.
- Поддерживается уведомление по электронной почте. Необходимо настроить профиль компонента Database Mail. Допускается только один профиль компонента Database Mail, и ему должно быть присвоено имя `AzureManagedInstance_dbmail_profile` в общедоступной предварительной версии (временное ограничение).  
  - Пейджер не поддерживается.  
  - NetSend не поддерживается.
  - Оповещения еще не поддерживаются.
  - Прокси-серверы не поддерживаются.  
- Eventlog не поддерживается.

Следующие функции сейчас не поддерживаются, но будут добавлены в будущем:

- прокси-серверы;
- планирование заданий простоя ЦП;
- включение и отключение агента;
- Оповещения

Сведения об агенте SQL Server см. в статье [Агент SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Таблицы

Следующие возможности не поддерживаются:

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED`  

Сведения о создании и изменении таблиц см. в статьях [Инструкция CREATE TABLE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) и [ALTER TABLE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Функциональные возможности

### <a name="bulk-insert--openrowset"></a>Массовая вставка или OpenRowset

Управляемый экземпляр не может получить доступ к общим папкам и папкам Windows, поэтому файлы необходимо импортировать из хранилища BLOB-объектов Azure.

- Во время импорта файлов из хранилища BLOB-объектов Azure `DATASOURCE` является обязательным в команде `BULK INSERT`. См. статью [BULK INSERT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- При считывании содержимого файла из хранилища BLOB-объектов Azure `DATASOURCE` является обязательным в функции `OPENROWSET`. См. статью [OPENROWSET (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>Среда CLR

Управляемый экземпляр не может получить доступ к общим папкам и папкам Windows, поэтому действуют следующие ограничения.

- Поддерживается только `CREATE ASSEMBLY FROM BINARY`. См. статью [CREATE ASSEMBLY (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).  
- `CREATE ASSEMBLY FROM FILE` не поддерживается. См. статью [CREATE ASSEMBLY (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` не может ссылаться на файлы. См. статью [ALTER ASSEMBLY (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="dbcc"></a>DBCC

Недокументированные инструкции DBCC, которые включены в SQL Server, не поддерживаются в управляемых экземплярах.

- `Trace Flags` не поддерживаются. См. статью [DBCC TRACEON - флаги трассировки (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` не поддерживается. См. статью [DBCC TRACEOFF (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` не поддерживается. См. статью [DBCC TRACEON (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Распределенные транзакции

Управляемые экземпляры сейчас не поддерживают ни MSDTC, ни [эластичные транзакции](sql-database-elastic-transactions-overview.md).

### <a name="extended-events"></a>Расширенные события

Некоторые из целевых объектов Windows для XEvents не поддерживаются:

- `etw_classic_sync target` не поддерживается. Храните файлы `.xel` в хранилище BLOB-объектов Azure. См. раздел [Целевой объект etw_classic_sync_target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- `event_file target` не поддерживается. Храните файлы `.xel` в хранилище BLOB-объектов Azure. См. раздел [Целевой объект event_file](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Внешние библиотеки

Внешние библиотеки R и Python в базе данных еще не поддерживаются. См. статью [Изучение служб машины SQL Server](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>Файловый поток и FileTable

- Данные файлового потока не поддерживаются.
- База данных не может содержать файловые группы с данными `FILESTREAM`.
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

- Поддерживаемые целевые объекты: SQL Server и База данных SQL.
- Неподдерживаемые целевые объекты: файлы, Analysis Services и другие реляционные СУБД.

Операции

- Транзакции записи между несколькими экземплярами не поддерживаются.
- `sp_dropserver` поддерживается для удаления связанного сервера. См. статью [sp_dropserver (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- Функцию `OPENROWSET` можно использовать для выполнения запросов только в экземплярах SQL Server (управляемом, локальном или на виртуальных машинах). См. статью [OPENROWSET (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- Функцию `OPENDATASOURCE` можно использовать для выполнения запросов только в экземплярах SQL Server (управляемом, локальном или на виртуальных машинах). В качестве поставщика поддерживаются только значения `SQLNCLI`, `SQLNCLI11` и `SQLOLEDB`. Например, `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. См. статью [OPENDATASOURCE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="polybase"></a>PolyBase

Внешние таблицы, ссылающиеся на файлы в HDFS или хранилище BLOB-объектов Azure, не поддерживаются. Сведения о Polybase см. в статье [Руководство по PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Репликация

Репликация доступна в общедоступной предварительной версии управляемых экземпляров. Дополнительные сведения о репликации см. в статье [Репликация SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance).

### <a name="restore-statement"></a>Инструкция RESTORE

- Поддерживаемый синтаксис
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Неподдерживаемый синтаксис
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Источник  
  - `FROM URL` (хранилище BLOB-объектов) — единственный поддерживаемый параметр.
  - `FROM DISK`/`TAPE` или устройство резервного копирования не поддерживаются.
  - Резервные наборы данных не поддерживаются.
- Параметры `WITH` не поддерживаются (`DIFFERENTIAL`, `STATS` и т. д.).
- `ASYNC RESTORE` — восстановление продолжится даже в случае разрыва соединения с клиентом. При разрыве соединения можно проверить представление `sys.dm_operation_status` о состоянии операции восстановления (также как и для СОЗДАНИЯ и УДАЛЕНИЯ базы данных). См. статью [sys.dm_operation_status (база данных SQL Azure)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).  

Следующие параметры базы данных задаются или переопределяются и не могут быть потом изменены:  

- `NEW_BROKER` (если брокер не включен в BAK-файле).  
- `ENABLE_BROKER` (если брокер не включен в BAK-файле).  
- `AUTO_CLOSE=OFF` (если база данных в BAK-файле содержит `AUTO_CLOSE=ON`).  
- `RECOVERY FULL` (если база данных в BAK-файле содержит модель восстановления `SIMPLE` или `BULK_LOGGED`).
- Добавляется оптимизированная для операций в памяти файловая группа, которой присваивается имя XTP, если она не содержится в исходном BAK-файле.  
- Любая имеющаяся файловая группа, оптимизированная для операций в памяти, переименовывается на XTP.  
- Параметры `SINGLE_USER` и `RESTRICTED_USER` преобразуются в `MULTI_USER`.

Ограничения:  

- Файлы `.BAK`, содержащие несколько резервных наборов данных, невозможно восстановить.
- Файлы `.BAK`, содержащие несколько файлов журнала, невозможно восстановить.
- Восстановление завершится со сбоем, если BAK-файл содержит данные `FILESTREAM`.
- Резервные копии, содержащие базы данных с активными выполняющимися в памяти объектами, сейчас восстановить невозможно.  
- Резервные копии, содержащие базы данных, в которых когда-либо хранились выполняющиеся в памяти объекты, сейчас восстановить невозможно.
- Резервные копии, содержащие базы данных в режиме только для чтения, сейчас восстановить невозможно. Скоро это ограничение будет снято.

Сведения об инструкции [Restore см. в статье](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Компонент Service broker

Компонент Service Broker между экземплярами не поддерживается:

- `sys.routes` — обязательный компонент: выберите адрес из sys.routes. Адрес должен быть ЛОКАЛЬНЫМ для каждого маршрута. См. статью [sys.routes (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`. `CREATE ROUTE` можно использовать только с локальным (`LOCAL`) адресом `ADDRESS`. См. статью [CREATE ROUTE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`. Невозможно выполнить `ALTER ROUTE` со значением `ADDRESS`, отличным от `LOCAL`. См. статью [ALTER ROUTE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql).  

### <a name="stored-procedures-functions-triggers"></a>Хранимые процедуры, функции и триггеры

- `NATIVE_COMPILATION` не поддерживается на уровне общего назначения.
- Следующие параметры [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) не поддерживаются:
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` не поддерживается. См. раздел [Примеры](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` не поддерживается. См. раздел [xp_cmdshell (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` не поддерживаются, включая `sp_addextendedproc`  и `sp_dropextendedproc`. См. статью [Основные расширенные хранимые процедуры (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)
- `sp_attach_db`, `sp_attach_single_file_db` и `sp_detach_db` не поддерживаются. См. статьи [sp_attach_db (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) и [sp_detach_db (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

## <a name="Changes"></a> Изменения в поведении

Следующие переменные, функции и представления возвращают различные результаты:

- `SERVERPROPERTY('EngineEdition')` возвращает значение 8. Это свойство уникально идентифицирует управляемый экземпляр. См. статью [SERVERPROPERTY (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` возвращает значение NULL, так как концепция экземпляра в том виде, в котором она существует в SQL Server, не применяется к управляемому экземпляру. См. статью [SERVERPROPERTY (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` возвращает полное DNS-имя с возможностью подключения, например my-managed-instance.wcus17662feb9ce98.database.windows.net. См. статью [@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` — возвращает полное DNS-имя с возможностью подключения, такое как `myinstance.domain.database.windows.net` для свойств name и data_source. См. статью [sys.servers (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` возвращает значение NULL, так как концепция службы в том виде, в котором она существует в SQL Server, не применяется к управляемому экземпляру. См. статью [@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` поддерживается. Возвращает значение NULL, если имя для входа Azure AD не содержится в sys.syslogins. См. статью [Идентификатор SUSER_ID (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql).  
- `SUSER_SID` не поддерживается. Возвращает неверные данные (временная известная проблема). См. статью [SUSER_SID (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql).
- `GETDATE()` и другие встроенные функции даты и времени всегда возвращают время в часовом поясе UTC. См. статью [GETDATE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql).

## <a name="Issues"></a> Известные проблемы и ограничения

### <a name="tempdb-size"></a>Размер tempdb

Максимальный размер файла `tempdb` не может иметь значение, чем 24 ГБ/core на уровне общего назначения. Max `tempdb` объем на уровне "критически важный для бизнеса" ограничен с размером экземпляра хранилища. `tempdb` всегда разбивается на файлы данных 12. Этот максимальный размер каждого файла невозможно изменить. В `tempdb` невозможно добавить новые файлы. Некоторые запросы могут возвращать ошибку, если им требуется более чем 24 ГБ / ядро в `tempdb`.

### <a name="cannot-restore-contained-database"></a>Не удается восстановить автономной базы данных

Управляемый экземпляр не удается восстановить [автономные базы данных](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Восстановление на определенный момент существующих автономных баз данных не работают в управляемом экземпляре. Эта проблема скоро будет удален и в то же время мы рекомендуем удалить параметр включения из баз данных, которые размещаются в управляемом экземпляре и не используйте параметр включения для рабочих баз данных.

### <a name="exceeding-storage-space-with-small-database-files"></a>Превышение дискового пространства с небольшими файлами баз данных

Для каждого управляемого экземпляра в хранилище резервируется 35 ТБ для диска Azure уровня "Премиум", а каждый файл базы данных размещается на отдельном физическом диске. Поддерживаются диски размером 128 ГБ, 256 ГБ, 512 ГБ, 1 ТБ или 4 ТБ. Неиспользуемое пространство на диске не оплачивается, но суммарный размер дисков Azure уровня "Премиум" не может превышать 35 ТБ. В некоторых случаях из-за внутренней фрагментации размер Управляемого экземпляра, которому требуется не более 8 ТБ дискового пространства, может превысить ограничение в 35 ТБ.

Например, Управляемый экземпляр использует один файл размером 1,2 ТБ, размещенный на диске объемом 4 ТБ, и 248 файлов по 1 ГБ каждый, размещенные на отдельных дисках объемом по 128 ГБ. В данном примере:

- общий размер выделенного дискового хранилища составляет 1 x 4 ТБ + 248 x 128 ГБ = 35 ТБ;
- общий объем зарезервированного пространства для баз данных в экземпляре составляет 1 x 1,2 ТБ + 248 x 1 ГБ = 1,4 ТБ.

Это свидетельствует о том, что в некоторых обстоятельствах при определенном распределении файлов размер Управляемого экземпляра может неожиданно достичь 35 ТБ, выделенных для присоединенного диска Azure уровня "Премиум".

В этом примере существующие базы данных без проблем будут работать и разрастаться при условии, что в них не будут добавляться новые файлы. При этом создать новые базы данных или восстановить имеющиеся не удастся, так как для новых дисков места недостаточно, даже если общий размер всех баз данных не достигает предельного размера экземпляра. Сообщение об ошибке, поступающее в этом случае, плохо отражает ситуацию.

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>Неправильная конфигурация ключа SAS во время восстановления базы данных

`RESTORE DATABASE`, читающая BAK-файл, может постоянно выполнять повторные попытки читать BAK-файл и возвращать ошибку после длительного периода времени, если подписанный URL-адрес в `CREDENTIAL` неверен. Выполните инструкцию RESTORE HEADERONLY перед восстановлением базы данных, чтобы убедиться в правильности ключа SAS.
Удалите начальный символ `?` из ключа SAS, созданного на портале Azure.

### <a name="tooling"></a>Инструментарий

В SQL Server Management Studio (SSMS) и SQL Server Data Tools (SSDT) могут возникать некоторые проблемы во время доступа к управляемому экземпляру.

- Использование субъектов сервера (имен для входа) и пользователей Azure AD (**общедоступная предварительная версия**) в SSDT пока не поддерживается.
- Написание скриптов для субъектов сервера (имен для входа) и пользователей Azure AD (**общедоступная предварительная версия**) не поддерживается в SSMS.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Неправильные имена базы данных в некоторых представлениях, журналах и сообщениях

Несколько системных представлений, счетчиков производительности, сообщений об ошибках, XEvents и записей в журнале ошибок отображают идентификаторы базы данных GUID вместо фактических имен базы данных. Не следует полагаться на эти идентификаторы GUID, так как в будущем они будут заменены на фактические имена базы данных.

### <a name="database-mail-profile"></a>Профиль компонента Database Mail

Профиль компонента database mail агент SQL должен вызываться `AzureManagedInstance_dbmail_profile`.

### <a name="error-logs-are-not-persisted"></a>Журналы ошибок, которые не сохраняются

Журналы ошибок, которые доступны в управляемом экземпляре, не сохраняются, и их размер не включается в максимальный размер хранилища. Журналы ошибок могут автоматически удаляться в случае отработки отказа.

### <a name="error-logs-are-verbose"></a>Журналы ошибок с подробной информацией

Управляемый экземпляр помещает подробную информацию в журналы ошибок, и многие из них не являются связанными. В будущем объем информации в журналах ошибок будет уменьшен.

**Возможное решение**: для чтения журналов ошибок настройте отфильтровывание некоторых несущественных записей. Дополнительные сведения доступны в разделе [Managed Instance – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) (Управляемый экземпляр: sp_readmierrorlog).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Не поддерживается разделение области транзакции на две базы данных внутри одного экземпляра.

`TransactionScope` класс в .NET не работает, если две базы данных в одном экземпляре в той же области транзакции отправляются два запроса:

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

Несмотря на то что этот код работает с данными в одном экземпляре, для него требуется координатор распределенных транзакций (MSDTC).

**Возможное решение**: вместо двух подключений используйте [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase), чтобы подключиться к другой базе данных в том же контексте.

### <a name="clr-modules-and-linked-servers-sometime-cant-reference-local-ip-address"></a>Модули среды CLR и связанные серверы иногда не могут ссылаться на локальный IP-адрес.

Модули среды CLR, помещенные в управляемом экземпляре, связанные серверы и распределенные запросы, ссылающиеся на текущий экземпляр, иногда не могут разрешить IP-адрес локального экземпляра. Это временная ошибка.

**Возможное решение**: если есть возможность, используйте в модуле среды CLR контекстные подключения.

### <a name="tde-encrypted-databases-dont-support-user-initiated-backups"></a>Базы данных, зашифрованные с помощью TDE, не поддерживают резервные копии, инициированные пользователем

Невозможно выполнить `BACKUP DATABASE ... WITH COPY_ONLY` с базой данных, зашифрованной с использованием прозрачного шифрования данных (TDE). TDE вынуждает шифровать резервные копии с помощью внутренних ключей TDE. Так как ключ невозможно экспортировать, вы не сможете восстановить резервную копию.

**Возможное решение**: Используйте автоматические резервные копии и восстановления до точки во времени или отключите шифрование для баз данных.

## <a name="next-steps"></a>Дальнейшие действия

- Сведения об управляемых экземплярах см. в статье [Использование Управляемого экземпляра базы данных SQL с виртуальными сетями и почти полной совместимостью](sql-database-managed-instance.md).
- Сведения о функциях и список сравнения см. в статье [Сравнение функций Базы данных SQL Azure и SQL Server](sql-database-features.md).
- Дополнительные сведения см. в инструкции по [созданию Управляемого экземпляра](sql-database-managed-instance-get-started.md).
