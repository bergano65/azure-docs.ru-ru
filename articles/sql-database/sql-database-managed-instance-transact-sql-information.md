---
title: Различия T-SQL управляемого экземпляра
description: В этой статье обсуждаются различия T-SQL между управляемым экземпляром в Базе данных SQL Azure и SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
ms.date: 11/04/2019
ms.custom: seoapril2019
ms.openlocfilehash: 636fd5fd17838c729cdbc9e2a322c1f991d93948
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186439"
---
# <a name="managed-instance-t-sql-differences-limitations-and-known-issues"></a>Различия в T-SQL управляемого экземпляра, ограничения и известные проблемы

В этой статье кратко описаны различия в синтаксисе и поведении между управляемым экземпляром базы данных SQL Azure и локальными ядро СУБД SQL Server. Вариант развертывания в виде управляемого экземпляра обеспечивает высокий уровень совместимости с локальным ядром СУБД SQL Server. В управляемом экземпляре поддерживается большинство функций ядра СУБД SQL Server.

![Миграция](./media/sql-database-managed-instance/migration.png)

Существуют некоторые ограничения PaaS, появившиеся в Управляемый экземпляр и некоторые изменения в поведении по сравнению с SQL Server. Различия делятся на следующие категории:<a name="Differences"></a>

- [Доступность](#availability) включает различия в [Always-On](#always-on-availability) и [Backups](#backup).
- [Безопасность](#security) включает в себя различия [в аудите](#auditing), [сертификатах](#certificates), [учетных данных](#credential), [поставщиках служб шифрования](#cryptographic-providers), [именах входа и пользователях](#logins-and-users), а также [ключе службы и главный ключ службы](#service-key-and-service-master-key).
- [Конфигурация](#configuration) включает различия в [расширении буферного пула](#buffer-pool-extension), параметрах [сортировки](#collation), [уровнях совместимости](#compatibility-levels), [зеркальном отображении баз данных](#database-mirroring), [параметрах базы данных](#database-options), [Агент SQL Server](#sql-server-agent)и [параметрах таблиц](#tables).
- К [функциональным возможностям](#functionalities) относятся [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [распределенные транзакции](#distributed-transactions), [Расширенные события](#extended-events), [внешние библиотеки](#external-libraries), [FileStream и FileTable](#filestream-and-filetable), [полнотекстовые Семантический поиск](#full-text-semantic-search), [связанные серверы](#linked-servers), [polybase](#polybase), [репликация](#replication), [Восстановление](#restore-statement), [Service Broker](#service-broker), [хранимые процедуры, функции и триггеры](#stored-procedures-functions-and-triggers).
- [Параметры среды](#Environment) , такие как виртуальных сетей и конфигурации подсети.

Большинство этих функций являются ограничениями архитектуры и представляют функции служб.

На этой странице также объясняются [временные известные проблемы](#Issues) , обнаруженные в управляемом экземпляре, которые будут разрешены в будущем.

## <a name="availability"></a>Доступность

### <a name="always-on-availability"></a>Always On

[Высокий уровень доступности](sql-database-high-availability.md) встроен в управляемый экземпляр и не может управляться пользователями. Следующие инструкции не поддерживаются:

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql);
- [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql);
- [ALTER AVAILABILITY GROUP](/sql/t-sql/statements/alter-availability-group-transact-sql);
- [DROP AVAILABILITY GROUP](/sql/t-sql/statements/drop-availability-group-transact-sql);
- Предложение [Set HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) инструкции [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Azure Backup

Управляемые экземпляры имеют автоматическое резервное копирование, поэтому пользователи могут создавать полные резервные копии базы данных `COPY_ONLY`. Разностные резервные копии, журналы и моментальные снимки файлов не поддерживаются.

- С помощью управляемого экземпляра можно создать резервную копию базы данных экземпляра только в учетной записи хранилища BLOB-объектов Azure.
  - Поддерживается только `BACKUP TO URL`.
  - `FILE`, `TAPE`и устройства резервного копирования не поддерживаются.
- Большинство общих вариантов `WITH` поддерживаются.
  - `COPY_ONLY` является обязательным.
  - `FILE_SNAPSHOT` не поддерживается.
  - Параметры ленты: `REWIND`, `NOREWIND`, `UNLOAD`и `NOUNLOAD` не поддерживаются.
  - Параметры конкретного журнала: `NORECOVERY`, `STANDBY`и `NO_TRUNCATE` не поддерживаются.

Ограничения: 

- С помощью управляемого экземпляра можно создать резервную копию базы данных экземпляра в резервной копии с количеством полос до 32, что достаточно для баз данных размером до 4 ТБ, если используется сжатие резервных копий.
- Невозможно выполнить `BACKUP DATABASE ... WITH COPY_ONLY` в базе данных, зашифрованной с помощью прозрачное шифрование данных, управляемого службами (TDE). Управляемые службой TDE принудительное шифрование резервных копий с помощью внутреннего ключа TDE. Ключ не может быть экспортирован, поэтому восстановить резервную копию невозможно. Используйте автоматическое резервное копирование и восстановление на момент времени или используйте [управляемую клиентом (BYOK) TDE](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key) . Также можно отключить шифрование базы данных.
- Максимальный размер полосы резервного копирования с помощью команды `BACKUP` в управляемом экземпляре составляет 195 ГБ, что является максимальным размером большого двоичного объекта. Увеличьте количество полосковых линий в команде резервного копирования, чтобы уменьшить размер отдельных полосковых линий и не превышать это ограничение.

    > [!TIP]
    > Чтобы обойти это ограничение, при резервном копировании базы данных из SQL Server в локальной среде или на виртуальной машине можно выполнить следующие действия.
    >
    > - Создайте резервную копию на `DISK` вместо резервного копирования на `URL`.
    > - Отправьте файлы резервных копий в хранилище BLOB-объектов.
    > - Восстановление в управляемый экземпляр.
    >
    > Команда `Restore` в управляемом экземпляре поддерживает большие размеры больших двоичных объектов в файлах резервных копий, так как для хранения загруженных файлов резервных копий используется другой тип больших двоичных объектов.

Сведения о резервном копировании с помощью T-SQL см. в статье [BACKUP (Transact-SQL)](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Безопасность

### <a name="auditing"></a>Аудит

Ниже перечислены основные различия между аудитом в базах данных в Базе данных SQL Azure и базах данных в SQL Server.

- С помощью параметра развертывания управляемого экземпляра в базе данных SQL Azure аудит работает на уровне сервера. Файлы журнала `.xel` хранятся в хранилище BLOB-объектов Azure.
- При использовании варианта развертывания в виде отдельной базы данных и эластичного пула в Базе данных SQL Azure аудит работает на уровне базы данных.
- В SQL Server локальных или виртуальных машинах аудит работает на уровне сервера. События хранятся в файловой системе или в журналах событий Windows.
 
Аудит XEvent в управляемом экземпляре поддерживает целевые объекты хранилища BLOB-объектов Azure. Журналы файлов и Windows не поддерживаются.

Основные различия в синтаксисе `CREATE AUDIT` для аудита в хранилище BLOB-объектов Azure:

- Предоставляется новый синтаксис `TO URL`, который можно использовать для указания URL-адреса контейнера хранилища больших двоичных объектов Azure, в который помещаются файлы `.xel`.
- Синтаксис `TO FILE` не поддерживается, так как управляемый экземпляр не может получить доступ к общим файловым ресурсам Windows.

Дополнительные сведения см. в следующих источниках. 

- [CREATE SERVER AUDIT (Transact-SQL)](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT (Transact-SQL)](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Аудит](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Сертификаты

Управляемый экземпляр не может получить доступ к файловым ресурсам и папкам Windows, поэтому применяются следующие ограничения.

- Файл `CREATE FROM`/`BACKUP TO` не поддерживается для сертификатов.
- `CREATE`/`BACKUP` сертификат `FILE`/`ASSEMBLY` не поддерживается. Невозможно использовать файлы закрытых ключей. 

См. статьи [Инструкция CREATE CERTIFICATE (Transact-SQL)](/sql/t-sql/statements/create-certificate-transact-sql) и [BACKUP CERTIFICATE (Transact-SQL)](/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Обходное решение**. вместо создания резервной копии сертификата и восстановления резервной копии [получите двоичное содержимое сертификата и закрытый ключ, сохраните его в виде SQL Server и создайте из двоичного файла](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database):

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Учетные данные

Поддерживаются удостоверения только Azure Key Vault и `SHARED ACCESS SIGNATURE`. Пользователи Windows не поддерживаются.

См. статьи [CREATE CREDENTIAL (Transact-SQL)](/sql/t-sql/statements/create-credential-transact-sql) и [ALTER CREDENTIAL (Transact-SQL)](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Поставщики служб шифрования

Управляемый экземпляр не может получить доступ к файлам, поэтому создание поставщиков служб шифрования невозможно:

- `CREATE CRYPTOGRAPHIC PROVIDER` не поддерживается. См. статью [CREATE CRYPTOGRAPHIC PROVIDER (Transact-SQL)](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` не поддерживается. См. статью [ALTER CRYPTOGRAPHIC PROVIDER (Transact-SQL)](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Имена входа и пользователи

- Поддерживаются имена входа SQL, созданные с помощью `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`и `FROM SID`. См. статью [CREATE LOGIN (Transact-SQL)](/sql/t-sql/statements/create-login-transact-sql).
- Поддерживаются субъекты (имена входа) на сервере Azure Active Directory (Azure AD), созданные с помощью синтаксиса [создания имени входа](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) или [создания пользователя из имени входа [Azure AD login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) . Эти имена входа создаются на уровне сервера.

    Управляемый экземпляр поддерживает субъекты базы данных Azure AD с синтаксисом `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Эта функция также называется пользователями автономной базы данных Azure AD.

- Имена входа Windows, созданные с помощью синтаксиса `CREATE LOGIN ... FROM WINDOWS`, не поддерживаются. Используйте пользователей и имена для входа Azure Active Directory.
- Пользователь Azure AD, создавший экземпляр, имеет [неограниченные права администратора](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Пользователи уровня базы данных Azure AD без прав администратора могут создаваться с помощью синтаксиса `CREATE USER ... FROM EXTERNAL PROVIDER`. См [. раздел Создание пользователя... ОТ внешнего поставщика](sql-database-manage-logins.md#non-administrator-users).
- Участники сервера Azure AD (имена входа) поддерживают функции SQL только в пределах одного управляемого экземпляра. Функции, требующие взаимодействия между экземплярами, независимо от того, находятся ли они в одном клиенте Azure AD или разных клиентах, не поддерживаются для пользователей Azure AD. Ниже приведены примеры таких функций:

  - Репликация транзакций SQL.
  - Сервер канала.

- Установка имени для входа Azure AD, сопоставленного с группой Azure AD, в качестве владельца базы данных, не поддерживается.
- Поддерживается олицетворение участников уровня сервера Azure AD с помощью других участников Azure AD, например предложения [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) . Ограничения EXECUTE AS:

  - ВЫПОЛНЕНИЕ от имени пользователя не поддерживается для пользователей Azure AD, если оно отличается от имени для входа. Например, когда пользователь создается с помощью синтаксиса CREATE [Мяадусер] из имени входа [john@contoso.com] и выполняется попытка олицетворения через EXEC от имени USER = _мяадусер_. При создании **пользователя** на сервере-участнике Azure AD (имя входа) укажите user_name как тот же login_name, что и для **входа**.
  - Только участники уровня SQL Server (имена входа), являющиеся частью роли `sysadmin`, могут выполнять следующие операции, предназначенные для участников Azure AD:

    - EXECUTE AS USER;
    - EXECUTE AS LOGIN.

- Экспорт и импорт базы данных с использованием BACPAC-файлов поддерживается для пользователей Azure AD в управляемом экземпляре с помощью [SSMS v 18.4 или более поздней версии](/sql/ssms/download-sql-server-management-studio-ssms)или [SqlPackage. exe](/sql/tools/sqlpackage-download).
  - С помощью BACPAC-файла базы данных поддерживаются следующие конфигурации: 
    - Экспорт и импорт базы данных между разными экземплярами управления в пределах одного домена Azure AD.
    - Экспортируйте базу данных из управляемого экземпляра и импортируйте ее в базу данных SQL в том же домене Azure AD. 
    - Экспортируйте базу данных из базы данных SQL и импортируйте ее в управляемый экземпляр в том же домене Azure AD.
    - Экспорт базы данных из управляемого экземпляра и импорт в SQL Server (версии 2012 или более поздней).
      - В этой конфигурации все пользователи Azure AD создаются как субъекты базы данных SQL (пользователи) без имен входа. Тип пользователей указывается как SQL (отображается как SQL_USER в sys. database_principals). Их разрешения и роли остаются в метаданных базы данных SQL Server и могут использоваться для олицетворения. Однако они не могут использоваться для доступа к SQL Server и входа в него с помощью учетных данных.

- Только имя входа субъекта уровня сервера, созданное процессом подготовки управляемого экземпляра, члены ролей сервера, такие как `securityadmin` или `sysadmin`или другие имена входа с разрешением ALTER ANY LOGIN на уровне сервера, могут создать сервер Azure AD. Участники (имена входа) в базе данных master для управляемого экземпляра.
- Если имя входа является субъектом SQL, только имена входа, являющиеся частью роли `sysadmin`, могут использовать команду CREATE для создания имен входа для учетной записи Azure AD.
- Имя входа Azure AD должно быть членом Azure AD в том же каталоге, который используется для управляемого экземпляра базы данных SQL Azure.
- Участники сервера Azure AD (имена входа) отображаются в обозревателе объектов, начиная с версии SQL Server Management Studio 18,0 Предварительная версия 5.
- Допускается перекрытие субъектов сервера (имен для входа) Azure AD с учетной записью администратора Azure AD. Участники сервера Azure AD (имена входа) имеют приоритет над администратором Azure AD при разрешении субъекта и применении разрешений к управляемому экземпляру.
- Во время проверки подлинности применяется следующая последовательность действий для разрешения субъекта проверки подлинности:

    1. Если учетная запись Azure AD существует как непосредственно сопоставленная с субъектом-сервером Azure AD (именем входа), которая присутствует в sys. server_principals как тип "E", предоставьте доступ и примените разрешения участника на сервере Azure AD (имя входа).
    2. Если учетная запись Azure AD является членом группы Azure AD, сопоставленной с участником сервера Azure AD (именем входа), который имеется в sys. server_principals как тип "X", предоставьте доступ и примените разрешения для имени входа группы Azure AD.
    3. Если учетная запись Azure AD является специальным настроенным на портале администратором Azure AD для управляемого экземпляра, который не существует в системных представлениях управляемых экземпляров, примените специальные разрешения для администратора Azure AD для управляемого экземпляра (устаревший режим).
    4. Если учетная запись Azure AD существует как непосредственно сопоставленная с пользователем Azure AD в базе данных, которая присутствует в sys. database_principals как тип "E", предоставьте доступ и примените разрешения для пользователя базы данных Azure AD.
    5. Если учетная запись Azure AD является членом группы Azure AD, сопоставленной с пользователем Azure AD в базе данных, которая содержится в представлении sys. database_principals как тип "X", предоставьте доступ и примените разрешения для имени входа группы Azure AD.
    6. Если есть имя входа Azure AD, сопоставленное с учетной записью пользователя Azure AD или учетной записью группы Azure AD, которая разрешается пользователю, который выполняет проверку подлинности, применяются все разрешения из этого имени входа Azure AD.

### <a name="service-key-and-service-master-key"></a>Ключ службы и главный ключ службы

- [Резервное копирование главного ключа](/sql/t-sql/statements/backup-master-key-transact-sql) не поддерживается (управляется службой базы данных SQL).
- [Восстановление главного ключа](/sql/t-sql/statements/restore-master-key-transact-sql) не поддерживается (управляется службой базы данных SQL).
- [Резервное копирование главного ключа службы](/sql/t-sql/statements/backup-service-master-key-transact-sql) не поддерживается (управляется службой базы данных SQL).
- [Восстановление главного ключа службы](/sql/t-sql/statements/restore-service-master-key-transact-sql) не поддерживается (управляется службой базы данных SQL).

## <a name="configuration"></a>Конфигурация

### <a name="buffer-pool-extension"></a>Расширение буферного пула

- [Расширение буферного пула](/sql/database-engine/configure-windows/buffer-pool-extension) не поддерживается.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` не поддерживается. См. статью [ALTER SERVER CONFIGURATION (Transact-SQL)](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Collation

Параметр сортировки экземпляра по умолчанию — `SQL_Latin1_General_CP1_CI_AS`. Этот параметр можно указать как параметр создания. См. статью [Параметры сортировки](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Уровни совместимости

- Поддерживаемые уровни совместимости: 100, 110, 120, 130, 140 и 150.
- Уровни совместимости ниже 100 не поддерживаются.
- Уровень совместимости по умолчанию для новых баз данных — 140. Для восстановленных баз данных уровень совместимости остается неизменным, если он был 100 и выше.

См. статью [Уровень совместимости инструкции ALTER DATABASE (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)

### <a name="database-mirroring"></a>Зеркальное отображение базы данных

Зеркальное отображение базы данных не поддерживается.

- Параметры `ALTER DATABASE SET PARTNER` и `SET WITNESS` не поддерживаются.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` не поддерживается.

Дополнительные сведения см. в статьях [Зеркальное отображение базы данных ALTER DATABASE (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) и [CREATE ENDPOINT (Transact-SQL)](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Параметры базы данных

- Несколько файлов журнала не поддерживаются.
- Объекты в памяти не поддерживаются на уровне служб "Общего назначения". 
- Существует ограничение в 280 файлов на экземпляр общего назначения, что подразумевает максимум 280 файлов на одну базу данных. К этому ограничению подсчитываются файлы данных и журналов на общего назначения уровне. [Критически важный для бизнеса уровень поддерживает 32 767 файлов на одну базу данных](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- База данных не может содержать файловые группы, содержащие данные FILESTREAM. Восстановление завершается сбоем, если bak содержит `FILESTREAM` данные. 
- Каждый файл помещается в хранилище BLOB-объектов Azure. Операции ввода-вывода и пропускная способность каждого файла зависят от размера каждого файла.

#### <a name="create-database-statement"></a>Инструкция CREATE DATABASE

На `CREATE DATABASE`применяются следующие ограничения.

- Невозможно определить файлы и файловые группы. 
- Параметр `CONTAINMENT` не поддерживается. 
- Параметры `WITH` не поддерживаются. 
   > [!TIP]
   > В качестве обходного решения используйте `ALTER DATABASE` после `CREATE DATABASE`, чтобы задать параметры базы данных для добавления файлов или установки вложений. 

- Параметр `FOR ATTACH` не поддерживается.
- Параметр `AS SNAPSHOT OF` не поддерживается.

Дополнительные сведения см. в статье [CREATE DATABASE (SQL Server Transact-SQL)](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Инструкция ALTER DATABASE

Невозможно задать или изменить некоторые свойства файла:

- Путь к файлу не может быть указан в `ALTER DATABASE ADD FILE (FILENAME='path')` инструкции T-SQL. Удалите `FILENAME` из сценария, так как управляемый экземпляр автоматически помещает файлы. 
- Имя файла нельзя изменить с помощью инструкции `ALTER DATABASE`.

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

Дополнительные сведения см. в статье [Параметры инструкции ALTER DATABASE для файлов и файловых групп (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>Агент SQL Server

- Включение и отключение агент SQL Server в настоящее время не поддерживается в управляемом экземпляре. Агент SQL работает постоянно.
- Параметры агент SQL Server доступны только для чтения. Процедура `sp_set_agent_properties` не поддерживается в управляемом экземпляре. 
- Задания
  - Шаги задания T-SQL поддерживаются.
  - Поддерживаются следующие задания репликации:
    - Читатель журнала транзакций.
    - Снимок
    - Распространитель.
  - Поддерживаются шаги задания служб SSIS.
  - Другие типы шагов заданий в настоящее время не поддерживаются:
    - Шаг задания репликации слиянием не поддерживается. 
    - Читатель очереди пока не поддерживается. 
    - Командная оболочка пока не поддерживается.
  - Управляемые экземпляры не могут получить доступ к внешним ресурсам, например к сетевым общим папкам через Robocopy. 
  - SQL Server Analysis Services не поддерживаются.
- Уведомления поддерживаются частично.
- Уведомление по электронной почте поддерживается, хотя для этого необходимо настроить профиль Database Mail. Агент SQL Server может использовать только один профиль Database Mail и должен вызываться `AzureManagedInstance_dbmail_profile`. 
  - Пейджер не поддерживается.
  - NetSend не поддерживается.
  - Предупреждения пока не поддерживаются.
  - Прокси-серверы не поддерживаются.
- Журнал событий не поддерживается.

В настоящее время следующие функции агента SQL Server не поддерживаются:

- прокси-серверы;
- Планирование заданий на неактивном ЦП
- Включение или отключение агента
- Предупреждения

Сведения об агенте SQL Server см. в статье [Агент SQL Server](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Таблицы

Следующие типы таблиц не поддерживаются:

- [ПОТОКА](/sql/relational-databases/blob/filestream-sql-server)
- [ПРОВЕРОЧ](/sql/relational-databases/blob/filetables-sql-server)
- [Внешняя таблица](/sql/t-sql/statements/create-external-table-transact-sql) (polybase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (не поддерживается только на уровне общего назначения)

Сведения о создании и изменении таблиц см. в разделе [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) и [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Функциональные возможности

### <a name="bulk-insert--openrowset"></a>BULK INSERT/OPENROWSET

Управляемый экземпляр не может получить доступ к файловым ресурсам и папкам Windows, поэтому файлы необходимо импортировать из хранилища BLOB-объектов Azure:

- `DATASOURCE` требуется в команде `BULK INSERT` при импорте файлов из хранилища BLOB-объектов Azure. См. статью [BULK INSERT (Transact-SQL)](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` требуется в функции `OPENROWSET` при чтении содержимого файла из хранилища BLOB-объектов Azure. См. статью [OPENROWSET (Transact-SQL)](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` можно использовать для чтения данных из других отдельных баз данных SQL Azure, управляемых экземпляров или экземпляров SQL Server. Другие источники, такие как базы данных Oracle или файлы Excel, не поддерживаются.

### <a name="clr"></a>Среда CLR

Управляемый экземпляр не может получить доступ к файловым ресурсам и папкам Windows, поэтому применяются следующие ограничения.

- Поддерживается только `CREATE ASSEMBLY FROM BINARY`. См. раздел [Создание АССЕМ артефакт из двоичного файла](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` не поддерживается. См. статью [CREATE ASSEMBLY (Transact-SQL)](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` не может ссылаться на файлы. См. статью [ALTER ASSEMBLY (Transact-SQL)](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Database Mail (db_mail)
 - `sp_send_dbmail` не может отправить вложения с помощью параметра @file_attachments. Локальная файловая система и внешние общие папки или хранилище BLOB-объектов Azure недоступны из этой процедуры.
 - Ознакомьтесь с известными проблемами, связанными с параметрами `@query` и проверкой подлинности.
 
### <a name="dbcc"></a>DBCC

Недокументированные инструкции DBCC, включенные в SQL Server, не поддерживаются в управляемых экземплярах.

- Поддерживается только ограниченное число глобальных флагов трассировки. `Trace flags` уровня сеанса не поддерживаются. См. раздел [Флаги трассировки](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) и [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) работают с ограниченным числом глобальных флагов трассировки.
- [Инструкция DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) с параметрами REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST и REPAIR_REBUILD не может быть использована, так как невозможно задать базу данных в режиме `SINGLE_USER` — см. раздел [изменение различий базы данных](#alter-database-statement). Потенциальные повреждения базы данных обрабатываются группой поддержки Azure. Если вы увидите повреждение базы данных, которое следует исправить, обратитесь в службу поддержки Azure.

### <a name="distributed-transactions"></a>Распределенные транзакции

В настоящее время службы MSDTC и [эластичные транзакции](sql-database-elastic-transactions-overview.md) не поддерживаются в управляемых экземплярах.

### <a name="extended-events"></a>Расширенные события

Некоторые целевые объекты Windows для расширенных событий (XEvents) не поддерживаются:

- Целевой объект `etw_classic_sync` не поддерживается. Храните файлы `.xel` в хранилище BLOB-объектов Azure. См. раздел [Целевой объект etw_classic_sync_target](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Целевой объект `event_file` не поддерживается. Храните файлы `.xel` в хранилище BLOB-объектов Azure. См. раздел [Целевой объект event_file](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Внешние библиотеки

В базах данных R и Python внешние библиотеки пока не поддерживаются. См. статью [Изучение служб машины SQL Server](/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FILESTREAM и FileTable

- Данные FILESTREAM не поддерживаются.
- База данных не может содержать файловые группы с `FILESTREAM` данными.
- `FILETABLE` не поддерживается.
- Таблицы не могут содержать типы `FILESTREAM`.
- Следующие функции не поддерживаются:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Дополнительные сведения см. в статьях [FILESTREAM (SQL Server)](/sql/relational-databases/blob/filestream-sql-server) и [Таблицы FileTable (SQL Server)](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Полнотекстовый семантический поиск

[Семантический поиск](/sql/relational-databases/search/semantic-search-sql-server) не поддерживается.

### <a name="linked-servers"></a>Связанные службы

Связанные службы в управляемых экземплярах поддерживают ограниченное число целевых объектов.

- Поддерживаемые целевые объекты — это управляемые экземпляры, отдельные базы данных и экземпляры SQL Server. 
- Связанные серверы не поддерживают распределенные транзакции с возможностью записи (MS DTC).
- Неподдерживаемые целевые объекты — это файлы, Analysis Services и другие РСУБД. Попробуйте использовать собственный импорт CSV из хранилища BLOB-объектов Azure с помощью `BULK INSERT` или `OPENROWSET` в качестве альтернативы для импорта файлов.

Операции

- Транзакции записи между несколькими экземплярами не поддерживаются.
- `sp_dropserver` поддерживается для удаления связанного сервера. См. статью [sp_dropserver (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- Функцию `OPENROWSET` можно использовать для выполнения запросов только в экземплярах SQL Server. Они могут быть как управляемыми, так и локальными, так и виртуальными машинами. См. статью [OPENROWSET (Transact-SQL)](/sql/t-sql/functions/openrowset-transact-sql).
- Функцию `OPENDATASOURCE` можно использовать для выполнения запросов только в экземплярах SQL Server. Они могут быть как управляемыми, так и локальными, так и виртуальными машинами. В качестве поставщика поддерживаются только значения `SQLNCLI`, `SQLNCLI11`и `SQLOLEDB`. Например, `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. См. статью [OPENDATASOURCE (Transact-SQL)](/sql/t-sql/functions/opendatasource-transact-sql).
- Связанные серверы нельзя использовать для чтения файлов (Excel, CSV) из общих сетевых папок. Попробуйте использовать [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) или [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) , считывающие CSV-файлы из хранилища BLOB-объектов Azure. Отслеживание запросов к [элементу обратной связи управляемого экземпляра](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Внешние таблицы, которые ссылаются на файлы в HDFS или хранилище BLOB-объектов Azure, не поддерживаются. Дополнительные сведения о Polybase см. в разделе [polybase](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Репликация

- Поддерживаются типы моментальных снимков и двунаправленной репликации. Репликация слиянием, одноранговая репликация и обновляемые подписки не поддерживаются.
- [Репликация транзакций](sql-database-managed-instance-transactional-replication.md) доступна для общедоступной предварительной версии управляемого экземпляра с некоторыми ограничениями:
    - Все типы участников репликации (издатель, распространитель, подписчик по запросу и подписчик push-уведомлений) можно разместить на управляемых экземплярах, но издатель и распространитель должны быть либо в облаке, либо в локальной среде.
    - Управляемые экземпляры могут взаимодействовать с последними версиями SQL Server. Поддерживаемые версии см. [здесь](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems).
    - Репликация транзакций имеет некоторые [Дополнительные требования к сети](sql-database-managed-instance-transactional-replication.md#requirements).

Сведения о настройке репликации см. в [руководстве по репликации](replication-with-sql-database-managed-instance.md).


Если репликация включена для базы данных в [группе отработки отказа](sql-database-auto-failover-group.md), администратор управляемого экземпляра должен очистить все публикации на старом первичном ресурсе и перенастроить их на новом первичном экземпляре после отработки отказа. В этом сценарии необходимы следующие действия.

1. Останавливает все задания репликации, выполняющиеся в базе данных, если они есть.
2. Удалите метаданные подписки из издателя, выполнив следующий скрипт в базе данных издателя:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Удаление метаданных подписки с подписчика. Выполните следующий скрипт в базе данных подписки на экземпляре подписчика:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Принудительно удалите все объекты репликации из издателя, выполнив следующий скрипт в опубликованной базе данных:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Принудительно удалить старый распространитель из исходного основного экземпляра (при восстановлении размещения на старом первичном экземпляре, который использовался для создания распространителя). Выполните следующий скрипт в базе данных master в старом управляемом экземпляре распространителя:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

### <a name="restore-statement"></a>Инструкция RESTORE 

- Поддерживаемый синтаксис:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Неподдерживаемый синтаксис:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Источник: 
  - единственным поддерживаемым вариантом является `FROM URL` (хранилище BLOB-объектов Azure).
  - `FROM DISK`/`TAPE` или устройство резервного копирования не поддерживаются.
  - Резервные наборы данных не поддерживаются.
- Параметры `WITH` не поддерживаются, например не `DIFFERENTIAL` или `STATS`.
- `ASYNC RESTORE`: восстановление продолжится, даже если соединение с клиентом будет прервано. Если подключение разорвано, можно проверить `sys.dm_operation_status` представление о состоянии операции восстановления, а также для создания и удаления базы данных. См. статью [sys.dm_operation_status (база данных SQL Azure)](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Следующие параметры базы данных задаются или переопределяются, и их нельзя изменить позже: 

- `NEW_BROKER`, если брокер не включен в BAK File. 
- `ENABLE_BROKER`, если брокер не включен в BAK File. 
- `AUTO_CLOSE=OFF`, `AUTO_CLOSE=ON`ли база данных в BAK-файле. 
- `RECOVERY FULL`, если база данных в BAK-файле имеет `SIMPLE` или `BULK_LOGGED` режим восстановления.
- Файловая группа, оптимизированная для памяти, добавляется и называется XTP, если она не находилась в файле source. bak. 
- Любая существующая файловая группа, оптимизированная для памяти, переименовывается в XTP. 
- Параметры `SINGLE_USER` и `RESTRICTED_USER` преобразуются в `MULTI_USER`.

Ограничения: 

- Резервные копии поврежденных баз данных могут быть восстановлены в зависимости от типа повреждения, но автоматические резервные копии не будут выполняться до устранения повреждений. Чтобы предотвратить эту ошибку, убедитесь, что вы запускаете `DBCC CHECKDB` на исходном экземпляре и используете `WITH CHECKSUM` резервного копирования.
- Восстановление `.BAK` файла базы данных, содержащего все ограничения, описанные в этом документе (например, `FILESTREAM` или `FILETABLE` объекты), невозможно восстановить на Управляемый экземпляр.
- не удается восстановить `.BAK` файлы, содержащие несколько резервных наборов данных. 
- не удается восстановить `.BAK` файлы, содержащие несколько файлов журнала.
- Резервные копии, содержащие базы данных размером более 8 ТБ, активные объекты OLTP в памяти или количество файлов, размер которых превышает 280 файлов на экземпляр, не могут быть восстановлены на экземпляре общего назначения. 
- Резервные копии, содержащие базы данных размером более 4 ТБ или объекты OLTP в памяти с общим размером, превышающим размер, описанный в разделе [ограничения ресурсов](sql-database-managed-instance-resource-limits.md) , не могут быть восстановлены в критически важный для бизнеса экземпляре.
Дополнительные сведения о инструкциях RESTORE см. в разделе [инструкции RESTORE](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > Те же ограничения применяются ко встроенной операции восстановления на момент времени. Например, база данных общего назначения более 4 ТБ не может быть восстановлена в экземпляре критически важный для бизнеса. Критически важный для бизнеса базу данных с файлами в памяти OLTP или более 280 файлов нельзя восстановить в экземпляре общего назначения.

### <a name="service-broker"></a>Компонент Service broker

Компонент Service Broker между экземплярами не поддерживается:

- `sys.routes`: в качестве необходимого компонента необходимо выбрать адрес из каталога sys. routes. Адрес должен быть ЛОКАЛЬным по отношению к каждому маршруту. См. статью [sys.routes (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: нельзя использовать `CREATE ROUTE` с `ADDRESS`, кроме `LOCAL`. См. статью [CREATE ROUTE (Transact-SQL)](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: нельзя использовать `ALTER ROUTE` с `ADDRESS`, кроме `LOCAL`. См. статью [ALTER ROUTE (Transact-SQL)](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Хранимые процедуры, функции и триггеры

- `NATIVE_COMPILATION` не поддерживается на уровне общего назначения.
- Следующие параметры [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) не поддерживаются: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` не поддерживается. См. раздел [Примеры](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` не поддерживается. См. раздел [xp_cmdshell (Transact-SQL)](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` не поддерживаются, в том числе `sp_addextendedproc` и `sp_dropextendedproc`. См. раздел [Расширенные хранимые процедуры](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` и `sp_detach_db` не поддерживаются. См. статьи [sp_attach_db (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) и [sp_detach_db (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Системные функции и переменные

Следующие переменные, функции и представления возвращают различные результаты:

- `SERVERPROPERTY('EngineEdition')` возвращает значение 8. Это свойство уникально идентифицирует управляемый экземпляр. См. статью [SERVERPROPERTY (Transact-SQL)](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` возвращает значение NULL, так как понятие экземпляра в том виде, в котором оно существует для SQL Server, не применяется к управляемому экземпляру. См. статью [SERVERPROPERTY (Transact-SQL)](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` возвращает полное имя (Connected) DNS, например my-managed-instance.wcus17662feb9ce98.database.windows.net. См. статью [@SERVERNAME](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` возвращает полное имя "Connected" DNS, например `myinstance.domain.database.windows.net` для свойств "Name" и "data_source". См. статью [sys.servers (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` возвращает значение NULL, так как концепция службы в том виде, в котором она существует для SQL Server, не применяется к управляемому экземпляру. См. статью [@SERVICENAME](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` поддерживается. Он возвращает значение NULL, если имя входа Azure AD отсутствует в sys. syslogins. См. статью [Идентификатор SUSER_ID (Transact-SQL)](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` не поддерживается. Возвращаются неверные данные, что является временной известной проблемой. См. статью [SUSER_SID (Transact-SQL)](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Environment"></a>Ограничения среды

### <a name="subnet"></a>Подсеть
-  Другие ресурсы (например, виртуальные машины) нельзя поместить в подсеть, в которой развернут управляемый экземпляр. Разверните эти ресурсы с помощью другой подсети.
- Подсеть должна иметь достаточное количество доступных [IP-адресов](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Минимальное значение — 16, хотя в подсети рекомендуется иметь по крайней мере 32 IP-адресов.
- [Конечные точки службы не могут быть связаны с подсетью управляемого экземпляра](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Убедитесь, что при создании виртуальной сети параметр конечные точки службы отключен.
- Количество виртуальных ядер и типов экземпляров, которые можно развернуть в регионе, имеет некоторые [ограничения и](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)ограничения.
- Существуют некоторые [правила безопасности, которые необходимо применить к подсети](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>Виртуальная сеть
- Виртуальную сеть можно развернуть с помощью модели ресурсов — классическая модель для виртуальной сети не поддерживается.
- После создания управляемого экземпляра перемещение управляемого экземпляра или виртуальной сети в другую группу ресурсов или подписку не поддерживается.
- Некоторые службы, такие как среды службы приложений, приложения логики и управляемые экземпляры (используемые для георепликации, репликации транзакций или с помощью связанных серверов), не могут получить доступ к управляемым экземплярам в разных регионах, если их виртуальных сетей подключены с помощью [глобального пиринг](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Вы можете подключаться к этим ресурсам через ExpressRoute или VNet-VNet через шлюзы виртуальной сети.

### <a name="tempdb"></a>БАЗЕ

Максимальный размер файла `tempdb` не может превышать 24 ГБ на ядро на уровне общего назначения. Максимальный размер `tempdb` на уровне критически важный для бизнеса ограничивается размером хранилища экземпляра. `Tempdb` размер файла журнала ограничен 120 ГБ на уровне общего назначения. Некоторые запросы могут возвращать ошибку, если им требуется более 24 ГБ на ядро в `tempdb` или если они создают более 120 ГБ данных журнала.

### <a name="error-logs"></a>Журналы ошибок

Управляемый экземпляр помещает подробные сведения в журналы ошибок. Существует множество внутренних системных событий, которые регистрируются в журнале ошибок. Используйте пользовательскую процедуру для чтения журналов ошибок, которые отфильтровывают некоторые ненужные записи. Дополнительные сведения см. в разделе [управляемый экземпляр — sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) или [расширение управляемого экземпляра (Предварительная версия)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) для Azure Data Studio.

## <a name="Issues"></a>Известные проблемы

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Ограничения памяти выполняющейся в памяти OLTP не применяются

**Дата:** Октябрь 2019

Критически важный для бизнеса уровня служб не будет правильно применять [максимальные ограничения памяти для оптимизированных для памяти объектов](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) в некоторых случаях. Управляемый экземпляр может позволить рабочей нагрузке использовать больше памяти для операций OLTP в памяти, что может повлиять на доступность и стабильность экземпляра. Запросы выполняющейся в памяти OLTP, которые достигли ограничений, могут не завершаться сбоем немедленно. Эта проблема будет исправлена в ближайшее время. Запросы, в которых используется больше памяти OLTP в памяти, будут завершаться сбоем быстрее, если достигнут [предел](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Обходное решение.** [отслеживайте использование хранилища выполняющейся в памяти OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) с помощью [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) , чтобы убедиться, что Рабочая нагрузка не использует больше доступной памяти. Увеличьте ограничения памяти, зависящие от числа виртуальных ядер, или оптимизируйте рабочую нагрузку для использования меньшего объема памяти.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>При попытке удаления непустого файла возвращена неверная ошибка

**Дата:** Октябрь 2019

SQL Server и Управляемый экземпляр [не позволяют пользователю удалять непустые файлы](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). При попытке удаления непустого файла данных с помощью оператора `ALTER DATABASE REMOVE FILE` `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` ошибка не будет возвращена немедленно. Управляемый экземпляр будет пытаться удалить файл, и операция завершится ошибкой после 30 мин с `Internal server error`.

Инструкции по **решению**: удалите содержимое файла с помощью команды `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)`. Если это единственный файл в файловой группе, необходимо удалить данные из таблицы или секции, связанной с этой файловой группой, перед сжатием файла и при необходимости загрузить эти данные в другую таблицу или секцию.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Изменение уровня служб и операций создания экземпляра заблокировано текущим восстановлением базы данных

**Дата:** Sep 2019

Текущая инструкция `RESTORE`, процесс миграции данных миграции служб и встроенное восстановление до точки во времени приведет к блокировке обновления уровня служб или изменения размера существующего экземпляра и создания новых экземпляров до завершения процесса восстановления. Процесс восстановления блокирует эти операции с управляемыми экземплярами и пулами экземпляров в той же подсети, где выполняется процесс восстановления. Экземпляры в пулах экземпляров не затрагиваются. Операции создания или изменения уровня служб не будут завершаться сбоем или превышено время ожидания — они будут продолжены после завершения или отмены процесса восстановления.

**Решение**. Дождитесь завершения процесса восстановления или отмените процесс восстановления, если операция создания или обновления уровня службы имеет более высокий приоритет.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Resource Governor на уровне служб критически важный для бизнеса может потребоваться перенастроить после отработки отказа

**Дата:** Sep 2019

[Resource Governor](/sql/relational-databases/resource-governor/resource-governor) функция, которая позволяет ограничить ресурсы, назначенные пользовательской рабочей нагрузке, может неправильно классифицировать определенную рабочую нагрузку пользователя после отработки отказа или изменения уровня служб (например, изменение максимального Виртуальное ядро или максимального экземпляра). Размер хранилища).

Инструкции по **решению**: запустите `ALTER RESOURCE GOVERNOR RECONFIGURE` периодически или в рамках задания агента SQL Server, которое ВЫПОЛНЯЕТ задачу SQL при запуске экземпляра, если используется [Resource Governor](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>После обновления уровня службы необходимо повторно инициализировать диалоговые окна межбазовых Service Broker

**Дата:** Авг 2019

Межбазовые Service Broker диалоговые окна перестают предоставлять сообщения службам в других базах данных после изменения уровня служб. Сообщения **не теряются** , и их можно найти в очереди отправителя. Любое изменение размера хранилища виртуальных ядер или экземпляра в Управляемый экземпляр приведет к изменению значения `service_broke_guid` в представлении [sys. databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) для всех баз данных. Все `DIALOG`, созданные с помощью инструкции [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) , которая ссылается на брокеры служб в другой базе данных, прекратят доставку сообщений целевой службе.

**Обходной путь:** Перед обновлением уровня службы и повторной их инициализацией после этого отключите все действия, использующие диалоговые окна межбазовых Service Broker. Если остались сообщения, которые не доставляются после изменения уровня службы, прочтите сообщения из очереди источника и повторно отправьте их в целевую очередь.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Имперсонификатион типов входа Azure AD не поддерживается

**Дата:** 2019 июля

Олицетворение с использованием `EXECUTE AS USER` или `EXECUTE AS LOGIN` следующих субъектов AAD не поддерживается:
-   Пользователи AAD с псевдонимами. В этом случае возвращается следующая ошибка `15517`.
- Имена входа и пользователей AAD, основанные на приложениях AAD или субъектах-службах. В этом случае возвращаются следующие ошибки `15517` и `15406`.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>параметр @query не поддерживается в sp_send_db_mail

**Дата:** Апрель 2019

Параметр `@query` в процедуре [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) не работает.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>После географической отработки отказа необходимо перенастроить репликацию транзакций

**Дата:** Мар 2019

Если репликация транзакций включена для базы данных в группе автоматической отработки отказа, администратор управляемого экземпляра должен очистить все публикации на старом первичном ресурсе и перенастроить их на новом первичном ресурсе после отработки отказа в другой регион. Дополнительные сведения см. в разделе [репликация](#replication) .

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>Имена входа и пользователи AAD не поддерживаются в SSDT

**Дата:** 2019 ноября

SQL Server Data Tools не полностью поддерживают имена входа и пользователей Azure Active Directory.

### <a name="temporary-database-is-used-during-restore-operation"></a>Во время операции восстановления используется временная база данных

При восстановлении базы данных в Управляемый экземпляр Служба восстановления сначала создаст пустую базу данных с нужным именем, чтобы выделить имя экземпляра. Через некоторое время эта база данных будет удалена и восстановлена фактическая база данных. База данных, которая находится в состоянии *восстановления* , будет иметь случайное значение идентификатора GUID вместо имени. После завершения процесса восстановления временное имя будет изменено на нужное имя, указанное в `RESTORE` инструкции. На начальном этапе пользователь может получить доступ к пустой базе данных и даже создать таблицы или загрузить данные в эту базу данных. Эта временная база данных будет удалена, когда служба восстановления запустит второй этап.

Инструкции по решению: не **пытайтесь**получить доступ к восстанавливаемой базе данных до тех пор, пока не будет показано, что восстановление завершено.

### <a name="tempdb-structure-and-content-is-re-created"></a>Структура и содержимое базы данных TEMPDB создано повторно

`tempdb` база данных всегда разделяется на 12 файлов данных, и структура файла не может быть изменена. Невозможно изменить максимальный размер на файл, и новые файлы нельзя добавить в `tempdb`. `Tempdb` всегда создается повторно как пустая база данных при запуске или отработки отказа экземпляра, а любые изменения, внесенные в `tempdb`, не сохраняются.

### <a name="exceeding-storage-space-with-small-database-files"></a>Превышение дискового пространства с небольшими файлами баз данных

инструкции `CREATE DATABASE`, `ALTER DATABASE ADD FILE`и `RESTORE DATABASE` могут завершиться ошибкой, так как экземпляр может достигнуть ограничения хранилища Azure.

Каждый управляемый экземпляр общего назначения имеет до 35 ТБ хранилища, зарезервированного для дискового пространства Azure уровня "Премиум". Каждый файл базы данных размещается на отдельном физическом диске. Поддерживаются диски размером 128 ГБ, 256 ГБ, 512 ГБ, 1 ТБ или 4 ТБ. Неиспользуемое пространство на диске не заряжено, но общая сумма размеров дисков Azure уровня "Премиум" не может превышать 35 ТБ. В некоторых случаях управляемый экземпляр, для которого не требуется 8 ТБ, может превысить ограничение в 35 ТБ на размер хранилища из-за внутренней фрагментации.

Например, управляемый экземпляр общего назначения может иметь один большой файл размером 1,2 ТБ, размещенный на диске объемом 4 ТБ. У него также может быть 248 файлов с размером 1 ГБ, каждый из которых размещается на отдельных дисках емкостью 128 ГБ. В данном примере:

- общий размер выделенного дискового хранилища составляет 1 x 4 ТБ + 248 x 128 ГБ = 35 ТБ;
- общий объем зарезервированного пространства для баз данных в экземпляре составляет 1 x 1,2 ТБ + 248 x 1 ГБ = 1,4 ТБ.

В этом примере показано, что в некоторых обстоятельствах при определенных распределениях файлов управляемый экземпляр может достичь предельного размера в 35 ТБ, зарезервированного для подключенного диска Azure уровня "Премиум", если вы не ожидаете его.

В этом примере существующие базы данных продолжают работать и могут увеличиваться без каких бы то ни было проблем, пока новые файлы не добавляются. Новые базы данных не могут быть созданы или восстановлены, так как не хватает места для новых дисков, даже если общий размер всех баз данных не достигает предельного размера экземпляра. Ошибка, возвращаемая в этом случае, не ясно.

[Количество оставшихся файлов можно узнать](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) с помощью системных представлений. Если вы достигли этого ограничения, попытайтесь [удалить некоторые файлы меньшего размера с помощью инструкции DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) или переключитесь на [уровень критически важный для бизнеса, который не имеет этого ограничения](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Значения GUID, отображаемые вместо имен баз данных

Несколько системных представлений, счетчиков производительности, сообщений об ошибках, XEvents и записей в журнале ошибок отображают идентификаторы базы данных GUID вместо фактических имен базы данных. Не полагайтесь на эти идентификаторы GUID, так как они заменяются фактическими именами баз данных в будущем.

### <a name="error-logs-arent-persisted"></a>Журналы ошибок не сохраняются

Журналы ошибок, доступные в управляемом экземпляре, не сохраняются, и их размер не включается в максимальный предел хранилища. Если происходит отработка отказа, журналы ошибок могут автоматически удаляться. В журнале ошибок могут содержаться пробелы, так как Управляемый экземпляр были перемещены несколько раз на несколько виртуальных машин.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Область транзакций в двух базах данных в одном и том же экземпляре не поддерживается

Класс `TransactionScope` в .NET не работает, если два запроса отправляются в две базы данных в одном экземпляре в одной и той же области транзакций:

```csharp
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

Несмотря на то, что этот код работает с данными в одном и том же экземпляре, требуется MSDTC.

**Обходной путь:** Используйте [SqlConnection. чанжедатабасе (String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) для использования другой базы данных в контексте соединения вместо двух соединений.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Модули CLR и связанные серверы иногда не могут ссылаться на локальный IP-адрес.

Модули CLR, размещенные в управляемом экземпляре, а также на связанных серверах или распределенных запросах, которые ссылаются на текущий экземпляр, иногда не могут разрешить IP-адрес локального экземпляра. Это временная ошибка.

**Обходной путь:** По возможности используйте контекстные соединения в модуле CLR.

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения об управляемых экземплярах см [. в разделе понятие управляемого экземпляра.](sql-database-managed-instance.md)
- Список функций и сравнительных списков см. в статье [Сравнение функций базы данных SQL Azure](sql-database-features.md).
- Краткое руководство, в котором показано, как создать новый управляемый экземпляр, см. в разделе [Создание управляемого экземпляра](sql-database-managed-instance-get-started.md).
