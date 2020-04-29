---
title: Защита базы данных
description: Советы по обеспечению безопасности базы данных и разработке решений в ресурсе пула SQL синапсе.
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 27d3a242d91a79ea00974748f4a8b5460d2dd247
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416054"
---
# <a name="secure-a-database-in-azure-synapse"></a>Защита базы данных в Azure синапсе

> [!div class="op_single_selector"]
>
> * [Обзор безопасности](sql-data-warehouse-overview-manage-security.md)
> * [Аутентификация](sql-data-warehouse-authentication.md)
> * [Шифрование (портал)](sql-data-warehouse-encryption-tde.md)
> * [Шифрование (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

В этой статье рассматриваются основы защиты пула SQL синапсе. В частности, эта статья посвящена началу работы с ресурсами для ограничения доступа, защиты данных и мониторинга действий в базе данных, подготовленной с помощью пула SQL.

## <a name="connection-security"></a>Безопасность подключения

Под безопасностью подключения подразумеваются способы ограничения и защиты подключений к базе данных с помощью правил брандмауэра и шифрования подключения.

Правила брандмауэра используются как сервером, так и базой данных для отклонения попыток подключения с IP-адресов, которые не были явно список разрешений. Чтобы разрешить подключение из своего приложения или с клиентского компьютера с общедоступным IP-адресом, сначала необходимо создать правило брандмауэра уровня сервера. Для этого воспользуйтесь порталом Azure, REST API или PowerShell.

В брандмауэре сервера рекомендуется максимально ограничить диапазоны разрешенных IP-адресов.  Чтобы получить доступ к пулу SQL с локального компьютера, убедитесь, что брандмауэр в сети и локальный компьютер допускают исходящие подключения через TCP-порт 1433.  

Azure синапсе Analytics использует правила брандмауэра IP на уровне сервера. Правила брандмауэра для IP-адресов уровня базы данных не поддерживаются. Дополнительные сведения см. в статье [правила брандмауэра базы данных SQL Azure](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

Подключения к пулу SQL шифруются по умолчанию.  Изменение параметров подключения для отключения шифрования игнорируется.

## <a name="authentication"></a>Проверка подлинности

В процессе аутентификации предлагается подтвердить личность пользователя при подключении к базе данных. В настоящее время пул SQL поддерживает SQL Server проверку подлинности с помощью имени пользователя и пароля, а также с Azure Active Directory.

При создании логического сервера для базы данных вы указали имя для входа "Администратор сервера" и пароль. Используя эти учетные данные, можно выполнить проверку подлинности SQL Server для любой базы данных на этом сервере в качестве владельца базы данных (или dbo).

Однако рекомендуется, чтобы пользователи вашей организации использовали для проверки подлинности другую учетную запись. Это позволит ограничить разрешения, предоставляемые приложению, и снизить риски вредоносных действий в случае, если приложение уязвимо для атак путем внедрения кода SQL.

Чтобы создать пользователя, прошедшего аутентификацию SQL Server, подключитесь к базе данных **master** на сервере с именем для входа администратора сервера и создайте новое имя для входа на сервер.  Рекомендуется также создать пользователя в базе данных master. Это позволит пользователям входить в систему с помощью таких инструментов, как среда SSMS, без указания имени базы данных.  Кроме того, это позволяет использовать обозреватель объектов для просмотра всех баз данных в SQL Server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Затем подключитесь к **базе данных пула SQL** с именем для входа администратора сервера и создайте пользователя базы данных на основе созданного имени входа сервера.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Чтобы предоставить пользователю разрешение для выполнения дополнительных операций, таких как создание имен для входа или баз данных, назначьте ему роли `Loginmanager` и `dbmanager` в базе данных master.

Дополнительные сведения об этих дополнительных ролях и аутентификации в базе данных SQL см. в статье [Проверка подлинности и авторизация в Базе данных SQL Azure: предоставление доступа](../../sql-database/sql-database-manage-logins.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Дополнительные сведения о подключении с помощью Azure Active Directory см. в разделе [подключение с использованием Azure Active Directory проверки подлинности](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Авторизация

Авторизация — это то, что можно сделать в базе данных после проверки подлинности и подключения. Привилегии авторизации определяются членством в ролях и разрешениями. Обычно пользователям рекомендуется предоставлять наименьшие необходимые привилегии. Для управления ролями можно использовать следующие хранимые процедуры.

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Учетной записи администратора сервера, которая используется для подключения, присвоена роль db_owner, обладатель которой может выполнять любые действия в базе данных. Сохраните эту учетную запись для развертывания обновлений схемы и выполнения других действий по управлению. Для подключения к базе данных из приложения с использованием наименьших привилегий, необходимых приложению, используйте учетную запись "ApplicationUser" с более ограниченными разрешениями.

Существуют способы еще больше ограничить возможности пользователя в базе данных.

* Детальная настройка [разрешений](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) позволяет указать операции, которые можно выполнять с отдельными столбцами, таблицами, представлениями, схемами, процедурами и другими объектами в базе данных. Используйте детализированные разрешения для обеспечения наивысшего уровня контроля и предоставления минимальных необходимых разрешений.
* Для создания учетных записей пользователей приложения с более широкими возможностями или учетных записей для управления с меньшим набором возможностей можно использовать [роли базы данных](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), отличные от db_datareader и db_datawriter. С помощью встроенных фиксированных ролей базы данных можно легко присваивать разрешения, однако это может привести к предоставлению лишних разрешений.
* [хранимые процедуры](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .

В следующем примере предоставляется доступ для чтения к определяемой пользователем схеме.

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Управление базами данных и логическими серверами на портале Azure или с помощью API Azure Resource Manager контролируется путем назначения ролей для учетной записи пользователя портала. Дополнительные сведения см. в статье [Использование управления доступом на основе ролей для контроля доступа к ресурсам в подписке Azure](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="encryption"></a>Encryption

Прозрачное шифрование данных (TDE) помогает защититься от угроз вредоносных действий путем шифрования и расшифровки неактивных данных. При шифровании базы связанные резервные копии и файлы журналов транзакций шифруются без необходимости вносить изменения в приложения. При использовании TDE хранилище всей базы данных шифруется с помощью симметричного ключа, который называется ключом шифрования базы данных.

В базе данных SQL ключ шифрования базы данных защищается встроенным сертификатом сервера. Каждый сервер Базы данных SQL обладает уникальным встроенным сертификатом. Майкрософт автоматически меняет эти сертификаты каждые 90 дней. Используется алгоритм шифрования AES-256. Общее описание функции TDE см. в статье [Прозрачное шифрование данных (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Базу данных можно зашифровать с помощью [портала Azure](sql-data-warehouse-encryption-tde.md) или [T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения и примеры подключения к хранилищу с различными протоколами см. в разделе [Подключение к пулу SQL](../sql/connect-overview.md).
