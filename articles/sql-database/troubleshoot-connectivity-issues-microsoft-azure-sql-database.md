---
title: Устранение проблем с подключением к База данных SQL Microsoft Azure | Документация Майкрософт
description: Устранение проблем с подключением к База данных SQL Microsoft Azure
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 3717b36e37f7d9cca206b5a9e8e3cded50393748
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974426"
---
# <a name="troubleshooting-connectivity-issues-with-microsoft-azure-sql-database"></a>Устранение проблем с подключением к База данных SQL Microsoft Azure

При сбоях подключения к базе данных SQL Azure отображаются сообщения об ошибке. Проблемы с подключением могут возникнуть из-за перенастройки базы данных SQL Azure, параметров брандмауэра, истечения времени ожидания подключения или использования неправильных учетных данных. Кроме того, если достигнуто максимальное ограничение на некоторые ресурсы базы данных SQL Azure, вы не сможете подключиться к базе данных SQL Azure.

## <a name="error-40613-database--x--on-server--y--is-not-currently-available"></a>Ошибка 40613. База данных < x > на сервере < y > сейчас недоступна

**Подробные сведения об ошибке**

``40613: Database <DBname> on server <server name> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of '<Tracing ID>'.``

Действия для устранения этой проблемы.

1. Проверьте [Microsoft Azure панели мониторинга службы](https://status.azure.com/status) на наличие известных простоев. 
2. Если нет известных простоев, перейдите на [веб-сайт поддержки Microsoft Azure](http://azure.microsoft.com/support/options) , чтобы открыть обращение в службу поддержки.

Дополнительные сведения см. в разделе [Устранение ошибки "база данных на сервере в настоящее время недоступна"](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-common-connection-issues#troubleshoot-transient-errors).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server"></a>При установлении соединения с SQL Server возникла ошибка, связанная с сетью или экземпляром

Проблема возникает из-за того, что приложению не удается подключиться к серверу.

Чтобы устранить эту проблему, выполните действия (по порядку) в разделе ниже, **чтобы устранить распространенные проблемы с подключением**.

## <a name="the-server-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Сервер не найден или недоступен (ошибки 26, 40, 10053)

### <a name="error-26-error-locating-serverinstance-specified"></a>Ошибка 26: Ошибка при поиске указанного сервера или экземпляра

**Подробные сведения об ошибке**

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

### <a name="error-40-could-not-open-a-connection-to-sql-server"></a>Ошибка 40: Не удалось открыть соединение с SQL Server

**Подробные сведения об ошибке**

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Ошибка 10053: ошибка на транспортном уровне при получении результатов с сервера.

**Подробные сведения об ошибке**

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Эти проблемы возникают из-за того, что приложению не удается подключиться к серверу.

Чтобы устранить эту проблему, выполните действия (по порядку) в разделе ниже, **чтобы устранить распространенные проблемы с подключением**.

## <a name="cannot-connect-to--servername--due-to-firewall-issues"></a>Не удается подключиться к > < ServerName из-за проблем с брандмауэром

### <a name="error-40615-cannot-connect-to--servername-"></a>Ошибка 40615. Не удается подключиться к < ServerName >

Чтобы устранить эту проблему, [Настройте параметры брандмауэра в базе данных SQL с помощью портал Azure.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>Ошибка 5. Не удается подключиться к < ServerName >

Чтобы устранить эту проблему, убедитесь, что порт 1433 открыт для исходящих подключений во всех брандмауэрах между клиентом и Интернетом. 

Дополнительные сведения см. [в разделе Настройка брандмауэра Windows для разрешения доступа к SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="unable-to-login-to-the-server-errors-18456-40531"></a>Не удалось выполнить вход на сервер (ошибки 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Ошибка входа пользователя "< имя пользователя >"

**Подробные сведения об ошибке**

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Чтобы устранить эту проблему, обратитесь к администратору службы, чтобы указать допустимые имя пользователя SQL и пароль.

Как правило, администратор службы может выполнить следующие действия, чтобы добавить имя для входа:

1. Войдите на сервер с помощью SQL Server Management Studio (SSMS).
2. Проверьте, не отключено ли имя для входа, отправив следующий запрос SQL:

   ```
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Если имя отключено, включите его с помощью следующей инструкции: 

   ```
   Alter login <User name> enable
   ```

4. Если имя пользователя для входа SQL не существует, создайте его с помощью SSMS:

   1. Дважды щелкните элемент **Безопасность**, чтобы развернуть его. 
   2. Щелкните правой кнопкой мыши пункт **Имена для входа** и выберите пункт **Создать имя для входа**. 
   3. Подставьте значения в созданный скрипт с заполнителями и выполните следующий запрос SQL:
 
   ```
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```       
5. Дважды щелкните **База данных**. 
6. Выберите базу данных, на доступ к которой пользователю нужно предоставить разрешение.
7. Дважды щелкните **Безопасность**. 
8. Щелкните правой кнопкой мыши пункт **Пользователи** и выберите **Новый пользователь**. 
9. Подставьте значения в созданный скрипт с заполнителями и выполните следующий запрос SQL: 

   ```
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

Дополнительные сведения см. в статье [Управление базами данных и учетными записями в Базе данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="connection-timeout-expired-errors"></a>Ошибки времени ожидания подключения истекли

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): Время ожидания подключения истекло.

**Подробные сведения об ошибке**

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): Время ожидания истекло.

**Подробные сведения об ошибке**

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. Data. Entity. Core. Ентитексцептион: Сбой базового поставщика при открытии.

**Подробные сведения об ошибке**

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Не удается подключиться к серверу < имя >. ""

**Подробные сведения об ошибке**

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Эти исключения могут возникать из-за проблем с подключением или запросами. Чтобы убедиться, что эта ошибка возникла из-за проблем с подключением, обратитесь к разделу под названием **подтверждение того, связана ли ошибка с подключением**:

Время ожидания соединения связано с тем, что приложение не может подключиться к серверу. Чтобы устранить эту проблему, выполните действия (по порядку) в разделе ниже, **чтобы устранить распространенные проблемы с подключением**.

## <a name="transient-errors-errors-40197-40545"></a>Временные ошибки (ошибки 40197, 40545)

### <a name="error-40197-the-service-has-encountered-an-error-processing-your-request-please-try-again-error-code--code-"></a>Ошибка 40197: При обработке вашего запроса служба обнаружила ошибку. Повторите попытку позже. Код ошибки < >

Эта проблема возникает из-за временной ошибки, возникшей во время перенастройки или отработки отказа в серверной части.

Чтобы устранить эту проблему, подождите короткий период и повторите попытку. Если проблемы не сохраняются, обращение в службу поддержки не требуется.

Рекомендуется обеспечивать логику повторных попыток. Дополнительные сведения о логике повторных попыток см. [в разделе Устранение временных сбоев и ошибок подключения к базе данных SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

## <a name="connection-terminated-due-to-hitting-some-system-defined-limit"></a>Подключение прервано из-за превышения определенного системой ограничения

### <a name="error-10928-resource-id-d"></a>Ошибка 10928. Идентификатор ресурса: %d.

**Подробные сведения об ошибке**

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Чтобы обойти эту ошибку, попробуйте один из следующих методов.

* Проверьте наличие долго выполняющихся запросов:

  > [!NOTE]
  > Этот подход является минимальным и не всегда может решить проблему.

  1. Проверьте представление [sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) , чтобы просмотреть все блокирующие запросы, выполнив следующий запрос SQL:

     ```
     SELECT * FROM dm_exec_requests
     ```

  2. Определите **входной буфер** для блока Head.
  3. Настройка запроса к головному блоку.

    Подробное описание процедуры устранения неполадок см. в статье как [работает мой запрос в облаке?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

* Если база данных постоянно достигает предела, несмотря на блокировку и длительные запросы, рассмотрите возможность обновления до одного из новых предварительных выпусков (таких как [выпуски Standard или Premium](https://azure.microsoft.com/pricing/details/sql-database/)).

Дополнительные сведения о ценах на базу данных SQL см. в статье [цены на базу данных SQL Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

Дополнительные сведения о динамических административных представлениях см. в разделе [системные динамические административные представления](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Дополнительные сведения об этом сообщении об ошибке см. в статье [ограничения ресурсов базы данных SQL для сервера базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>Ошибка 10929: Идентификатор ресурса: 1.

**Подробные сведения об ошибке**

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

Дополнительные сведения об этой ошибке см. в разделе [сообщения об ошибках для клиентских программ базы данных SQL](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages) .

### <a name="error-40501-the-service-is-currently-busy"></a>Ошибка 40501: Служба в данный момент занята

**Подробные сведения об ошибке**

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Это ошибка регулирования подсистемы, которая указывает на превышение лимитов ресурсов.

Дополнительные сведения об ограничениях ресурсов см. в разделе [ограничения ресурсов сервера базы данных](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-40544--the-database-has-reached-its-size-quota"></a>Ошибка 40544: База данных достигла квоты на размер

**Подробные сведения об ошибке**

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Эта ошибка возникает, когда база данных достигла квоты на размер.

Следующие шаги помогут решить проблему или предоставить дополнительные возможности, которые можно принять во внимание.

1. Проверьте текущий размер базы данных с помощью панели мониторинга в портал Azure.

   > [!NOTE]
   > Чтобы узнать, какие таблицы потребляют наибольшее пространство и потенциальные кандидаты на очистку, можно использовать следующий SQL-запрос:

   ```
   SELECT o.name,
    a.SUM(p.row_count) AS 'Row Count',
    b.SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Если текущий размер не превышает максимальный размер, поддерживаемый в выпуске, можно использовать инструкцию ALTER DATABASE, чтобы увеличить значение параметра MAXSIZE. 
3. Если размер базы данных уже превышает максимальный поддерживаемый размер для вашего выпуска, можно выполнить одно из следующих действий.
   1. Выполнение обычных действий очистки базы данных (очистка ненужных данных с помощью усечения или удаления и т. д.) или перемещение данных с помощью служб SSIS, bcp и т. д.)
   2. Секционируйте или удалите данные, удалите индексы или попробуйте найти возможное решение в документации. 
   
   *  Сведения об масштабировании базы данных см. в статье [масштабирование ресурсов отдельной базы данных](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) и [масштабирование ресурса эластичного пула](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Ошибка 40549: Сеанс остановлен из-за наличия транзакции с длительным временем выполнения.

**Подробные сведения об ошибке**

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Если вы Повторяйте это сообщение об ошибке, попробуйте выполнить следующие действия, чтобы устранить эту проблему: 

1. Проверьте представление sys. DM _exec_requests, чтобы просмотреть все открытые сеансы, имеющие большое значение для столбца total_elapsed_time, выполнив следующий сценарий SQL:

   ```
   SELECT * FROM dm_exec_requests
   ```
2. Определите входной буфер для долго выполняющегося запроса. 
3. Настройте запрос.

Также рассмотрите возможность пакетной обработки запросов. Сведения о пакетировании см. в разделе [Использование пакетной обработки для повышения производительности приложения базы данных SQL](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

Подробное описание процедуры устранения неполадок см. в статье как [работает мой запрос в облаке?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Ошибка 40551: Сеанс был завершен из-за чрезмерного использования базы данных TEMPDB.

**Подробные сведения об ошибке**

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Чтобы обойти эту ошибку, выполните следующие действия.

1. Измените запросы, чтобы уменьшить использование временного пространства таблицы. 
2. Удалите временные объекты после того, как они больше не нужны. 
3. Усечение таблиц или удаление неиспользуемых таблиц.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Ошибка 40552: Сеанс остановлен из-за чрезмерного использования места для журналов транзакций.

**Подробные сведения об ошибке**

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Чтобы устранить эту проблему, выполните следующие действия. 

* Проблема возникает из-за операций вставки, обновления или удаления. Попробуйте сократить количество строк, которые работают немедленно, путем реализации пакетной обработки или разделения на несколько меньших транзакций.
* Проблема возникает из-за операций перестроения индекса. Убедитесь, что вы соблюдаете следующую формулу: количество строк, затронутых в таблице * (средний размер поля, обновляемого в байтах + 80) < 2 ГБ.

  > [!NOTE]
  > Для перестроения индекса средний размер обновляемого поля должен быть заменен средним размером индекса.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Ошибка 40553: Сеанс остановлен из-за чрезмерного потребления памяти.

**Подробные сведения об ошибке**

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Чтобы обойти эту ошибку, попробуйте оптимизировать запрос.

Подробное описание процедуры устранения неполадок см. в статье как [работает мой запрос в облаке?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).


### <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Не удается открыть базу данных "Master", запрашиваемую именем для входа. Вход в систему не выполнен.

Эта проблема возникает из-за того, что учетная запись не имеет разрешения на доступ к базе данных master. Однако по умолчанию SQL Server Management Studio (SSMS) пытается подключиться к базе данных master.

Проблему можно устранить следующим способом.

1. На экране входа в SSMS выберите **Параметры**, а затем щелкните **Свойства соединения**. 
2. В поле **Подключение к базе данных**введите имя базы данных пользователя по умолчанию в качестве имени для входа по умолчанию, а затем нажмите кнопку **подключить**.

   ![Каннот-опен-датабасе-мастер. png](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-due-to-a-connectivity-issue"></a>Убедитесь, что ошибка возникла из-за проблемы с подключением

Чтобы убедиться, что ошибка возникла из-за проблемы с подключением, просмотрите трассировку стека для кадров, которые показывают вызовы, чтобы открыть подключение, как показано ниже (Обратите внимание на ссылку на класс **SqlConnection** ):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Когда исключение возникает из-за проблем с запросами, вы увидите стек вызовов, аналогичный приведенному ниже (Обратите внимание на ссылку на класс **SqlCommand** ). В этих сценариях [Настройте запросы](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```
Дополнительные рекомендации по настройке производительности см. в следующих статьях:

* [Как поддерживать индексы и статистику Azure SQL](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Настройка производительности запросов вручную в базе данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Мониторинг производительности базы данных SQL Azure с помощью динамических административных представлений](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Управление хранилищем запросов в Базе данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)


## <a name="steps-to-fix-common-connection-issues"></a>Действия по устранению распространенных проблем с подключением

1. Убедитесь, что IP-адрес TCP включен в качестве клиентского протокола на сервере приложений. Дополнительные сведения см. в разделе [Настройка клиентских протоколов](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). На серверах приложений, на которых не установлены средства SQL Server, убедитесь, что IP-адрес TCP включен, выполнив **cliconfg. exe** (служебная программа для клиента SQL Server). 
2. Проверьте строку подключения приложения и убедитесь, что она настроена правильно. Например, убедитесь, что в строке подключения указан правильный порт (1433) и полное имя сервера.
См. раздел [Получение сведений о подключении SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Попробуйте увеличить **время ожидания**подключения. Корпорация Майкрософт рекомендует использовать время ожидания подключения не менее 30 секунд. 
4. Проверьте подключение между сервером приложений и базой данных SQL Azure с помощью [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms), файла UDL, проверки связи и Telnet. Дополнительные сведения см. в статьях [Устранение неполадок подключения SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) и [Диагностика проблем с подключением](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics).

   > [!NOTE]
   > В качестве шага устранения неполадок можно также попробовать проверить подключение на другом клиентском компьютере.

5. Рекомендуется обеспечивать логику повторных попыток. Дополнительные сведения о логике повторных попыток см. [в разделе Устранение временных сбоев и ошибок подключения к базе данных SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

Если описанные выше действия не помогли устранить проблему, попробуйте получить дополнительные данные и обратитесь в службу поддержки. Если приложение является облачной службой, включите ведение журнала. Этот шаг возвращает метку времени ошибки в формате UTC. Кроме того, SQL Azure возвращает идентификатор трассировки. Эти сведения могут использовать [службы поддержки пользователей Майкрософт](http://azure.microsoft.com/support/options/) . 

Дополнительные сведения о том, как включить ведение журнала, см. [в статье Включение ведения журнала диагностики для веб-приложений в службе приложений Azure](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

**Связанные документы**

* [Azure SQL Connectivity Architecture](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture) (Архитектура подключения к SQL Azure)<br>
* [Управление доступом к сети для базы данных SQL Azure и хранилища данных](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)