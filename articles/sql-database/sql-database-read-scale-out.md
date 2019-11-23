---
title: Read queries on replicas
description: The Azure SQL Database provides the ability to load-balance read-only workloads using the capacity of read-only replicas - called Read Scale-Out.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: f111b19eb07c218a9f3250ef3ffdb8a97cf07542
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420729"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Use read-only replicas to load-balance read-only query workloads

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

As part of the [High Availability architecture](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability), each database in the Premium and Business Critical service tier is automatically provisioned with a primary replica and several secondary replicas. The secondary replicas are provisioned with the same compute size as the primary replica. The **Read Scale-Out** feature allows you to load-balance SQL Database read-only workloads using the capacity of one of the read-only replicas instead of sharing the read-write replica. Таким образом рабочая нагрузка только для чтения изолируется от главных рабочих нагрузок чтения и записи и не влияет на их производительность. The feature is intended for the applications that include logically separated read-only workloads, such as analytics. In the Premium and Business Critical service tiers, applications could gain performance benefits using this additional capacity at no extra cost.

The **Read Scale-Out** feature is also available in the Hyperscale service tier when at least one secondary replica is created. Multiple secondary replicas can be used if read-only workloads require more resources than available on one secondary replica. The High Availability architecture of Basic, Standard, and General Purpose service tiers does not include any replicas. The **Read Scale-Out** feature is not available in these service tiers.

The following diagram illustrates it using a Business Critical database.

![Реплики только для чтения](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

The Read Scale-Out feature is enabled by default on new Premium,  Business Critical, and Hyperscale databases. For Hyperscale, one secondary replica is created by default for new databases. If your SQL connection string is configured with `ApplicationIntent=ReadOnly`, the application will be redirected by the gateway to a read-only replica of that database. For information on how to use the `ApplicationIntent` property, see [Specifying Application Intent](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

If you wish to ensure that the application connects to the primary replica regardless of the `ApplicationIntent` setting in the SQL connection string, you must explicitly disable read scale-out when creating the database or when altering its configuration. For example, if you upgrade your database from Standard or General Purpose tier to Premium, Business Critical or Hyperscale tier and want to make sure all your connections continue to go to the primary replica, disable Read Scale-out. For details on how to disable it, see [Enable and disable Read Scale-Out](#enable-and-disable-read-scale-out).

> [!NOTE]
> Query Data Store, Extended Events, SQL Profiler and Audit features are not supported on the read-only replicas.

## <a name="data-consistency"></a>Согласованность данных

Одним из преимуществ реплик является то, что реплики всегда находятся в состоянии транзакционной согласованности, но в разные моменты времени может возникать небольшая задержка между различными репликами. Горизонтальное масштабирование для чтения поддерживает согласованность на уровне сеанса. It means, if the read-only session reconnects after a connection error caused by replica unavailability, it may be redirected to a replica that is not 100% up-to-date with the read-write replica. Likewise, if an application writes data using a read-write session and immediately reads it using a read-only session, it is possible that the latest updates are not immediately visible on the replica. The latency is caused by an asynchronous transaction log redo operation.

> [!NOTE]
> Задержки репликации в пределах региона случаются крайне редко.

## <a name="connect-to-a-read-only-replica"></a>Подключение к реплике только для чтения

При включении для базы данных горизонтального масштабирования для чтения предоставляемый клиентом параметр `ApplicationIntent` в строке подключения определяет, куда направляется подключение: к реплике для записи или к реплике только для чтения. В частности, если значение `ApplicationIntent` — `ReadWrite` (по умолчанию), подключение направляется к реплике базы данных для чтения и записи. Также происходит и без этой функции. Если значение `ApplicationIntent` — `ReadOnly`, подключение направляется к реплике, доступной только для чтения.

Например, следующая строка подключения позволяет подключить клиента к реплике только для чтения (замените элементы в угловых скобках правильными значениями для среды и удалите угловые скобки):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Обе приведенные ниже строки подключения подключают подключить клиента к реплике для чтения и записи (замените элементы в угловых скобках правильными значениями для среды и удалите угловые скобки):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Убедитесь, что подключение выполняется к реплике только для чтения

Можно проверить, подключены ли вы к реплике только для чтения, выполнив приведенный ниже запрос. Он возвращает READ_ONLY при подключении к реплике только для чтения.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> В отдельно взятый момент времени только одна из реплик AlwaysON доступна в сеансах только для чтения.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Monitoring and troubleshooting read-only replica

When connected to a read-only replica, you can access the performance metrics using the `sys.dm_db_resource_stats` DMV. To access query plan statistics, use the `sys.dm_exec_query_stats`, `sys.dm_exec_query_plan` and `sys.dm_exec_sql_text` DMVs.

> [!NOTE]
> The DMV `sys.resource_stats` in the logical master database returns CPU usage and storage data of the primary replica.

## <a name="enable-and-disable-read-scale-out"></a>Включение и отключение горизонтального масштабирования для чтения

Read Scale-Out is enabled by default on Premium, Business Critical and Hyperscale service tiers. Read Scale-Out cannot be enabled in Basic, Standard, or General Purpose service tiers. Read Scale-Out is automatically disabled on Hyperscale databases configured with 0 replicas.

You can disable and re-enable Read Scale-Out on single databases and elastic pool databases in Premium or Business Critical service tier using the following methods.

> [!NOTE]
> The ability to disable Read Scale-Out is provided for backward compatibility.

### <a name="azure-portal"></a>портала Azure

You can manage the Read Scale-out setting on the **Configure** database blade.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

Для управления горизонтальным масштабированием для чтения в Azure PowerShell требуется выпуск Azure PowerShell за декабрь 2016 года или более поздней версии. Последнюю версию Azure PowerShell см. [здесь](https://docs.microsoft.com/powershell/azure/install-az-ps).

You can disable or re-enable Read Scale-Out in Azure PowerShell by invoking the [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet and passing in the desired value – `Enabled` or `Disabled` -- for the `-ReadScale` parameter.

To disable read scale-out on an existing database (replacing the items in the angle brackets with the correct values for your environment and dropping the angle brackets):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

To disable read scale-out on a new database (replacing the items in the angle brackets with the correct values for your environment and dropping the angle brackets):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

To re-enable read scale-out on an existing database (replacing the items in the angle brackets with the correct values for your environment and dropping the angle brackets):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

To create a database with read scale-out disabled, or to change the setting for an existing database, use the following method with the `readScale` property set to `Enabled` or `Disabled` as in the below sample request.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Дополнительные сведения см. в статье о [создании или обновлении базы данных](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Using TempDB on read-only replica

The TempDB database is not replicated to the read-only replicas. Each replica has its own version of TempDB database that is created when the replica is created. It ensures that TempDB is updateable and can be modified during your query execution. If your read-only workload depends on using TempDB objects, you should create these objects as part of your query script.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Использование горизонтального масштабирования для чтения с геореплицированными базами данных

If you are using Read Scale-Out to load-balance read-only workloads on a database that is geo-replicated (for example, as a member of a failover group), make sure that read scale-out is enabled on both the primary and the geo-replicated secondary databases. This configuration will ensure that the same load-balancing experience continues when your application connects to the new primary after failover. Если подключение к геореплицированной базе данных-получателю выполняется с включенной функцией масштабирования для чтения, ваши сеансы с `ApplicationIntent=ReadOnly` будут направляться в одну из реплик, так же как и в случае с маршрутизацией подключений к базе данных-источнику.  Сеансы без `ApplicationIntent=ReadOnly` будут направляться в первичную реплику геореплицированной базы данных-получателя (тоже только для чтения). Because geo-replicated secondary database has a different endpoint than the primary database, historically to access the secondary it wasn't required to set `ApplicationIntent=ReadOnly`. Для обеспечения обратной совместимости в динамическом административном представлении `sys.geo_replication_links` отображается `secondary_allow_connections=2` (разрешены любые подключения клиента).

> [!NOTE]
> Round-robin or any other load-balanced routing between the local replicas of the secondary database is not supported.

## <a name="next-steps"></a>Дальнейшие действия

- For information about SQL Database Hyperscale offering, see [Hyperscale service tier](./sql-database-service-tier-hyperscale.md).
