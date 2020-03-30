---
title: Различия управляемого экземпляра T-S'L
description: В этой статье обсуждаются различия T-SQL между управляемым экземпляром в Базе данных SQL Azure и SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova, danil
ms.date: 03/11/2020
ms.custom: seoapril2019
ms.openlocfilehash: e01c61ca4f415ffbb46c86034d4b7441bc2617d9
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365480"
---
# <a name="managed-instance-t-sql-differences-and-limitations"></a>Управляемый экземпляр T-S'L различия и ограничения

В этой статье кратко излагаются и разъясняются различия в синтаксисе и поведении между управляемым экземпляром базы данных Azure S'L и натерритории S'L Server Database Engine. Вариант развертывания в виде управляемого экземпляра обеспечивает высокий уровень совместимости с локальным ядром СУБД SQL Server. В управляемом экземпляре поддерживается большинство функций ядра СУБД SQL Server.

![Миграция](./media/sql-database-managed-instance/migration.png)

Есть некоторые ограничения PaaS, которые вводятся в Управляемой инстанции и некоторые изменения поведения по сравнению с сервером S'L. Различия делятся на следующие категории:<a name="Differences"></a>

- [Доступность](#availability) включает в себя различия в [всегда на группы доступности](#always-on-availability-groups) и [резервных копированиях.](#backup)
- [Безопасность](#security) включает в себя различия в [аудите,](#auditing) [сертификаты,](#certificates) [учетные данные,](#credential) [криптографические провайдеры,](#cryptographic-providers) [логины и пользователи,](#logins-and-users)а также [ключ службы и ключ мастера службы.](#service-key-and-service-master-key)
- [Конфигурация](#configuration) включает в себя различия в [расширении буферного пула,](#buffer-pool-extension) [сопоставлении,](#collation) [уровнях совместимости,](#compatibility-levels) [зеркальном отражении базы данных,](#database-mirroring) [вариантах базы данных,](#database-options) [серверном агенте S'L](#sql-server-agent)и [опциях таблицы.](#tables)
- [Функциональность](#functionalities) включает [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [распределенные транзакции](#distributed-transactions), [расширенные события](#extended-events), [внешние библиотеки,](#external-libraries) [filestream и FileTable](#filestream-and-filetable), [полный текст Semantic Search](#full-text-semantic-search), [связанные серверы](#linked-servers), [PolyBase](#polybase), [Репликация](#replication), [RESTORE](#restore-statement), [Сервисный брокер](#service-broker), [сохраненные процедуры, функции и триггеры](#stored-procedures-functions-and-triggers).
- [Настройки среды,](#Environment) такие как VNets и конфигурации подсетей.

Большинство из этих функций являются архитектурными ограничениями и представляют функции обслуживания.

Временные известные проблемы, обнаруженные в управляемом экземпляре и которые будут решены в будущем, описаны на [странице примечаний к выпуску.](sql-database-release-notes.md)

## <a name="availability"></a>Доступность

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Группы доступности AlwaysOn

[Высокая доступность](sql-database-high-availability.md) встроена в управляемый экземпляр и не может контролироваться пользователями. Следующие заявления не поддерживаются:

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql);
- [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- [Пункт SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) заявления [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Резервное копирование

Управляемые экземпляры имеют автоматическое резервное копирование, поэтому пользователи могут создавать полные резервные данные базы данных. `COPY_ONLY` Дифференциал, резервные копии моментального снимка файлов не поддерживаются.

- В управляемом экземпляре можно создать резервную копию базы данных экземпляров только в учетную запись хранения Azure Blob:
  - Поддерживается только `BACKUP TO URL`.
  - `FILE`, `TAPE`и устройства резервного копирования не поддерживаются.
- Большинство общих `WITH` вариантов поддерживается.
  - `COPY_ONLY`является обязательным.
  - `FILE_SNAPSHOT` не поддерживается.
  - Параметры ленты: `REWIND`, `NOUNLOAD` `NOREWIND` `UNLOAD`и не поддерживаются.
  - Параметры входа: `NORECOVERY` `STANDBY`и `NO_TRUNCATE` не поддерживаются.

Ограничения: 

- В управляемом экземпляре можно создать резервную резервную базу данных экземпляров для резервного копирования с 32 полосами, что достаточно для баз данных до 4 ТБ, если используется сжатие резервного копирования.
- Вы не можете `BACKUP DATABASE ... WITH COPY_ONLY` выполнить в базе данных, которая зашифрована с помощью управляемого сервисом прозрачного шифрования данных (TDE). Управляемый сервисом TDE заставляет резервные копирования шифроваться с помощью внутреннего ключа TDE. Ключ не может быть экспортирован, поэтому вы не можете восстановить резервную поддержку. Используйте автоматические резервные и временные восстановления, или вместо этого используйте [TDE, управляемый клиентом (BYOK).](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key) Вы также можете отключить шифрование в базе данных.
- Максимальный размер полосы резервного `BACKUP` копирования с помощью команды в управляемом экземпляре составляет 195 ГБ, что является максимальным размером капли. Чтобы уменьшить размер отдельного чередующегося набора и соблюсти это ограничение, можно увеличить число чередующихся наборов в команде резервного копирования.

    > [!TIP]
    > Для решения этого ограничения при резервном копировании базы данных с сервера s'L в предварительной среде или в виртуальной машине можно:
    >
    > - Резервное `DISK` копирование вместо `URL`резервного копирования до .
    > - Загрузите файлы резервного копирования в хранилище Blob.
    > - Восстановление в управляемом экземпляре.
    >
    > Команда `Restore` в управляемом экземпляре поддерживает большие размеры капли в файлах резервного копирования, поскольку для хранения загруженных файлов резервного копирования используется другой тип капли.

Сведения о резервном копировании с помощью T-SQL см. в статье [BACKUP (Transact-SQL)](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Безопасность

### <a name="auditing"></a>Аудит

Ниже перечислены основные различия между аудитом в базах данных в Базе данных SQL Azure и базах данных в SQL Server.

- С помощью управляемого варианта развертывания экземпляров в базе данных Azure S'L аудит работает на уровне сервера. Файлы `.xel` журнала хранятся в хранилище Azure Blob.
- При использовании варианта развертывания в виде отдельной базы данных и эластичного пула в Базе данных SQL Azure аудит работает на уровне базы данных.
- Аудит работает на серверном уровне в помещениях или виртуальных машинах. События хранятся в файловых системах или журналах событий Windows.
 
Аудит XEvent в управляемом экземпляре поддерживает целевые объекты хранилища BLOB-объектов Azure. Файлы и журналы Windows не поддерживаются.

Основные различия в синтаксисе `CREATE AUDIT` для аудита в хранилище BLOB-объектов Azure:

- Новый синтаксис `TO URL` предусмотрен, что можно использовать для указания URL-адреса `.xel` контейнера для хранения Azure Blob, в котором размещаются файлы.
- Синтаксис `TO FILE` не поддерживается из-за того, что управляемый экземпляр не может получить доступ к файлам Windows.

Дополнительные сведения см. в разделе: 

- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Аудит](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Сертификаты

Управляемый экземпляр не может получить доступ к файлам и папкам Windows, поэтому применяются следующие ограничения:

- `CREATE FROM` / Файл `BACKUP TO` не поддерживается для сертификатов.
- `CREATE` / Сертификат `BACKUP` `FILE` от / не поддерживается. `ASSEMBLY` Невозможно использовать файлы закрытых ключей. 

См. статьи [Инструкция CREATE CERTIFICATE (Transact-SQL)](/sql/t-sql/statements/create-certificate-transact-sql) и [BACKUP CERTIFICATE (Transact-SQL)](/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Обход**: Вместо создания резервного копирования сертификата и восстановления резервного [копирования, получить двоичный контент сертификата и частный ключ, хранить его как файл .sql, и создавать из двоичного](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database):

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Учетные данные

Поддерживаются удостоверения только Azure Key Vault и `SHARED ACCESS SIGNATURE`. Пользователи Windows не поддерживаются.

См. статьи [CREATE CREDENTIAL (Transact-SQL)](/sql/t-sql/statements/create-credential-transact-sql) и [ALTER CREDENTIAL (Transact-SQL)](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Поставщики служб шифрования

Управляемый экземпляр не может получить доступ к файлам, поэтому криптографические провайдеры не могут быть созданы:

- `CREATE CRYPTOGRAPHIC PROVIDER` не поддерживается. См. статью [CREATE CRYPTOGRAPHIC PROVIDER (Transact-SQL)](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` не поддерживается. См. статью [ALTER CRYPTOGRAPHIC PROVIDER (Transact-SQL)](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Имена входа и пользователи

- Логины, созданные с `FROM CERTIFICATE` `FROM ASYMMETRIC KEY`помощью `FROM SID` и миноносной системы, поддерживаются. См. статью [CREATE LOGIN (Transact-SQL)](/sql/t-sql/statements/create-login-transact-sql).
- Поддерживаются принципы серверов Active Directory (Azure AD), созданные с помощью синтаксиса [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) или синтаксиса [CREATE USER FROM LOGIN «Azure AD Login».](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) Эти логины создаются на уровне сервера.

    Управляемый экземпляр поддерживает принципы базы данных `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`Azure AD с помощью синтаксиса. Эта функция также известна как Azure AD, содержащиепользователей баз данных.

- Логины Windows, созданные с помощью `CREATE LOGIN ... FROM WINDOWS` синтаксиса, не поддерживаются. Используйте пользователей и имена для входа Azure Active Directory.
- Пользователь Azure AD, создавший экземпляр, имеет [неограниченные привилегии админ.](sql-database-manage-logins.md)
- Пользователи базы данных уровня Azure AD могут `CREATE USER ... FROM EXTERNAL PROVIDER` быть созданы с помощью синтаксиса. Смотрите [CREATE USER ... ОТ ВНЕШНЕГО ПОСТАВЩИКА](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
- Принципы серверов Azure AD (логины) поддерживают функции S'L только в одном управляемом экземпляре. Функции, требующие взаимодействия между экземплярами, независимо от того, находятся ли они в пределах одного и того же арендатора Azure AD или разных арендаторов, не поддерживаются для пользователей Azure AD. Ниже приведены примеры таких функций:

  - Репликация транзакций S'L.
  - Ссылка сервера.

- Установка имени для входа Azure AD, сопоставленного с группой Azure AD, в качестве владельца базы данных, не поддерживается.
- Поддержка олицетворения принципов сервера Azure AD с помощью других принципов Azure AD поддерживается, например, оговорка [EXECUTE AS.](/sql/t-sql/statements/execute-as-transact-sql) EXECUTE AS ограничения:

  - EXECUTE AS USER не поддерживается для пользователей Azure AD, когда имя отличается от имени входа. Например, когда пользователь создается через синтаксис CREATE USER (myAadUser) ОТ LOGINjohn@contoso.comи олицетворение пытается через EXEC AS USER - _myAadUser_. При создании **USER** из основного сервера Azure AD (логин) укажите user_name как тот же login_name из **LOGIN.**
  - Только принципы уровня сервера S'L, которые являются `sysadmin` частью роли, могут выполнять следующие операции, ориентированные на принципы Azure AD:

    - EXECUTE AS USER;
    - EXECUTE AS LOGIN.

- Экспорт/импорт баз данных с использованием файлов bacpac поддерживается для пользователей Azure AD в управляемом экземпляре, используя либо [SSMS V18.4, либо позже,](/sql/ssms/download-sql-server-management-studio-ssms)либо [s'LPackage.exe.](/sql/tools/sqlpackage-download)
  - Следующие конфигурации поддерживаются с помощью файла bacpac базы данных: 
    - Экспорт/импорт базы данных между различными экземплярами управления в одном домене Azure AD.
    - Экспорт базы данных из управляемого экземпляра и импорт в базу данных S'L в рамках того же домена Azure AD. 
    - Экспорт базы данных из базы данных S'L и импортируйте в управляемый экземпляр в том же домене Azure AD.
    - Экспорт базы данных из управляемого экземпляра и импорт на сервер S'L (версия 2012 или более поздняя).
      - В этой конфигурации все пользователи Azure AD создаются в качестве основ данных (пользователей) без логинов. Тип пользователей указан в виде S'L (видимом как SQL_USER в sys.database_principals). Их разрешения и роли остаются в метаданных базы данных S'L Server и могут быть использованы для олицетворения. Тем не менее, они не могут быть использованы для доступа и входа в систему с помощью своих учетных данных.

- Только основной логин на уровне сервера, созданный в процессе подготовки управляемого `securityadmin` `sysadmin`экземпляра, члены ролей сервера, такие как или, или другие логины с разрешением ALTER ANY LOGIN на уровне сервера, могут создавать принципы сервера Azure AD (логины) в основной базе данных, например.
- Если логин является принципом S'L, только логины, которые являются частью `sysadmin` роли, могут использовать команду создания для создания логинов для учетной записи Azure AD.
- Логин Azure AD должен быть членом Azure AD в том же каталоге, который используется для управляемого экземпляра базы данных Azure S'L.
- Принципы серверов Azure AD (логины) видны в Object Explorer, начиная с S'L Server Management Studio 18.0 Preview 5.
- Допускается перекрытие субъектов сервера (имен для входа) Azure AD с учетной записью администратора Azure AD. Принципы сервера AD Azure AD (логины) имеют приоритет над администратором Azure AD при решении основного и применении разрешений к управляемому экземпляру.
- При проверке подлинности для устранения принципапроверки применяется следующая последовательность:

    1. Если учетная запись Azure AD существует как непосредственно отображенный к главному серверу Azure AD (логин), который присутствует в sys.server_principals как тип "E", предоставите доступ и примените разрешения главного сервера Azure AD (логин).
    2. Если учетная запись Azure AD является членом группы Azure AD, которая отображается на главном сервере AD Azure (логин), который присутствует в sys.server_principals как тип "X", предоставите доступ и примените разрешения на логин группы Azure AD.
    3. Если учетная запись Azure AD — это специальный портал, настроенный на azure AD для управляемого экземпляра, который не существует в управляемых представлениях системы экземпляров, примените специальные фиксированные разрешения админа Azure AD для управляемого экземпляра (устаревший режим).
    4. Если учетная запись Azure AD существует как непосредственно отображенный для пользователя Azure AD в базе данных, которая присутствует в sys.database_principals как тип "E", предоставите доступ и примените разрешения пользователя базы данных Azure AD.
    5. Если учетная запись Azure AD является членом группы Azure AD, которая отображается для пользователя Azure AD в базе данных, которая присутствует в sys.database_principals как тип "X", предоставите доступ и примените разрешения на логин группы Azure AD.
    6. Если есть логин Azure AD, отображаемый либо с учетной записью пользователя Azure AD, либо с учетной записью группы Azure AD, которая разрешает сядее к пользователю, который проверяет подлинность, применяются все разрешения от этого входа Azure AD.

### <a name="service-key-and-service-master-key"></a>Ключ службы и главный ключ службы

- [Резервное копирование ключа Master](/sql/t-sql/statements/backup-master-key-transact-sql) не поддерживается (управляется службой базы данных S'L).
- [Восстановление ключа Master](/sql/t-sql/statements/restore-master-key-transact-sql) не поддерживается (управляется службой базы данных S'L).
- [Резервное копирование ключа s-службы не](/sql/t-sql/statements/backup-service-master-key-transact-sql) поддерживается (управляется службой базы данных S'L).
- [Восстановление ключа обслуживания](/sql/t-sql/statements/restore-service-master-key-transact-sql) не поддерживается (управляется службой базы данных S'L).

## <a name="configuration"></a>Параметр Configuration

### <a name="buffer-pool-extension"></a>Расширение буферного пула

- [Расширение пула буфера](/sql/database-engine/configure-windows/buffer-pool-extension) не поддерживается.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` не поддерживается. См. статью [ALTER SERVER CONFIGURATION (Transact-SQL)](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Параметры сортировки

Параметр сортировки экземпляра по умолчанию — `SQL_Latin1_General_CP1_CI_AS`. Этот параметр можно указать как параметр создания. См. статью [Параметры сортировки](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Уровни совместимости

- Поддерживаемые уровни совместимости 100, 110, 120, 130, 140 и 150.
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
- В экземпляре общего назначения имеется ограничение в 280 файлов, что подразумевает максимум 280 файлов на одну базу данных. К этому пределу учитываются как файлы данных, так и файлы журнала в ярусе общего назначения. [Бизнес критический уровень поддерживает 32 767 файлов в базе данных.](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)
- База данных не может содержать группы файлов, содержащих данные файлов. Восстановление завершается неудачей, если .bak содержит `FILESTREAM` данные. 
- Каждый файл помещается в хранилище BLOB-объектов Azure. Операции ввода-вывода и пропускная способность каждого файла зависят от размера каждого файла.

#### <a name="create-database-statement"></a>Инструкция CREATE DATABASE

Следующие ограничения применяются к: `CREATE DATABASE`

- Невозможно определить файлы и файловые группы. 
- Опция `CONTAINMENT` не поддерживается. 
- `WITH`варианты не поддерживаются. 
   > [!TIP]
   > В качестве обходного `ALTER DATABASE` `CREATE DATABASE` пути используйте после установки параметров базы данных для добавления файлов или установки сдерживания. 

- Опция `FOR ATTACH` не поддерживается.
- Опция `AS SNAPSHOT OF` не поддерживается.

Дополнительные сведения см. в статье [CREATE DATABASE (SQL Server Transact-SQL)](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Инструкция ALTER DATABASE

Невозможно задать или изменить некоторые свойства файла:

- Путь файла не может быть `ALTER DATABASE ADD FILE (FILENAME='path')` указан в выписке t-S'SL. Удалите `FILENAME` из сценария, так как управляемый экземпляр автоматически помещает файлы. 
- Имя файла не может быть `ALTER DATABASE` изменено с помощью оператора.

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

### <a name="sql-server-agent"></a>Агент SQL Server

- Включение и отключение серверного агента S'L в настоящее время не поддерживается в управляемом экземпляре. Агент SQL работает всегда.
- Параметры серверного агента ссчитываются только. Процедура `sp_set_agent_properties` не поддерживается в управляемом экземпляре. 
- Задания
  - Шаги задания T-SQL поддерживаются.
  - Поддерживаются следующие задания репликации:
    - Читатель журнала транзакций.
    - Моментальный снимок
    - Распространитель
  - Поддержка рабочих шагов SSIS.
  - Другие типы работ ы не поддерживаются в настоящее время:
    - The merge replication job step isn't supported. 
    - Читатель очереди пока не поддерживается. 
    - Командная оболочка еще не поддерживается.
  - Управляемые экземпляры не могут получить доступ к внешним ресурсам, например, сетевые акции с помощью робокопии. 
  - Службы анализа серверов s'L не поддерживаются.
- Уведомления поддерживаются частично.
- Уведомление по электронной почте поддерживается, хотя требует, чтобы вы настроить профиль почты базы данных. Серверный агент S'L может использовать только один `AzureManagedInstance_dbmail_profile`профиль почты базы данных, и он должен быть вызван. 
  - Пейджер не поддерживается.
  - NetSend не поддерживается.
  - Оповещения еще не поддерживаются.
  - Прокси не поддерживаются.
- EventLog не поддерживается.

Следующие функции агента S'L в настоящее время не поддерживаются:

- прокси-серверы;
- Планирование заданий на холостом ходу процессора
- Включение или отключение агента
- видны узлы

Сведения об агенте SQL Server см. в статье [Агент SQL Server](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Таблицы

Следующие типы таблиц не поддерживаются:

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [Filetable](/sql/relational-databases/blob/filetables-sql-server)
- [EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) (Полибаза)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (не поддерживается только на уровне общего назначения)

Для получения информации о том, как [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql)создавать и изменять таблицы, [см.](/sql/t-sql/statements/create-table-transact-sql)

## <a name="functionalities"></a>Функциональные возможности

### <a name="bulk-insert--openrowset"></a>Массовая вставка / OPENROWSET

Управляемый экземпляр не может получить доступ к файлам и папкам Windows, поэтому файлы должны быть импортированы из хранилища Azure Blob:

- `DATASOURCE`требуется в `BULK INSERT` команде при импорте файлов из хранилища Azure Blob. См. статью [BULK INSERT (Transact-SQL)](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE`требуется в `OPENROWSET` функции при прочтении содержимого файла из хранилища Azure Blob. См. статью [OPENROWSET (Transact-SQL)](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET`могут использоваться для чтения данных из других баз данных Azure S'L, управляемых экземпляров или экземпляров сервера S'L. Другие источники, такие как базы данных Oracle или файлы Excel, не поддерживаются.

### <a name="clr"></a>CLR

Управляемый экземпляр не может получить доступ к файлам и папкам Windows, поэтому применяются следующие ограничения:

- Поддерживается только `CREATE ASSEMBLY FROM BINARY`. Смотрите [CREATE ASSEM BLY от BINARY](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` не поддерживается. Смотрите [CREATE ASSEMBLY FROM FILE](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` не может ссылаться на файлы. Смотрите [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Почта базы данных (db_mail)
 - `sp_send_dbmail`не может отправлять @file_attachments вложения с помощью параметра. Локальная файловая система и внешние акции или Хранилище Azure Blob недоступны для этой процедуры.
 - Просмотрите известные `@query` проблемы, связанные с параметром и аутентификацией.
 
### <a name="dbcc"></a>DBCC

Недокументированные операторы DBCC, включенные в S'L Server, не поддерживаются в управляемых экземплярах.

- Поддерживается лишь ограниченное число флагов Global Trace. Сессионный `Trace flags` уровень не поддерживается. Смотрите [флаги Trace](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) и [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) работают с ограниченным числом глобальных следовых флагов.
- [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) с опциями REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST и REPAIR_REBUILD не `SINGLE_USER` могут быть использованы, поскольку база данных не может быть установлена в режиме - см. [различия ALTER DATABASE.](#alter-database-statement) Потенциальные повреждения базы данных обрабатываются группой поддержки Azure. Если вы обратитесь в службу поддержки Azure, если вы обратите на это всучить повреждения базы данных, которые должны быть исправлены.

### <a name="distributed-transactions"></a>Распределенные транзакции

MSDTC и [упругие транзакции](sql-database-elastic-transactions-overview.md) в настоящее время не поддерживаются в управляемых экземплярах.

### <a name="extended-events"></a>Расширенные события

Некоторые целевые показатели для расширенных событий (XEvents) не поддерживаются:

- Цель `etw_classic_sync` не поддерживается. Храните `.xel` файлы в хранилище Azure Blob. См. раздел [Целевой объект etw_classic_sync_target](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Цель `event_file` не поддерживается. Храните `.xel` файлы в хранилище Azure Blob. См. раздел [Целевой объект event_file](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Внешние библиотеки

Внешние библиотеки в базе данных R и Python пока не поддерживаются. См. статью [Изучение служб машины SQL Server](/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>Filestream и FileTable

- Данные Filestream не поддерживаются.
- База данных не может `FILESTREAM` содержать файлы с данными.
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

### <a name="linked-servers"></a>Связанные серверы

Связанные службы в управляемых экземплярах поддерживают ограниченное число целевых объектов.

- Поддерживаемые цели — это управляемые экземпляры, единичные базы данных и экземпляры S'L Server. 
- Связанные серверы не поддерживают распределенные транзакции (MS DTC).
- Целями, которые не поддерживаются, являются файлами, аналитическими службами и другими RDBMS. Попробуйте использовать родной импорт CSV из `BULK INSERT` `OPENROWSET` Azure Blob Storage, используя или в качестве альтернативы для импорта файлов.

Операции

- Транзакции записи между несколькими экземплярами не поддерживаются.
- `sp_dropserver` поддерживается для удаления связанного сервера. См. статью [sp_dropserver (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- Функция `OPENROWSET` может использоваться для выполнения запросов только в экземплярах Сервера S'L. Ими можно управлять либо на территории, либо в виртуальных машинах. См. статью [OPENROWSET (Transact-SQL)](/sql/t-sql/functions/openrowset-transact-sql).
- Функция `OPENDATASOURCE` может использоваться для выполнения запросов только в экземплярах Сервера S'L. Ими можно управлять либо на территории, либо в виртуальных машинах. Только `SQLNCLI`, `SQLNCLI11`и `SQLOLEDB` значения поддерживаются в качестве поставщика. Например, `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. См. статью [OPENDATASOURCE (Transact-SQL)](/sql/t-sql/functions/opendatasource-transact-sql).
- Связанные серверы не могут использоваться для чтения файлов (Excel, CSV) из сетевых акций. Попробуйте использовать [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) или [OPENROWSET,](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) которые считывают файлы CSV из Azure Blob Storage. Отслеживайте эти запросы по [элементу управляемой обратной связи экземпляра](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Внешние таблицы, отопомающиеся на файлах в хранилище HDFS или Azure Blob, не поддерживаются. Для получения информации о PolyBase, [см.](/sql/relational-databases/polybase/polybase-guide)

### <a name="replication"></a>Репликация

- Поддерживаются типы моментальных снимков и би-направленных репликации. Репликация слияния, одноранговая репликация и подписка updatable не поддерживаются.
- [Транзакционная репликация](sql-database-managed-instance-transactional-replication.md) доступна для публичного предварительного просмотра в управляемом экземпляре с некоторыми ограничениями:
    - Все типы участников репликации (Издатель, Дистрибьютор, Pull Абонент, и Push Абонент) могут быть размещены на управляемых экземпляров, но издатель и дистрибьютор должен быть либо в облаке или как на месте.
    - Управляемые экземпляры могут связываться с последними версиями сервера S'L. Для получения дополнительной информации смотрите [матрицу поддерживаемых версий.](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems)
    - Транзакционная репликация имеет некоторые [дополнительные сетевые требования.](sql-database-managed-instance-transactional-replication.md#requirements)

Для получения дополнительной информации о настройке транзакционной репликации см.
- [Репликация между издателем MI и абонентом](replication-with-sql-database-managed-instance.md)
- [Репликация между издателем MI, дистрибьютором MI и абонентом сервера S'L](sql-database-managed-instance-configure-replication-tutorial.md)

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
  - `FROM URL`(Хранилище Azure Blob) является единственным поддерживаемым вариантом.
  - `FROM DISK`/`TAPE` или устройство резервного копирования не поддерживаются.
  - Резервные наборы данных не поддерживаются.
- `WITH`варианты не поддерживаются, `DIFFERENTIAL` такие `STATS`как нет или .
- `ASYNC RESTORE`: Восстановление продолжается, даже если подключение клиента прерывается. Если соединение удалено, можно `sys.dm_operation_status` проверить представление на состояние операции восстановления, а также базу данных CREATE и DROP. См. статью [sys.dm_operation_status (база данных SQL Azure)](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Следующие параметры базы данных устанавливаются или переопределяются и не могут быть изменены позже: 

- `NEW_BROKER`если брокер не включен в файл .bak. 
- `ENABLE_BROKER`если брокер не включен в файл .bak. 
- `AUTO_CLOSE=OFF`если база данных в `AUTO_CLOSE=ON`файле .bak имеет . 
- `RECOVERY FULL`если база данных в `SIMPLE` файле .bak имеет или `BULK_LOGGED` режим восстановления.
- Если она не была в файле .bak, то добавлена и вызвана XTP, если она не была в файле source .bak. 
- Любая существующая оптимизированная файловая группа памяти переименована в XTP. 
- `SINGLE_USER`и `RESTRICTED_USER` варианты `MULTI_USER`преобразуются в .

Ограничения: 

- Резервное копирование поврежденных баз данных может быть восстановлено в зависимости от типа повреждения, но автоматизированные резервные копирования не будут приниматься до тех пор, пока не будет исправлена коррупция. Убедитесь, что `DBCC CHECKDB` вы работаете на `WITH CHECKSUM` экземпляре исходного кода и используете резервную линию, чтобы предотвратить эту проблему.
- Восстановление `.BAK` файла базы данных, содержащей любые `FILESTREAM` ограничения, описанные в этом документе (например, или `FILETABLE` объекты), не может быть восстановлено в Управляемом экземпляре.
- `.BAK`файлы, содержащие несколько наборов резервного копирования, не могут быть восстановлены. 
- `.BAK`файлы, содержащие несколько файлов журнала, не могут быть восстановлены.
- Резервное копирование, содержащее базы данных размером более 8 ТБ, активные объекты OLTP-памяти в памяти или количество файлов, превышающее 280 файлов в экземпляре, не могут быть восстановлены в экземпляре общего назначения. 
- Резервное копирование, содержащее базы данных размером более 4 ТБ или объекты OLTP в памяти с общим размером, превышающее размер, описанный в [ограничениях ресурсов,](sql-database-managed-instance-resource-limits.md) не могут быть восстановлены в критических для бизнеса.
Для получения информации [RESTORE statements](/sql/t-sql/statements/restore-statements-transact-sql)о заявлениях о восстановлении см.

 > [!IMPORTANT]
 > Те же ограничения применяются к встроенной операции восстановления в точках времени. Например, база данных общего назначения, превышающее 4 ТБ, не может быть восстановлена в критических случаях бизнеса. Критическая база данных с файлами In-memory OLTP или более чем 280 файлами не может быть восстановлена в экземпляре общего назначения.

### <a name="service-broker"></a>Service Broker

Компонент Service Broker между экземплярами не поддерживается:

- `sys.routes`: В качестве предварительного условия необходимо выбрать адрес из sys.routes. Адрес должен быть LOCAL на каждом маршруте. См. статью о [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Вы не `CREATE ROUTE` можете `ADDRESS` использовать `LOCAL`с другими, чем . См. статью о [CREATE ROUTE](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Вы не `ALTER ROUTE` можете `ADDRESS` использовать `LOCAL`с другими, чем . См. статью об [ALTER ROUTE](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Сохраненные процедуры, функции и триггеры

- `NATIVE_COMPILATION`не поддерживается в уровне общего назначения.
- Следующие параметры [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) не поддерживаются: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` не поддерживается. См. раздел [Примеры](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` не поддерживается. См. раздел [xp_cmdshell (Transact-SQL)](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures`не поддерживаются, что `sp_addextendedproc`   `sp_dropextendedproc`включает в себя и . Смотрите [Расширенные сохраненные процедуры](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` и `sp_detach_db` не поддерживаются. См. статьи [sp_attach_db (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) и [sp_detach_db (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Системные функции и переменные

Следующие переменные, функции и представления возвращают различные результаты:

- `SERVERPROPERTY('EngineEdition')`возвращает значение 8. Это свойство уникально идентифицирует управляемый экземпляр. См. статью [SERVERPROPERTY (Transact-SQL)](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')`возвращает NULL, поскольку концепция экземпляра в том виде, в каком он существует для сервера S'L, не применяется к управляемому экземпляру. См. статью [SERVERPROPERTY (Transact-SQL)](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME`возвращает полное "подключаемое" имя DNS, например, my-managed-instance.wcus17662feb9ce98.database.windows.net. [См.@SERVERNAME](/sql/t-sql/functions/servername-transact-sql) 
- `SYS.SERVERS`возвращает полное "подключаемое" имя `myinstance.domain.database.windows.net` DNS, например, для свойств "имя" и "data_source". См. статью [sys.servers (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME`возвращает NULL, поскольку концепция обслуживания в том виде, в каком она существует для сервера S'L, не применяется к управляемому экземпляру. [См.@SERVICENAME](/sql/t-sql/functions/servicename-transact-sql)
- `SUSER_ID` поддерживается. Он возвращает NULL, если логин Azure AD не входит в sys.syslogins. См. статью [Идентификатор SUSER_ID (Transact-SQL)](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` не поддерживается. Неправильные данные возвращаются, что является временной известной проблемой. См. статью [SUSER_SID (Transact-SQL)](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Ограничения окружающей среды

### <a name="subnet"></a>Подсеть
-  Вы не можете разместить другие ресурсы (например, виртуальные машины) в подсети, где вы развернули управляемый экземпляр. Развертывайте эти ресурсы с помощью другой подсети.
- Subnet должен иметь достаточное количество доступных [IP-адресов.](sql-database-managed-instance-connectivity-architecture.md#network-requirements) Минимум 16, в то время как рекомендация состоит в том, чтобы иметь по крайней мере 32 IP-адреса в подсети.
- [Конечные точки обслуживания не могут быть связаны с подсетью управляемого экземпляра.](sql-database-managed-instance-connectivity-architecture.md#network-requirements) Убедитесь, что при создании виртуальной сети опция «конечные точки обслуживания» отключена.
- Количество vCores и типы экземпляров, которые можно развернуть в регионе, имеют некоторые [ограничения и ограничения.](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)
- Есть некоторые [правила безопасности, которые должны быть применены в подсети.](sql-database-managed-instance-connectivity-architecture.md#network-requirements)

### <a name="vnet"></a>Виртуальная сеть
- VNet может быть развернут с помощью модели ресурса - Классическая модель для VNet не поддерживается.
- После создания управляемого экземпляра перемещение управляемого экземпляра или VNet в другую группу ресурсов или подписку не поддерживается.
- Некоторые службы, такие как среды служб приложений, приложения логики и управляемые экземпляры (используемые для георепликации, транзакционной репликации или через связанные серверы) не могут получить доступ к управляемым экземплярам в разных регионах, если их VNets подключены с помощью [глобального пиринга.](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) Вы можете подключиться к этим ресурсам через ExpressRoute или VNet-to-VNet через шлюзы VNet.

### <a name="tempdb"></a>Базы данных tempdb

Максимальный размер `tempdb` файла не может быть больше 24 ГБ на ядро на уровне общего назначения. Максимальный `tempdb` размер на уровне «Бизнес критический» ограничен размером хранилища экземпляра. `Tempdb`размер файла журнала ограничен 120 ГБ на уровне General Purpose. Некоторые запросы могут вернуть ошибку, если им нужно `tempdb` более 24 ГБ на ядро или если они производят более 120 ГБ данных журнала.

### <a name="msdb"></a>MSDB

Следующие схемы MSDB в управляемом экземпляре должны принадлежать их соответствующим предопределенным ролям:

- Общие роли
  - ЦелеваяКансРол
- [Фиксированные роли базы данных](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [Роли DatabaseMail:](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile)
  - База данныхMailUserRole
- [Роли интеграционных услуг:](https://docs.microsoft.com/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15)
  - msdb
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> Изменение предопределенных имен ролей, имен схем и владельцев схем клиентами повлияет на нормальную работу службы. Любые изменения, внесенные в них, будут возвращены к предопределенным значениям, как только они будут обнаружены, или при следующем обновлении службы не позднее, чтобы обеспечить нормальную работу службы.

### <a name="error-logs"></a>Журналы ошибок

Управляемый экземпляр помещает многословную информацию в журналы ошибок. Есть много внутренних событий системы, которые вошли в журнал ошибок. Используйте пользовательскую процедуру для чтения журналов ошибок, которые отфильтровывает некоторые нерелевантные записи. Для получения дополнительной информации смотрите [управляемый экземпляр - sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) или [управляемый экземпляр расширения (предварительный просмотр)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) для Azure Data Studio.

## <a name="next-steps"></a>Дальнейшие действия

- Для получения дополнительной информации [What is a managed instance?](sql-database-managed-instance.md) об управляемых экземплярах см.
- Для сравнения функций и [Azure SQL Database feature comparison](sql-database-features.md)списка сравнений см.
- Для получения обновлений и [SQL Database release notes](sql-database-release-notes.md) состояния известных проблем см.
- Для быстрого запуска, который показывает, как создать новый управляемый экземпляр, [см.](sql-database-managed-instance-get-started.md)
