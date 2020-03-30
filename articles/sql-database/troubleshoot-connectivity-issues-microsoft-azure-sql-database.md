---
title: Устранение проблем с общими проблемами подключения к базе данных S'L
description: Предоставляет шаги для устранения неполадок в связи с базой данных Azure s'L и устранения других конкретных проблем базы данных S'L
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019
author: ramakoni1
ms.author: ramakoni
ms.reviewer: carlrab,vanto
ms.date: 01/14/2020
ms.openlocfilehash: 578d076dfc2fc383051c0c3a8528cdbb0fbcdb15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208788"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-microsoft-azure-sql-database"></a>Устранение неполадок с подключением и решение других проблем с Базой данных SQL Microsoft Azure

При сбоях подключения к базе данных SQL Azure отображаются сообщения об ошибке. Эти проблемы с подключением могут быть вызваны перенастройкой базы данных Azure S'L, настройками брандмауэра, тайм-аутом соединения, неправильной информацией входа в систему или неспособностью применить рекомендации и рекомендации по проектированию приложений в процессе [проектирования приложения.](sql-database-develop-overview.md) Кроме того, если достигнут максимальный лимит на некоторые ресурсы базы данных Azure S'L, вы не сможете подключиться к базе данных Azure S'L.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>Сообщения об ошибке переходного исхода (40197, 40613 и др.)

Инфраструктура Azure способна динамически изменять конфигурацию серверов при высокой рабочей нагрузке на службу баз данных SQL.  Такое динамическое поведение может привести к разрыву подключения между клиентской программой и базой данных SQL. Такое состояние называется *временной ошибкой*. События перенастройки базы данных обычно происходят в запланированном случае (например, при обновлении программного обеспечения) или в незапланированном случае (например, при сбое процесса или балансировке нагрузки). Большей частью события перенастройки кратковременные и должны завершаться в течение 60 секунд максимум. Тем не менее завершение этих событий иногда может занимать больше времени, например когда большая транзакция приводит к длительному восстановлению. В следующей таблице перечислены различные переходные ошибки, которые приложения могут получить при подключении к базе данных S'L

### <a name="list-of-transient-fault-error-codes"></a>Список кодов временных ошибок ошибок


| Код ошибки | Severity | Описание |
| ---:| ---:|:--- |
| 4060 |16 |Невозможно открыть базу данных %.&#x2a;ls, запрашиваемую именем входа. Не удалось выполнить вход. Для получения дополнительной информации [см.](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |При обработке вашего запроса служба обнаружила ошибку. Повторите попытку. Код ошибки: %d.<br/><br/>Эта ошибка возникает, если служба не работает по причине обновления программного или аппаратного обеспечения, аппаратных ошибок или проблем при отработке отказа. Код ошибки (%d), встроенный в сообщение об ошибке 40197, предоставляет дополнительную информацию о том, какой сбой или неудача произошел. В сообщении об ошибке 40197 может быть указан код ошибки 40020, 40143, 40166 или 40540.<br/><br/>При повторном подключении сервер Базы данных SQL автоматически подключается к рабочей копии базы данных. Приложение должно зафиксировать ошибку 40197, зарегистрировать указанный в сообщении код ошибки (%d) для устранения неполадок и повторять попытки подключения к базе данных SQL, пока не появится доступ к ресурсам, а подключение не будет восстановлено. Для получения дополнительной информации [см.](sql-database-connectivity-issues.md#transient-errors-transient-faults)|
| 40501 |20 |Служба занята. Повторите запрос через 10 секунд. Идентификатор инцидента: %ls. Код: %d. Дополнительные сведения см. в разделе: <br/>&bull;&nbsp; [Ограничения ресурсов сервера баз данных](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [Ограничения на основе DTU для отдельных баз данных](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Ограничения на основе DTU для эластичных бассейнов](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-ограничения для отдельных баз данных](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-ограничения для эластичных бассейнов](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Управляемые пределы ресурсов экземпляра](sql-database-managed-instance-resource-limits.md).|
| 40613 |17 |База данных %.&#x2a;ls на сервере %.&#x2a;ls в данный момент недоступна. Повторите попытку подключения позже. Если проблема повторится, обратитесь в службу поддержки пользователей и сообщите идентификатор трассировки сеанса %.&#x2a;ls.<br/><br/> Эта ошибка может возникнуть при наличии уже существующего выделенного подключения администратора (DAC), установленного в базе данных. Для получения дополнительной информации [см.](sql-database-connectivity-issues.md#transient-errors-transient-faults)|
| 49918 |16 |Не удается обработать запрос. Недостаточно ресурсов для обработки запроса.<br/><br/>Служба занята. Повторите запрос позже. Дополнительные сведения см. в разделе: <br/>&bull;&nbsp; [Ограничения ресурсов сервера баз данных](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [Ограничения на основе DTU для отдельных баз данных](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Ограничения на основе DTU для эластичных бассейнов](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-ограничения для отдельных баз данных](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-ограничения для эластичных бассейнов](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Управляемые пределы ресурсов экземпляра](sql-database-managed-instance-resource-limits.md). |
| 49919 |16 |Невозможно обработать запрос на создание или обновление. Для подписки "%ld" выполняется слишком много операций создания или обновления.<br/><br/>Служба занята обработкой нескольких запросов на создание или обновление для вашей подписки или сервера. В данный момент запросы блокируются для оптимизации ресурсов. Выполните запрос [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) для ожидающих операций. Подождите, пока выполнятся ожидающие запросы на создание или обновление, либо удалите один из ожидающих запросов и повторите свой запрос позже. Дополнительные сведения см. в разделе: <br/>&bull;&nbsp; [Ограничения ресурсов сервера баз данных](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [Ограничения на основе DTU для отдельных баз данных](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Ограничения на основе DTU для эластичных бассейнов](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-ограничения для отдельных баз данных](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-ограничения для эластичных бассейнов](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Управляемые пределы ресурсов экземпляра](sql-database-managed-instance-resource-limits.md). |
| 49920 |16 |Не удается обработать запрос. Для подписки «%ld» выполняется слишком много операций.<br/><br/>Служба занята обработкой нескольких запросов для этой подписки. В данный момент запросы блокируются для оптимизации ресурсов. Выполните запрос [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) для состояния операции. Подождите, пока выполнятся ожидающие запросы, либо удалите один из ожидающих запросов и повторите свой запрос позже. Дополнительные сведения см. в разделе: <br/>&bull;&nbsp; [Ограничения ресурсов сервера баз данных](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [Ограничения на основе DTU для отдельных баз данных](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Ограничения на основе DTU для эластичных бассейнов](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-ограничения для отдельных баз данных](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-ограничения для эластичных бассейнов](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Управляемые пределы ресурсов экземпляра](sql-database-managed-instance-resource-limits.md). |
| 4221 |16 |Произошел сбой при входе в базу данных-получатель для чтения из-за длительного ожидания выполнения HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING. Вход в реплику невозможен, так как отсутствуют версии строк для транзакций, которые выполнялись при перезапуске реплики. Чтобы устранить проблему, откатите реплику или зафиксируйте активные транзакции в первичной реплике. Большинство таких случаев можно избежать, если не выполнять длительные транзакции записи в базе данных-источнике. |


### <a name="steps-to-resolve-transient-connectivity-issues"></a>Порядок устранения временных проблем подключения

1. Проверьте [панель мониторинга служб Microsoft Azure](https://azure.microsoft.com/status) на наличие каких-либо известных сбоев, произошедших в то время, когда приложение сообщало об ошибках.
2. Приложения, подключающиеся к облачной службе, такой как база данных SQL Azure, должны ожидать периодические события перенастройки и реализовывать логику повторов для обработки этих ошибок, а не отображать их как ошибки приложения для пользователей. 
3. Если база данных близка к исчерпанию доступных ресурсов, может возникать временная проблема подключения. Дополнительные сведения см. в статье [Обзор ограничений ресурсов базы данных SQL Azure](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Если проблемы подключения остаются или интервал, во время которого приложение обнаруживает ошибку, превышает 60 секунд, а также если в определенный день такая ошибка возникает многократно, зарегистрируйте запрос на поддержку Azure, нажав **Получить поддержку** на сайте [Поддержка Azure](https://azure.microsoft.com/support/options) .

#### <a name="implementing-retry-logic"></a>Реализация логики повторов
Настоятельно рекомендуем включить в клиентской программе логику повторных попыток, чтобы программа смогла восстановить подключение после определенного периода, позволяющего временной ошибке самоустраниться.  Рекомендуется подождать 5 секунд, прежде чем выполнять первую повторную попытку. Повторная попытка после ожидания менее 5 секунд может привести к перегрузке облачной службы. Для каждой последующей повторной попытки ожидание должно увеличиваться экспоненциально, но не более чем до 60 секунд.

Примеры кода с логикой повторных попыток см. в следующих статьях:
- [Подключите устойчиво к S'L с помощью ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [Выполнение устойчивого подключения к SQL с помощью PHP](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

Для получения дополнительной информации об обработке временных ошибок в обзоре приложения
* [Устранение временных ошибок соединения в базе данных S'L](sql-database-connectivity-issues.md)

Обсуждение *периода блокировки* для клиентов, которые используют ADO.NET, см. в статье [Организация пулов соединений SQL Server (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-database-server"></a>При установлении подключения к серверу базы данных S'L произошла ошибка, связанная с сетью или конкретным экземпляром

Проблема возникает, если приложение не может подключиться к серверу.

Чтобы решить эту проблему, попробуйте шаги (в представленном порядке) в [разделе Шаги исправления общего раздела проблем с подключением.](#steps-to-fix-common-connection-issues)

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Сервер/экземпляр не был найден или недоступен (ошибки 26, 40, 10053)

#### <a name="error-26-error-locating-server-specified"></a>Ошибка 26: Указанный сервер обнаружения ошибок

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>Ошибка 40: не удалось открыть подключение к серверу

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Ошибка 10053: При получении результатов с сервера произошла ошибка уровня транспортировки

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Эти проблемы возникают, если приложение не может подключиться к серверу.

Чтобы решить эти проблемы, попробуйте шаги (в представленном порядке) в [разделе Шаги исправления общего раздела проблем с подключением.](#steps-to-fix-common-connection-issues)

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Не удается подключиться к серверу из-за проблем с брандмауэром

### <a name="error-40615-cannot-connect-to--servername-"></a>Ошибка 40615: не может подключиться к < >

Чтобы решить эту проблему, [настроите настройки брандмауэра на базе данных S'L через портал Azure.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>Ошибка 5: не может подключиться к > < сервера

Чтобы решить эту проблему, убедитесь, что порт 1433 открыт для исходящих соединений на всех брандмауэрах между клиентом и Интернетом.

Для получения дополнительной информации [см. Наверяйте брандмауэр Windows, чтобы обеспечить доступ к серверу S'L.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Невозможно войти на сервер (ошибки 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Сбой входа для пользователя '< имя пользователя >'

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Чтобы решить эту проблему, обратитесь к администратору службы, чтобы предоставить вам действительное имя пользователя сервера и пароль.

Как правило, администратор службы может использовать следующие действия для добавления учетных данных для входа:

1. Войти на сервер с помощью студии управления серверами S'L (SSMS).
2. Выполнить следующий запрос S'L, чтобы проверить, отключено ли имя входа:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Если имя отключено, включите его с помощью следующей инструкции:

   ```sql
   Alter login <User name> enable
   ```

4. Если имя пользователя для входа в систему S'L не существует, создайте его следующим образом:

   1. В SSMS, дважды нажмите **безопасности,** чтобы расширить его.
   2. Щелкните правой кнопкой мыши пункт **Имена для входа** и выберите пункт **Создать имя для входа**.
   3. В сгенерированном скрипте с заполнителями отодевать и запустить следующий запрос S'L:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```

5. Дважды щелкните **База данных**.
6. Выберите базу данных, на которую вы хотите дать разрешение пользователя.
7. Дважды щелкните **Безопасность**.
8. Нажмите справа **на пользователях,** а затем выберите **нового пользователя.**
9. В сгенерированном скрипте с заполнителями отодевать и запустить следующий запрос S'L:

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
   > Можно также `sp_addrolemember` использовать для картирования конкретных пользователей к определенным ролям базы данных.

Для получения дополнительной информации посетите [информацию об управлении базами данных и логинами в базе данных Azure S'L.](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)

## <a name="connection-timeout-expired-errors"></a>Ошибки тайм-аута подключения истекли

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904): Срок подключения истек

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904): Срок оттаивание истек

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System.Data.Entity.Core.EntityException: Основной поставщик не удалось на Open

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Не удается подключиться к < названию сервера >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Эти исключения могут возникать либо из-за проблем с подключением или запросом. Чтобы подтвердить, что эта ошибка вызвана проблемами с подключением, [см. Confirm, вызвана ли ошибка проблемой подключения.](#confirm-whether-an-error-is-caused-by-a-connectivity-issue)

Время подключения происходит из-за того, что приложение не может подключиться к серверу. Чтобы решить эту проблему, попробуйте шаги (в представленном порядке) в [разделе Шаги исправления общего раздела проблем с подключением.](#steps-to-fix-common-connection-issues)

## <a name="resource-governance-errors"></a>Ошибки управления ресурсами

### <a name="error-10928-resource-id-d"></a>Ошибка 10928: Идентификатор ресурсов: %d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Чтобы обойти этот вопрос, попробуйте один из следующих методов:

* Проверьте, есть ли долгосрочные запросы.

  > [!NOTE]
  > Это минималистский подход, который может не решить проблему.

1. Выполнить следующий запрос S'L, чтобы проверить [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) просмотра, чтобы увидеть любые блокировки запросов:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Определите **буфер ввода** для головного блокировщика.
3. Настройте запрос на головной блокировщик.

   Для углубленной процедуры устранения неполадок [см.](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

Если база данных последовательно достигает своего предела, несмотря на решение блокировок и длительных запросов, рассмотрите возможность обновления до издания с большим количеством ресурсов [Editions](https://azure.microsoft.com/pricing/details/sql-database/)).

Для получения дополнительной информации [System dynamic management views](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)о динамических представлениях управления см.

Для получения более подробной [SQL Database resource limits for Azure SQL Database server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)информации об ограничениях баз данных см.

### <a name="error-10929-resource-id-1"></a>Ошибка 10929: Идентификатор ресурсов: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>Ошибка 40501: Служба в настоящее время занята

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Это ошибка регулирования двигателя, что указывает на превышение лимитов ресурсов.

Для получения дополнительной информации [Database server resource limits](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)об ограничениях ресурсов см.

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Ошибка 40544: База данных достигла своей квоты размера

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Эта ошибка возникает, когда база данных достигла своей квоты размера.

Следующие шаги могут помочь вам обойти проблему или предоставить вам дополнительные опции:

1. Проверьте текущий размер базы данных с помощью панели мониторинга на портале Azure.

   > [!NOTE]
   > Чтобы определить, какие таблицы занимают больше всего места и поэтому являются потенциальными кандидатами на очистку, запустите следующий запрос S'L:

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Если текущий размер не превышает максимальный размер, поддерживаемый для издания, вы можете использовать ALTER DATABASE для увеличения параметра MAX-Size.
3. Если база данных уже прошла максимальный поддерживаемый размер для вашего издания, попробуйте один или несколько следующих шагов:

   * Выполняйте обычные действия по очистке базы данных. Например, очистка нежелательных данных с помощью усечения/удаления или перемещение данных с помощью служб интеграции серверов (SSIS) или утилиты массовой копирования (bcp).
   * Секционируйте или удалите данные, удалите индексы или попробуйте найти возможное решение в документации.
   * Для масштабирования баз данных см. [Масштаб единой базы данных ресурсов](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) и [ресурсов эластичного пула.](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale)

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Ошибка 40549: Сессия прекращается, потому что у вас есть длительная транзакция

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Если вы неоднократно сталкивались с этой ошибкой, попробуйте решить проблему, выполнив следующие следующие действия:

1. Проверьте представление sys.dm_exec_requests, чтобы увидеть открытые сеансы, которые имеют высокое значение для total_elapsed_time столбца. Выполните эту проверку, запустив следующий скрипт S'L:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Определите буфер ввода для длительного запроса.
3. Настройте запрос.

Также рассмотрите возможность пакетирования запросов. Для получения информации [How to use batching to improve SQL Database application performance](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)о пакетных пакетных пакетов см.

Для углубленной процедуры устранения неполадок [см.](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Ошибка 40551: Сеанс был прекращен из-за чрезмерного использования TEMPDB

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Чтобы устранить эту проблему, выполните следующие действия:

1. Измените запросы, чтобы уменьшить временное использование пространства таблицы.
2. Отбросьте временные объекты после того, как они больше не нужны.
3. Truncate таблицы или удалить неиспользованные таблицы.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Ошибка 40552: Сеанс был прекращен из-за чрезмерного использования пространства журнала транзакций

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Устранить эту проблему можно с помощью следующих методов.

* Проблема может возникнуть из-за вставки, обновления или удаления операций.
Попробуйте уменьшить количество строк, которые работают сразу же путем реализации пакетирования или разделения на несколько небольших транзакций.
* Проблема может возникнуть из-за операций восстановления индекса. Чтобы обойти эту проблему, убедитесь, что количество строк, которые затрагиваются в таблице , (средний размер поля, который обновляется в байтах No 80) < 2 гигабайта (ГБ).

  > [!NOTE]
  > Для восстановления индекса средний размер обновленного поля должен быть заменен средним размером индекса.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Ошибка 40553: Сеанс был прекращен из-за чрезмерного использования памяти

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Чтобы обойти эту проблему, попробуйте оптимизировать запрос.

Для углубленной процедуры устранения неполадок [см.](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

### <a name="table-of-additional-resource-governance-error-messages"></a>Таблица дополнительных сообщений об ошибках управления ресурсами

| Код ошибки | Severity | Описание |
| ---:| ---:|:--- |
| 10928 |20 |Идентификатор ресурса: %d. Предел %s для базы данных составляет %d, и он достигнут. Дополнительные сведения см. в статье [Ограничения ресурсов Базы данных SQL для отдельных баз данных и баз данных в пуле](sql-database-resource-limits-database-server.md).<br/><br/>Идентификатор ресурса указывает на ресурс, предел которого был достигнут. Для рабочих потоков идентификатор ресурса = 1. Для сеансов идентификатор ресурса — 2.<br/><br/>Дополнительные сведения об этой ошибке и способах ее устранения см. в статье: <br/>&bull;&nbsp; [Ограничения ресурсов сервера баз данных](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [Ограничения на основе DTU для отдельных баз данных](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Ограничения на основе DTU для эластичных бассейнов](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-ограничения для отдельных баз данных](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-ограничения для эластичных бассейнов](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Управляемые пределы ресурсов экземпляра](sql-database-managed-instance-resource-limits.md). |
| 10929 |20 |Идентификатор ресурса: %d. Минимальная гарантия %s составляет %d, максимальное значение равно %d, а текущее использование для базы данных — %d. Тем не менее, в настоящее время сервер слишком занят, чтобы обработать более чем %d запросов для этой базы данных. Идентификатор ресурса указывает на ресурс, предел которого был достигнут. Для рабочих потоков идентификатор ресурса = 1. Для сеансов идентификатор ресурса — 2. Дополнительные сведения см. в разделе: <br/>&bull;&nbsp; [Ограничения ресурсов сервера баз данных](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [Ограничения на основе DTU для отдельных баз данных](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Ограничения на основе DTU для эластичных бассейнов](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-ограничения для отдельных баз данных](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-ограничения для эластичных бассейнов](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Управляемые пределы ресурсов экземпляра](sql-database-managed-instance-resource-limits.md). <br/>В противном случае повторите попытку позже. |
| 40544 |20 |База данных достигла предельного размера. Секционируйте или удалите данные, удалите индексы или попробуйте найти возможное решение в документации. Для масштабирования баз данных см. [Масштаб единой базы данных ресурсов](sql-database-single-database-scale.md) и [ресурсов эластичного пула.](sql-database-elastic-pool-scale.md)|
| 40549 |16 |Сеанс завершен по причине долго выполняющейся транзакции. Рекомендуется сократить транзакцию. Для получения информации [How to use batching to improve SQL Database application performance](sql-database-use-batching-to-improve-performance.md)о пакетных пакетных пакетов см.|
| 40550 |16 |Сеанс был завершен в связи с тем, что он получил слишком много блокировок. Рекомендуется сократить число строк, считываемых или изменяемых в одной транзакции. Для получения информации [How to use batching to improve SQL Database application performance](sql-database-use-batching-to-improve-performance.md)о пакетных пакетных пакетов см.|
| 40551 |16 |Сеанс остановлен из-за чрезмерного использования `TEMPDB`. Попробуйте изменить запрос, чтобы сократить объем использования временных таблиц.<br/><br/>Если вы используете временные объекты, то для экономии места в базе данных `TEMPDB` удаляйте их сразу после того, как в них исчезнет необходимость. Для получения более подробной информации об использовании tempdb в базе [данных](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)S'L, см.|
| 40552 |16 |Сеанс был завершен в связи с чрезмерным использованием объема журнала транзакций. Рекомендуется сократить число строк, изменяемых в одной транзакции. Для получения информации [How to use batching to improve SQL Database application performance](sql-database-use-batching-to-improve-performance.md)о пакетных пакетных пакетов см.<br/><br/>В случае выполнения массовых вставок с использованием служебной программы `bcp.exe` или класса `System.Data.SqlClient.SqlBulkCopy` попробуйте ограничить количество строк, копируемых на сервер при каждой транзакции, с помощью параметра `-b batchsize` или `BatchSize`. В случае перестройки индекса с помощью оператора `ALTER INDEX` попробуйте использовать параметр `REBUILD WITH ONLINE = ON`. Для получения информации о размерах журналов транзакций для модели покупки vCore см.: <br/>&bull;&nbsp; [vCore-ограничения для отдельных баз данных](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-ограничения для эластичных бассейнов](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Управляемые пределы ресурсов экземпляра](sql-database-managed-instance-resource-limits.md).|
| 40553 |16 |Сеанс был завершен в связи с чрезмерным использованием памяти. Рекомендуется изменить запрос, сократив число обрабатываемых строк.<br/><br/>Сокращение количества операций `ORDER BY` и `GROUP BY` в коде Transact-SQL позволяет уменьшить потребление памяти при выполнении запроса. Для масштабирования баз данных см. [Масштаб единой базы данных ресурсов](sql-database-single-database-scale.md) и [ресурсов эластичного пула.](sql-database-elastic-pool-scale.md)|

## <a name="elastic-pool-errors"></a>Ошибки пула эластичных БД

Приведенные ниже ошибки связаны с созданием и использованием эластичных пулов.

| Код ошибки | Severity | Описание | Действие по исправлению |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |Хранилище эластичного пула достигло своего предельного значения. Уровень использования хранилища для эластичного пула не может превышать (%d) МБ. Попытка записи данных в базу данных, когда было достигнуто предельное значение хранилища эластичного пула. Для получения информации об ограничениях ресурсов см.: <br/>&bull;&nbsp; [Ограничения на основе DTU для эластичных бассейнов](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-ограничения для эластичных бассейнов.](sql-database-vcore-resource-limits-elastic-pools.md) <br/> |Если это возможно, попробуйте увеличить DTU эластичного пула и (или) добавить в него пространство хранения для увеличения предельного размера его хранилища, сократить объем памяти, используемой отдельными базами данных в эластичном пуле, или удалить базы данных из пула. Для масштабирования эластичного пула [см.](sql-database-elastic-pool-scale.md)|
| 10929 | 16 |Минимальная гарантия %s составляет %d, максимальное значение равно %d, а текущее использование для базы данных — %d. Тем не менее, в настоящее время сервер слишком занят, чтобы обработать более чем %d запросов для этой базы данных. Для получения информации об ограничениях ресурсов см.: <br/>&bull;&nbsp; [Ограничения на основе DTU для эластичных бассейнов](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-ограничения для эластичных бассейнов.](sql-database-vcore-resource-limits-elastic-pools.md) <br/> В противном случае повторите попытку позже. Минимальное количество DTU или виртуальных ядер на базу данных; максимальное количество DTU или виртуальных ядер на базу данных. Общее количество одновременных работников (запросов) по всем базам данных в эластичном пуле превысило ограничение пула. |Если это возможно, попробуйте увеличить DTU или число виртуальных ядер эластичного пула для увеличения предельно допустимого количества работников или удалить базы данных из эластичного пула. |
| 40844 | 16 |База данных '%ls' на сервере '%ls' является базой данных выпуска '%ls' в эластичном пуле и не может иметь связь непрерывной копии.  |Недоступно |
| 40857 | 16 |Эластичный пул для сервера '%ls' не найден, имя эластичного пула: '%ls'. На указанном сервере указанный эластичный пул не существует. | Укажите допустимое имя эластичного пула. |
| 40858 | 16 |Эластичный пул "%ls" на сервере "%ls" уже существует. Указанный эластичный пул уже существует на указанном сервере Базы данных SQL. | Укажите новое имя эластичного пула. |
| 40859 | 16 |Эластичный пул не поддерживает уровень служб '%ls'. Для подготовки эластичного пула указанный уровень служб не поддерживается. |Укажите правильный выпуск или оставьте значение уровня служб пустым, чтобы использовать значение по умолчанию. |
| 40860 | 16 |Недопустимая комбинация эластичного пула '%ls' и цели службы '%ls'. Эластичный пул и уровень служб можно указать вместе только в том случае, если тип ресурса задан как ElasticPool. |Укажите правильную комбинацию эластичного пула и уровня служб. |
| 40861 | 16 |Выпуск "%.*ls" базы данных не может отличаться от уровня служб пула эластичных БД, который равен "%.* ls". Выпуск базы данных отличается от уровня служб эластичного пула. |Не указывайте выпуск базы данных, который отличается от уровня служб эластичного пула.  Обратите внимание, что выпуск базы данных указывать не требуется. |
| 40862 | 16 |Если указана цель служб эластичного пула, то необходимо указать имя этого эластичного пула. Цель службы эластичного пула неоднозначно определяет эластичный пул. |Если используется цель служб эластичного пула, укажите имя этого эластичного пула. |
| 40864 | 16 |Число DTU для эластичного пула должно составлять по крайней мере (%d) DTU для уровня служб "%.*ls". Попытка задать для эластичного пула DTU, значение которого ниже минимального ограничения. |Повторите попытку, установив для эластичного пула DTU, значение которого хотя бы равно минимальному ограничению. |
| 40865 | 16 |Число DTU для эластичного пула не должно превышать (%d) DTU для уровня служб "%.*ls". Попытка задать для эластичного пула DTU, значение которого выше максимального ограничения. |Повторите попытку, установив для эластичного пула DTU, значение которого не превышает максимальное ограничение. |
| 40867 | 16 |Максимальное количество DTU на каждую базу данных должно составлять как минимум (%d) для уровня служб "%.*ls". Попытка задать максимальное количество DTU на каждую базу данных ниже поддерживаемого ограничения. | Попробуйте использовать уровень служб эластичного пула, который поддерживает желаемый параметр. |
| 40868 | 16 |Максимальное количество DTU на каждую базу данных не должно превышать (%d) для уровня служб "%.*ls". Попытка задать максимальное число DTU на каждую базу данных, которое находится за пределами поддерживаемого ограничения. | Попробуйте использовать уровень служб эластичного пула, который поддерживает желаемый параметр. |
| 40870 | 16 |Минимальное количество DTU на каждую базу данных не должно превышать (%d) для уровня служб "%.*ls". Попытка задать минимальное число DTU на каждую базу, которое находится за пределами поддерживаемого ограничения. | Попробуйте использовать уровень служб эластичного пула, который поддерживает желаемый параметр. |
| 40873 | 16 |Количество баз данных (%d) и минимальное количество DTU на каждую базу данных (%d) не может превышать DTU эластичного пула (%d). Попытка задать минимальное количество DTU для баз данных в эластичном пуле, превышающее DTU эластичного пула. | Попробуйте увеличить количество DTU эластичного пула, уменьшить минимальное количество DTU на каждую базу данных или уменьшить количество баз данных в эластичном пуле. |
| 40877 | 16 |Эластичный пул не может быть удален до тех пор, пока он содержит какую-либо базу данных. Эластичный пул содержит одну или несколько баз данных и не может быть удален. |Чтобы удалить эластичный пул, удалите из него базы данных. |
| 40881 | 16 |В эластичном пуле '%.*ls' достигнуто предельное количество баз данных.  Предельное количество баз данных не может превышать (%d) для эластичного пула с DTU (%d). Попытка создания или добавления базы данных в эластичный пул, когда было достигнуто предельное количество баз данных эластичного пула. | Если это возможно, попробуйте увеличить DTU эластичного пула, чтобы увеличить предельное количество баз данных, или удалить базы данных из эластичного пула. |
| 40889 | 16 |Невозможно уменьшить количество DTU или размер хранилища для эластичного пула '%.*ls', так как недостаточно места в хранилище баз данных. Попытка ограничить размер хранилища эластичного пула, при которой уменьшается размер используемого им хранилища. | Попробуйте сократить использование хранилища отдельными базами данных в эластичном пуле или удалить базы данных из пула, чтобы уменьшить количество DTU или размер хранилища. |
| 40891 | 16 |Минимальное число DTU на базу данных (%d) не может превышать максимального числа DTU на базу данных (%d). Попытка задать минимальное число DTU на каждую базу данных, превышающее максимальное число DTU на каждую базу данных. |Убедитесь, что минимальное число DTU на базу данных не превышает максимального числа DTU на базу данных. |
| TBD | 16 |Размер хранилища для отдельной базы данных в эластичном пуле не может превышать максимальный размер, допустимый уровнем служб эластичного пула "%.*ls". Максимальный размер базы данных превышает максимальный размер, допустимый уровнем служб эластичного пула. |Укажите максимальный размер базы данных, который находится в пределах ограничений на максимальный размер, допустимый уровнем служб эластичного пула. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Не удается открыть базу данных "мастер", запрошенный логином. Сбой входа

Эта проблема возникает из-за того, что учетная запись не имеет разрешения на доступ к основной базе данных. Но по умолчанию студия управления серверами S'L (SSMS) пытается подключиться к основной базе данных.

Проблему можно устранить следующим способом.

1. На экране входа SSMS выберите **параметры,** а затем выберите **Свойства подключения.**
2. В поле **«Подключение к базе данных»** введите имя базы данных пользователя по умолчанию в качестве базы данных входа по умолчанию, а затем выберите **Connect.**

   ![Свойства подключения](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Подтвердить, вызвана ли ошибка проблемой подключения

Чтобы подтвердить, вызвана ли ошибка проблемой подключения, просмотрите след стека для кадров, которые показывают вызовы, чтобы открыть соединение, как следующие (обратите внимание на ссылку на класс **SqlConnection):**

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Когда исключение вызвано проблемами запроса, вы заметите стек вызовов, похожий на следующий (обратите внимание на ссылку на класс **SqlCommand).** В этой ситуации [настройте запросы.](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

Дополнительные рекомендации по производительности тонкой настройки приведены в следующем списке ресурсов:

* [Как сохранить индексы и статистику Azure S'L](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Ручная настройка производительности запросов в Базе данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Мониторинг производительности данных Azure S'L с помощью динамических представлений управления](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Управление хранилищем запросов в Базе данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)

## <a name="steps-to-fix-common-connection-issues"></a>Инструкции по устранению распространенных неполадок с подключением

1. Убедитесь, что TCP/IP включен в качестве клиентского протокола на сервере приложения. Для получения дополнительной [информации см.](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols) На серверах приложений, где у вас нет установленных инструментов S'L Server, убедитесь, что TCP/IP включен атакжем **поддержки cliconfg.exe** (утилита S'L Server Client Network).
2. Проверьте строку соединения приложения, чтобы убедиться, что она настроена правильно. Например, убедитесь, что строка соединения определяет правильное название порта (1433) и полностью квалифицированное имя сервера.
Просмотрите [информацию о подключении сервера S'L](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Попробуйте увеличить значение тайм-аута соединения. Мы рекомендуем использовать время подключения не менее 30 секунд.
4. Проверьте подключение между сервером приложений и базой данных Azure S'L, используя [студию управления сервером S'L (SSMS),](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms)файл UDL, пинг или telnet. Для получения дополнительной информации см. [Проблемы устранения проблем с подключением сервера S'L](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) и [диагностики проблем с подключением.](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics)

   > [!NOTE]
   > В качестве шага по устранению неполадок можно также протестировать подключение на другом клиентском компьютере.

5. В качестве наилучшей практики убедитесь, что логика повтора на месте. Для получения дополнительной информации [Troubleshoot transient faults and connection errors to SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)о логике повтора см.

Если эти действия не решат вашу проблему, попробуйте собрать больше данных, а затем обратитесь в службу поддержки. Если ваше приложение является облачным сервисом, включите журнал. Этот шаг возвращает отметку времени UTC сбоя. Кроме того, S'L Azure возвращает идентификатор трассировки. [Службы поддержки клиентов Майкрософт](https://azure.microsoft.com/support/options/) могут использовать эту информацию.

Для получения дополнительной информации о том, как включить журнал, смотрите [Запись диагностики для приложений в Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

## <a name="next-steps"></a>Дальнейшие действия

* [Архитектура подключения Azure S'L](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)
* [Контроль доступа к сети лазурных данных и хранилища данных](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)
