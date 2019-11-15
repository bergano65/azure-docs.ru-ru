---
title: Проблемы при подключении и использовании базы данных SQL Azure
description: Пошаговые инструкции по устранению неполадок подключения к базе данных SQL Azure и устранению других проблем, связанных с базой данных SQL
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019
author: v-miegge
ms.author: ramakoni
ms.reviewer: carlrab
ms.date: 11/14/2019
ms.openlocfilehash: 0bd018d90f4ca2c64df56d27eebdc6c9160309ac
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082406"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-microsoft-azure-sql-database"></a>Устранение проблем с подключением и другие ошибки с База данных SQL Microsoft Azure

При сбоях подключения к базе данных SQL Azure отображаются сообщения об ошибке. Эти проблемы с подключением могут быть вызваны переконфигурацией базы данных SQL Azure, параметрами брандмауэра, временем ожидания подключения или неправильными сведениями для входа. Кроме того, если достигнуто максимальное ограничение на некоторые ресурсы базы данных SQL Azure, вы не сможете подключиться к базе данных SQL Azure.

## <a name="transient-fault-error-messages"></a>Сообщения об ошибках временных сбоев

Инфраструктура Azure способна динамически изменять конфигурацию серверов при высокой рабочей нагрузке на службу баз данных SQL.  Такое динамическое поведение может привести к разрыву подключения между клиентской программой и базой данных SQL. Такое состояние называется *временной ошибкой*. Настоятельно рекомендуем включить в клиентской программе логику повторных попыток, чтобы программа смогла восстановить подключение после определенного периода, позволяющего временной ошибке самоустраниться.  Рекомендуется подождать 5 секунд, прежде чем выполнять первую повторную попытку. Повторная попытка после ожидания менее 5 секунд может привести к перегрузке облачной службы. Для каждой последующей повторной попытки ожидание должно увеличиваться экспоненциально, но не более чем до 60 секунд.

Примеры кода с логикой повторных попыток см. в следующих статьях:

* [Библиотеки подключений для базы данных SQL и SQL Server](sql-database-libraries.md)
* [Исправление ошибок подключения и временных ошибок в базе данных SQL](sql-database-connectivity-issues.md)

> [!TIP]
> Чтобы устранить проблемы, описанные в следующих разделах, выполните шаги (в указанном порядке) в разделе [действия по исправлению общих проблем с подключением](#steps-to-fix-common-connection-issues) .

### <a name="error-40613-database--x--on-server--y--is-not-currently-available"></a>Ошибка 40613: база данных < x > на сервере < y > сейчас недоступна

``40613: Database <DBname> on server < server name > is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of '< Tracing ID >'.``

Действия для устранения этой проблемы.

1. Проверьте [Microsoft Azure панели мониторинга службы](https://status.azure.com/status) на наличие известных простоев.
2. Если нет известных простоев, перейдите на [веб-сайт поддержки Microsoft Azure](https://azure.microsoft.com/support/options) , чтобы открыть обращение в службу поддержки.

Дополнительные сведения см. [в разделе Устранение неполадок при возникновении ошибки "база данных на сервере в настоящее время недоступна"](sql-database-troubleshoot-common-connection-issues.md#troubleshoot-transient-errors).

### <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-database-server"></a>При установлении соединения с сервером базы данных SQL произошла ошибка, связанная с сетью или экземпляром

Эта проблема возникает, если приложению не удается подключиться к серверу.

Чтобы устранить эту проблему, попробуйте выполнить действия (в указанном порядке) в разделе [действия по исправлению общих проблем с подключением](#steps-to-fix-common-connection-issues) .

### <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Сервер или экземпляр не найден или недоступен (ошибки 26, 40, 10053)

#### <a name="error-26-error-locating-server-specified"></a>Ошибка 26: ошибка при поиске указанного сервера

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>Ошибка 40: не удалось открыть соединение с сервером

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Ошибка 10053: произошла ошибка на транспортном уровне при получении результатов с сервера

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

#### <a name="cannot-connect-to-a-secondary-database"></a>Не удается подключиться к базе данных-получателю

Попытка подключения к базе данных-получателю завершилась сбоем, так как база данных повторно настраивается и к ней применяются новые страницы в процессе активной транзакции в базе данных-источнике.

#### <a name="adonet-and-blocking-period"></a>ADO.NET и период блокировки

Обсуждение *периода блокировки* для клиентов, которые используют ADO.NET, см. в статье [Организация пулов соединений SQL Server (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="list-of-transient-fault-error-codes"></a>Список кодов ошибок временной ошибки

Ниже приведены временные ошибки, для которых в приложении следует реализовать логику повтора.

| Код ошибки | Уровень серьезности | ОПИСАНИЕ |
| ---:| ---:|:--- |
| 4060 |16 |Невозможно открыть базу данных %.&#x2a;ls, запрашиваемую именем входа. Вход в систему не выполнен. Дополнительные сведения см. [в разделе ошибки с 4000 по 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999) .|
| 40197 |17 |При обработке вашего запроса служба обнаружила ошибку. Повторите попытку позже. Код ошибки: %d.<br/><br/>Эта ошибка возникает, если служба не работает по причине обновления программного или аппаратного обеспечения, аппаратных ошибок или проблем при отработке отказа. Код ошибки (% d), внедренный в сообщение об ошибке 40197], предоставляет дополнительные сведения о типе сбоя или отработки отказа, которые произошли. В сообщении об ошибке 40197 может быть указан код ошибки 40020, 40143, 40166 или 40540.<br/><br/>При повторном подключении сервер Базы данных SQL автоматически подключается к рабочей копии базы данных. Приложение должно зафиксировать ошибку 40197, зарегистрировать указанный в сообщении код ошибки (%d) для устранения неполадок и повторять попытки подключения к базе данных SQL, пока не появится доступ к ресурсам, а подключение не будет восстановлено. Дополнительные сведения см. в разделе [временные ошибки](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |Служба в настоящий момент занята. Повторите запрос через 10 секунд. Идентификатор инцидента: %ls. Код: %d. Дополнительные сведения см. в следующих источниках. <br/>[ограничения ресурсов сервера базы данных](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>&bull; &nbsp;[ограничения на основе DTU для отдельных баз данных](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[ограничения на основе DTU для эластичных пулов](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[ограничения на основе виртуальное ядро для отдельных баз данных](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[ограничения на основе виртуальное ядро для эластичных пулов](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; [ограничения ресурсов для управляемого экземпляра](sql-database-managed-instance-resource-limits.md)&nbsp;.|
| 40613 |17 |База данных %.&#x2a;ls на сервере %.&#x2a;ls в данный момент недоступна. Повторите попытку подключения позже. Если проблема повторится, обратитесь в службу поддержки пользователей и сообщите идентификатор трассировки сеанса %.&#x2a;ls.<br/><br/> Эта ошибка может возникать, если уже имеется выделенное административное соединение (DAC), установленное для базы данных. Дополнительные сведения см. в разделе [временные ошибки](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |Не удается обработать запрос. Недостаточно ресурсов для обработки запроса.<br/><br/>Служба в настоящий момент занята. Повторите запрос позже. Дополнительные сведения см. в следующих источниках. <br/>[ограничения ресурсов сервера базы данных](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>&bull; &nbsp;[ограничения на основе DTU для отдельных баз данных](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[ограничения на основе DTU для эластичных пулов](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[ограничения на основе виртуальное ядро для отдельных баз данных](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[ограничения на основе виртуальное ядро для эластичных пулов](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; [ограничения ресурсов для управляемого экземпляра](sql-database-managed-instance-resource-limits.md)&nbsp;. |
| 49919 |16 |Невозможно обработать запрос на создание или обновление. Для подписки "%ld" выполняется слишком много операций создания или обновления.<br/><br/>Служба занята обработкой нескольких запросов на создание или обновление для вашей подписки или сервера. В данный момент запросы блокируются для оптимизации ресурсов. Выполните запрос [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) для ожидающих операций. Подождите, пока выполнятся ожидающие запросы на создание или обновление, либо удалите один из ожидающих запросов и повторите свой запрос позже. Дополнительные сведения см. в следующих источниках. <br/>[ограничения ресурсов сервера базы данных](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>&bull; &nbsp;[ограничения на основе DTU для отдельных баз данных](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[ограничения на основе DTU для эластичных пулов](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[ограничения на основе виртуальное ядро для отдельных баз данных](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[ограничения на основе виртуальное ядро для эластичных пулов](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; [ограничения ресурсов для управляемого экземпляра](sql-database-managed-instance-resource-limits.md)&nbsp;. |
| 49920 |16 |Не удается обработать запрос. Для подписки «%ld» выполняется слишком много операций.<br/><br/>Служба занята обработкой нескольких запросов для этой подписки. В данный момент запросы блокируются для оптимизации ресурсов. Выполните запрос [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) для состояния операции. Подождите, пока выполнятся ожидающие запросы, либо удалите один из ожидающих запросов и повторите свой запрос позже. Дополнительные сведения см. в следующих источниках. <br/>[ограничения ресурсов сервера базы данных](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>&bull; &nbsp;[ограничения на основе DTU для отдельных баз данных](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[ограничения на основе DTU для эластичных пулов](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[ограничения на основе виртуальное ядро для отдельных баз данных](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[ограничения на основе виртуальное ядро для эластичных пулов](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; [ограничения ресурсов для управляемого экземпляра](sql-database-managed-instance-resource-limits.md)&nbsp;. |
| 4221 |16 |Произошел сбой при входе в базу данных-получатель для чтения из-за длительного ожидания выполнения HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING. Вход в реплику невозможен, так как отсутствуют версии строк для транзакций, которые выполнялись при перезапуске реплики. Чтобы устранить проблему, откатите реплику или зафиксируйте активные транзакции в первичной реплике. Большинство таких случаев можно избежать, если не выполнять длительные транзакции записи в базе данных-источнике. |

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Не удается подключиться к серверу из-за проблем с брандмауэром

### <a name="error-40615-cannot-connect-to--servername-"></a>Ошибка 40615: не удается подключиться к < ServerName >

Чтобы устранить эту проблему, [Настройте параметры брандмауэра в базе данных SQL с помощью портал Azure.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>Ошибка 5: не удается подключиться к < ServerName >

Чтобы устранить эту проблему, убедитесь, что порт 1433 открыт для исходящих подключений во всех брандмауэрах между клиентом и Интернетом.

Дополнительные сведения см. [в разделе Настройка брандмауэра Windows для разрешения доступа к SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Не удалось войти на сервер (ошибки 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Ошибка входа пользователя "< имя пользователя >"

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Чтобы устранить эту проблему, обратитесь к администратору службы, чтобы ввести допустимое имя пользователя SQL Server и пароль.

Как правило, администратор служб может выполнить следующие действия, чтобы добавить учетные данные для входа.

1. Войдите на сервер с помощью SQL Server Management Studio (SSMS).
2. Выполните следующий SQL-запрос, чтобы проверить, отключено ли имя для входа:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Если имя отключено, включите его с помощью следующей инструкции:

   ```sql
   Alter login <User name> enable
   ```

4. Если имя пользователя для входа SQL не существует, создайте его, выполнив следующие действия.

   1. В среде SSMS дважды щелкните **Безопасность** , чтобы развернуть ее.
   2. Щелкните правой кнопкой мыши пункт **Имена для входа** и выберите пункт **Создать имя для входа**.
   3. В созданном скрипте с заполнителями измените и выполните следующий запрос SQL:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```

5. Дважды щелкните **База данных**.
6. Выберите базу данных, которой необходимо предоставить разрешение пользователя.
7. Дважды щелкните **Безопасность**.
8. Щелкните правой кнопкой мыши пункт **Пользователи** и выберите **Новый пользователь**.
9. В созданном скрипте с заполнителями измените и выполните следующий запрос SQL:

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```

   > [!NOTE]
   > Можно также использовать `sp_addrolemember`, чтобы сопоставлять конкретных пользователей с конкретными ролями базы данных.

Дополнительные сведения см. [в статье Управление базами данных и именами входа в базе данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="connection-timeout-expired-errors"></a>Ошибки времени ожидания подключения истекли

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): время ожидания подключения истекло

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): время ожидания истекло

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. Data. Entity. Core. Ентитексцептион: сбой базового поставщика при открытии

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Не удается подключиться к серверу < имя >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Эти исключения могут возникать из-за проблем с подключением или запросами. Чтобы убедиться, что эта ошибка вызвана проблемами подключения, см. статью Проверка [причины ошибки подключения](#confirm-whether-an-error-is-caused-by-a-connectivity-issue).

Время ожидания соединения связано с тем, что приложению не удается подключиться к серверу. Чтобы устранить эту проблему, попробуйте выполнить действия (в указанном порядке) в разделе [действия по исправлению общих проблем с подключением](#steps-to-fix-common-connection-issues) .

## <a name="transient-errors-errors-40197-40545"></a>Временные ошибки (ошибки 40197, 40545)

### <a name="error-40197-the-service-has-encountered-an-error-processing-your-request-please-try-again-error-code--code-"></a>Ошибка 40197: Служба обнаружила ошибку при обработке запроса. Повторите попытку позже. Код ошибки < >

Эта проблема возникает из-за временной ошибки, возникшей во время перенастройки или отработки отказа в серверной части.

Чтобы устранить эту проблему, подождите короткий период и повторите попытку. Если проблема не будет устранена, это не потребует никаких обращений в службу поддержки.

Рекомендуется использовать логику повторных попыток. Дополнительные сведения о логике повторных попыток см. [в разделе Устранение временных сбоев и ошибок подключения к базе данных SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

## <a name="resource-governance-errors"></a>Ошибки управления ресурсами

### <a name="error-10928-resource-id-d"></a>Ошибка 10928: идентификатор ресурса:% d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Чтобы обойти эту ошибку, попробуйте один из следующих методов.

* Проверьте наличие долго выполняющихся запросов.

  > [!NOTE]
  > Это минимальный подход, который может не решить проблему.

1. Выполните следующий SQL запрос, чтобы проверить представление [sys. dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) , чтобы увидеть все блокирующие запросы:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Определите **входной буфер** для блока Head.
3. Настройка запроса к головному блоку.

   Подробное описание процедуры устранения неполадок см. в статье как [работает мой запрос в облаке?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

Если база данных постоянно достигает предела, несмотря на блокировку и длительные запросы, рассмотрите возможность обновления до выпуска с дополнительными [выпусками](https://azure.microsoft.com/pricing/details/sql-database/)ресурсов.

Дополнительные сведения о динамических административных представлениях см. в разделе [системные динамические административные представления](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Дополнительные сведения об ограничениях базы данных см. в статье [ограничения ресурсов базы данных SQL для сервера базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>Ошибка 10929: идентификатор ресурса: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>Ошибка 40501: служба в настоящий момент занята

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Это ошибка регулирования подсистемы, которая указывает на превышение лимитов ресурсов.

Дополнительные сведения об ограничениях ресурсов см. в разделе [ограничения ресурсов сервера базы данных](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Ошибка 40544: достигнута квота на размер базы данных

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Эта ошибка возникает, когда база данных достигла квоты на размер.

Следующие шаги могут помочь решить проблему или предоставить дополнительные возможности:

1. Проверьте текущий размер базы данных с помощью панели мониторинга в портал Azure.

   > [!NOTE]
   > Чтобы узнать, какие таблицы потребляют наибольшее пространство, и, следовательно, потенциальные кандидаты на очистку, выполните следующий SQL-запрос:

   ```sql
   SELECT o.name,
    a.SUM(p.row_count) AS 'Row Count',
    b.SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Если текущий размер не превышает максимальный размер, поддерживаемый в выпуске, можно использовать инструкцию ALTER DATABASE, чтобы увеличить значение параметра MAXSIZE.
3. Если размер базы данных уже превышает максимальный поддерживаемый выпуск, попробуйте выполнить одно или несколько из следующих действий.

   * Выполнение обычных действий по очистке базы данных. Например, очистите ненужные данные с помощью функции усечения или удаления или перемещения данных с помощью SQL Server Integration Services (SSIS) или программы для создания программного копирования (BCP).
   * Секционируйте или удалите данные, удалите индексы или попробуйте найти возможное решение в документации.
   * Сведения об масштабировании базы данных см. в статье [масштабирование ресурсов отдельной базы данных](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) и [масштабирование ресурса эластичного пула](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Ошибка 40549: сеанс завершен, так как имеется долго выполняющаяся транзакция

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Если эта ошибка повторяется, попробуйте устранить проблему, выполнив следующие действия.

1. Проверьте представление sys. dm_exec_requests, чтобы просмотреть все открытые сеансы, имеющие большое значение для столбца total_elapsed_time. Выполните эту проверку, выполнив следующий скрипт SQL:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Определите входной буфер для долго выполняющегося запроса.
3. Настройте запрос.

Также рассмотрите возможность пакетной обработки запросов. Сведения о пакетировании см. в разделе [Использование пакетной обработки для повышения производительности приложения базы данных SQL](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

Подробное описание процедуры устранения неполадок см. в статье как [работает мой запрос в облаке?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Ошибка 40551: сеанс был завершен из-за чрезмерного использования базы данных TEMPDB

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Чтобы обойти эту ошибку, выполните следующие действия.

1. Измените запросы, чтобы уменьшить использование временного пространства таблицы.
2. Удалить временные объекты после того, как они больше не нужны.
3. Усечение таблиц или удаление неиспользуемых таблиц.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Ошибка 40552: сеанс был завершен из-за чрезмерного использования места в журнале транзакций

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Устранить эту проблему можно с помощью следующих методов.

* Эта ошибка может возникнуть из-за операций вставки, обновления или удаления.
Попробуйте сократить количество строк, которые работают немедленно, путем реализации пакетной обработки или разделения на несколько меньших транзакций.
* Эта ошибка может возникать из-за операций перестроения индекса. Чтобы обойти эту ошибку, убедитесь, что число строк, затронутых в таблице * (средний размер поля, обновляемого в байтах + 80), < 2 гигабайта (ГБ).

  > [!NOTE]
  > Для перестроения индекса средний размер обновляемого поля должен быть заменен средним размером индекса.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Ошибка 40553: сеанс был завершен из-за чрезмерного использования памяти

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Чтобы обойти эту ошибку, попробуйте оптимизировать запрос.

Подробное описание процедуры устранения неполадок см. в статье как [работает мой запрос в облаке?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="table-of-additional-resource-governance-error-messages"></a>Таблица дополнительных сообщений об ошибках управления ресурсами

| Код ошибки | Уровень серьезности | ОПИСАНИЕ |
| ---:| ---:|:--- |
| 10928 |20 |Идентификатор ресурса: %d. Предел %s для базы данных составляет %d, и он достигнут. Дополнительные сведения см. в статье [Ограничения ресурсов Базы данных SQL для отдельных баз данных и баз данных в пуле](sql-database-resource-limits-database-server.md).<br/><br/>Идентификатор ресурса указывает на ресурс, предел которого был достигнут. Для рабочих потоков идентификатор ресурса = 1. Для сеансов идентификатор ресурса — 2.<br/><br/>Дополнительные сведения об этой ошибке и способах ее устранения см. в статье: <br/>[ограничения ресурсов сервера базы данных](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>&bull; &nbsp;[ограничения на основе DTU для отдельных баз данных](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[ограничения на основе DTU для эластичных пулов](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[ограничения на основе виртуальное ядро для отдельных баз данных](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[ограничения на основе виртуальное ядро для эластичных пулов](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; [ограничения ресурсов для управляемого экземпляра](sql-database-managed-instance-resource-limits.md)&nbsp;. |
| 10929 |20 |Идентификатор ресурса: %d. Минимальная гарантия %s составляет %d, максимальное значение равно %d, а текущее использование для базы данных — %d. Тем не менее, в настоящее время сервер слишком занят, чтобы обработать более чем %d запросов для этой базы данных. Идентификатор ресурса указывает на ресурс, предел которого был достигнут. Для рабочих потоков идентификатор ресурса = 1. Для сеансов идентификатор ресурса — 2. Дополнительные сведения см. в следующих источниках. <br/>[ограничения ресурсов сервера базы данных](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>&bull; &nbsp;[ограничения на основе DTU для отдельных баз данных](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[ограничения на основе DTU для эластичных пулов](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[ограничения на основе виртуальное ядро для отдельных баз данных](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[ограничения на основе виртуальное ядро для эластичных пулов](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; [ограничения ресурсов для управляемого экземпляра](sql-database-managed-instance-resource-limits.md)&nbsp;. <br/>В противном случае повторите попытку позже. |
| 40544 |20 |База данных достигла предельного размера. Секционируйте или удалите данные, удалите индексы или попробуйте найти возможное решение в документации. Сведения об масштабировании базы данных см. в статье [масштабирование ресурсов отдельной базы данных](sql-database-single-database-scale.md) и [масштабирование ресурса эластичного пула](sql-database-elastic-pool-scale.md).|
| 40549 |16 |Сеанс остановлен из-за наличия транзакции с длительным временем выполнения. Попробуйте сократить время выполнения транзакции. Сведения о пакетировании см. в разделе [Использование пакетной обработки для повышения производительности приложения базы данных SQL](sql-database-use-batching-to-improve-performance.md).|
| 40550 |16 |Сеанс остановлен, поскольку он использует слишком много блокировок. Попробуйте сократить количество читаемых или изменяемых строк в одной транзакции. Сведения о пакетировании см. в разделе [Использование пакетной обработки для повышения производительности приложения базы данных SQL](sql-database-use-batching-to-improve-performance.md).|
| 40551 |16 |Сеанс остановлен из-за чрезмерного использования `TEMPDB`. Попробуйте изменить запрос, чтобы сократить использование временного табличного пространства.<br/><br/>Если вы используете временные объекты, то для экономии места в базе данных `TEMPDB` удаляйте их сразу после того, как в них исчезнет необходимость. Дополнительные сведения об использовании tempdb в базе данных SQL см. в разделе [база данных tempdb в базе данных SQL](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |Сеанс остановлен из-за чрезмерного использования места для журналов транзакций. Попробуйте сократить количество изменяемых строк в одной транзакции. Сведения о пакетировании см. в разделе [Использование пакетной обработки для повышения производительности приложения базы данных SQL](sql-database-use-batching-to-improve-performance.md).<br/><br/>В случае выполнения массовых вставок с использованием служебной программы `bcp.exe` или класса `System.Data.SqlClient.SqlBulkCopy` попробуйте ограничить количество строк, копируемых на сервер при каждой транзакции, с помощью параметра `-b batchsize` или `BatchSize`. В случае перестройки индекса с помощью оператора `ALTER INDEX` попробуйте использовать параметр `REBUILD WITH ONLINE = ON`. Сведения о размерах журналов транзакций для модели приобретения Виртуальное ядро см. в следующих статьях: <br/>&bull; &nbsp;[ограничения на основе виртуальное ядро для отдельных баз данных](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[ограничения на основе виртуальное ядро для эластичных пулов](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; [ограничения ресурсов для управляемого экземпляра](sql-database-managed-instance-resource-limits.md)&nbsp;.|
| 40553 |16 |Сеанс остановлен из-за чрезмерного потребления памяти. Попробуйте изменить запрос и задать обработку меньшего числа строк.<br/><br/>Сокращение количества операций `ORDER BY` и `GROUP BY` в коде Transact-SQL позволяет уменьшить потребление памяти при выполнении запроса. Сведения об масштабировании базы данных см. в статье [масштабирование ресурсов отдельной базы данных](sql-database-single-database-scale.md) и [масштабирование ресурса эластичного пула](sql-database-elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Ошибки пула эластичных БД

Приведенные ниже ошибки связаны с созданием и использованием эластичных пулов.

| Код ошибки | Уровень серьезности | ОПИСАНИЕ | Корректирующее действие |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |Хранилище эластичного пула достигло своего предельного значения. Уровень использования хранилища для эластичного пула не может превышать (%d) МБ. Попытка записи данных в базу данных, когда было достигнуто предельное значение хранилища эластичного пула. Сведения об ограничениях ресурсов см. в следующих статьях: <br/>&bull; &nbsp;[ограничения на основе DTU для эластичных пулов](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[ограничения на основе виртуальное ядро для эластичных пулов](sql-database-vcore-resource-limits-elastic-pools.md). <br/> |Если это возможно, попробуйте увеличить DTU эластичного пула и (или) добавить в него пространство хранения для увеличения предельного размера его хранилища, сократить объем памяти, используемой отдельными базами данных в эластичном пуле, или удалить базы данных из пула. Сведения о масштабировании эластичных пулов см. в статье [масштабирование ресурсов эластичного пула](sql-database-elastic-pool-scale.md).|
| 10929 | 16 |Минимальная гарантия %s составляет %d, максимальное значение равно %d, а текущее использование для базы данных — %d. Тем не менее, в настоящее время сервер слишком занят, чтобы обработать более чем %d запросов для этой базы данных. Сведения об ограничениях ресурсов см. в следующих статьях: <br/>&bull; &nbsp;[ограничения на основе DTU для эластичных пулов](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[ограничения на основе виртуальное ядро для эластичных пулов](sql-database-vcore-resource-limits-elastic-pools.md). <br/> В противном случае повторите попытку позже. Минимальное количество DTU или виртуальных ядер на базу данных; максимальное количество DTU или виртуальных ядер на базу данных. Общее количество одновременных работников (запросов) по всем базам данных в эластичном пуле превысило ограничение пула. |Если это возможно, попробуйте увеличить DTU или число виртуальных ядер эластичного пула для увеличения предельно допустимого количества работников или удалить базы данных из эластичного пула. |
| 40844 | 16 |База данных '%ls' на сервере '%ls' является базой данных выпуска '%ls' в эластичном пуле и не может иметь связь непрерывной копии.  |Недоступно |
| 40857 | 16 |Эластичный пул для сервера '%ls' не найден, имя эластичного пула: '%ls'. На указанном сервере указанный эластичный пул не существует. | Укажите допустимое имя эластичного пула. |
| 40858 | 16 |Эластичный пул "%ls" на сервере "%ls" уже существует. Указанный эластичный пул уже существует на указанном сервере Базы данных SQL. | Укажите новое имя эластичного пула. |
| 40859 | 16 |Эластичный пул не поддерживает уровень служб '%ls'. Для подготовки эластичного пула указанный уровень служб не поддерживается. |Укажите правильный выпуск или оставьте значение уровня служб пустым, чтобы использовать значение по умолчанию. |
| 40860 | 16 |Недопустимая комбинация эластичного пула '%ls' и цели службы '%ls'. Эластичный пул и уровень служб можно указать вместе только в том случае, если тип ресурса задан как ElasticPool. |Укажите правильную комбинацию эластичного пула и уровня служб. |
| 40861 | 16 |Выпуск "%.*ls" базы данных не может отличаться от уровня служб пула эластичных БД, который равен "%.* ls". Выпуск базы данных отличается от уровня служб эластичного пула. |Не указывайте выпуск базы данных, который отличается от уровня служб эластичного пула.  Обратите внимание, что нет необходимости указывать выпуск базы данных. |
| 40862 | 16 |Если указана цель служб эластичного пула, то необходимо указать имя этого эластичного пула. Цель службы эластичного пула неоднозначно определяет эластичный пул. |Если используется цель служб эластичного пула, укажите имя этого эластичного пула. |
| 40864 | 16 |Число DTU для эластичного пула должно составлять по крайней мере (%d) DTU для уровня служб "%.*ls". Попытка задать для эластичного пула DTU, значение которого ниже минимального ограничения. |Повторите попытку, установив для эластичного пула DTU, значение которого хотя бы равно минимальному ограничению. |
| 40865 | 16 |Число DTU для эластичного пула не должно превышать (%d) DTU для уровня служб "%.*ls". Попытка задать для эластичного пула DTU, значение которого выше максимального ограничения. |Повторите попытку, установив для эластичного пула DTU, значение которого не превышает максимальное ограничение. |
| 40867 | 16 |Максимальное количество DTU на каждую базу данных должно составлять как минимум (%d) для уровня служб "%.*ls". Попытка задать максимальное количество DTU на каждую базу данных ниже поддерживаемого ограничения. | Попробуйте использовать уровень служб эластичного пула, который поддерживает желаемый параметр. |
| 40868 | 16 |Максимальное количество DTU на каждую базу данных не должно превышать (%d) для уровня служб "%.*ls". Попытка задать максимальное число DTU на каждую базу данных, которое находится за пределами поддерживаемого ограничения. | Попробуйте использовать уровень служб эластичного пула, который поддерживает желаемый параметр. |
| 40870 | 16 |Минимальное количество DTU на каждую базу данных не должно превышать (%d) для уровня служб "%.*ls". Попытка задать минимальное число DTU на каждую базу, которое находится за пределами поддерживаемого ограничения. | Попробуйте использовать уровень служб эластичного пула, который поддерживает желаемый параметр. |
| 40873 | 16 |Количество баз данных (%d) и минимальное количество DTU на каждую базу данных (%d) не может превышать DTU эластичного пула (%d). Попытка задать минимальное количество DTU для баз данных в эластичном пуле, превышающее DTU эластичного пула. | Попробуйте увеличить количество DTU эластичного пула, уменьшить минимальное количество DTU на каждую базу данных или уменьшить количество баз данных в эластичном пуле. |
| 40877 | 16 |Эластичный пул не может быть удален до тех пор, пока он содержит какую-либо базу данных. Эластичный пул содержит одну или несколько баз данных и не может быть удален. |Чтобы удалить эластичный пул, удалите из него базы данных. |
| 40881 | 16 |В эластичном пуле '%.*ls' достигнуто предельное количество баз данных.  Максимальное количество баз данных для пула эластичных баз данных не может превышать (%d) для пула эластичных баз данных с (%d) DTU. Попытка создания или добавления базы данных в эластичный пул, когда было достигнуто предельное количество баз данных эластичного пула. | Если это возможно, попробуйте увеличить DTU эластичного пула, чтобы увеличить предельное количество баз данных, или удалить базы данных из эластичного пула. |
| 40889 | 16 |Невозможно уменьшить количество DTU или размер хранилища для эластичного пула '%.*ls', так как недостаточно места в хранилище баз данных. Попытка ограничить размер хранилища эластичного пула, при которой уменьшается размер используемого им хранилища. | Попробуйте сократить использование хранилища отдельными базами данных в эластичном пуле или удалить базы данных из пула, чтобы уменьшить количество DTU или размер хранилища. |
| 40891 | 16 |Минимальное число DTU на базу данных (%d) не может превышать максимального числа DTU на базу данных (%d). Попытка задать минимальное число DTU на каждую базу данных, превышающее максимальное число DTU на каждую базу данных. |Убедитесь, что минимальное число DTU на базу данных не превышает максимального числа DTU на базу данных. |
| ПОДЛЕЖИТ УТОЧНЕНИЮ | 16 |Размер хранилища для отдельной базы данных в эластичном пуле не может превышать максимальный размер, допустимый уровнем служб эластичного пула "%.*ls". Максимальный размер базы данных превышает максимальный размер, допустимый уровнем служб эластичного пула. |Укажите максимальный размер базы данных, который находится в пределах ограничений на максимальный размер, допустимый уровнем служб эластичного пула. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Не удается открыть базу данных "Master", запрашиваемую именем для входа. Ошибка входа

Эта проблема возникает из-за того, что учетная запись не имеет разрешения на доступ к базе данных master. Но по умолчанию SQL Server Management Studio (SSMS) пытается подключиться к базе данных master.

Проблему можно устранить следующим способом.

1. На экране входа в SSMS выберите **Параметры**, а затем выберите **Свойства соединения**.
2. В поле **Подключение к базе данных** введите имя базы данных пользователя по умолчанию в качестве базы данных для входа по умолчанию, а затем нажмите кнопку **подключить**.

   ![Свойства подключения](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Проверьте, вызвана ли ошибка проблемой подключения.

Чтобы проверить, вызвана ли ошибка подключением, просмотрите трассировку стека для кадров, которые показывают вызовы, чтобы открыть подключение, как показано ниже (Обратите внимание на ссылку на класс **SqlConnection** ):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Если исключение вызывается проблемами запросов, вы увидите стек вызовов, аналогичный приведенному ниже (Обратите внимание на ссылку на класс **SqlCommand** ). В этом случае [Настройте запросы](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

Дополнительные рекомендации по точной настройке производительности см. в следующих ресурсах:

* [Как поддерживать индексы и статистику Azure SQL](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Настройка производительности запросов вручную в базе данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Мониторинг производительности базы данных SQL Azure с помощью динамических административных представлений](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Управление хранилищем запросов в Базе данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)

## <a name="steps-to-fix-common-connection-issues"></a>Действия по устранению распространенных проблем с подключением

1. Убедитесь, что протокол TCP/IP включен в качестве клиентского протокола на сервере приложений. Дополнительные сведения см. в разделе [Настройка клиентских протоколов](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). На серверах приложений, на которых не установлены средства SQL Server, убедитесь, что протокол TCP/IP включен, выполнив **cliconfg. exe** (служебная программа для клиента SQL Server).
2. Проверьте строку подключения приложения, чтобы убедиться, что она настроена правильно. Например, убедитесь, что в строке подключения указан правильный порт (1433) и полное имя сервера.
См. раздел [Получение сведений о подключении SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Попробуйте увеличить значение времени ожидания подключения. Мы рекомендуем использовать время ожидания подключения не менее 30 секунд.
4. Проверьте подключение между сервером приложений и базой данных SQL Azure с помощью [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms), файла UDL, проверки связи или Telnet. Дополнительные сведения см. в статьях [Устранение неполадок подключения SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) и [Диагностика проблем с подключением](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics).

   > [!NOTE]
   > На этапе устранения неполадок можно также проверить возможность подключения на другом клиентском компьютере.

5. Рекомендуется убедиться в том, что логика повторных попыток выполнена. Дополнительные сведения о логике повторных попыток см. [в разделе Устранение временных сбоев и ошибок подключения к базе данных SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

Если эти действия не помогли устранить проблему, попробуйте получить дополнительные данные и обратитесь в службу поддержки. Если приложение является облачной службой, включите ведение журнала. Этот шаг возвращает метку времени ошибки в формате UTC. Кроме того, SQL Azure возвращает идентификатор трассировки. Эти сведения могут использовать [службы поддержки пользователей Майкрософт](https://azure.microsoft.com/support/options/) .

Дополнительные сведения о включении ведения журнала см. [в статье Включение ведения журнала диагностики для приложений в службе приложений Azure](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

## <a name="next-steps"></a>Дополнительная информация

* [Архитектура подключения SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)
* [Управление доступом к сети для базы данных SQL Azure и хранилища данных](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)
