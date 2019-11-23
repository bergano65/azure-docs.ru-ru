---
title: Data Warehouse Units (DWUs) in Azure Synapse Analytics (formerly SQL DW)
description: Recommendations on choosing the ideal number of data warehouse units (DWUs) to optimize price and performance, and how to change the number of units.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7cd6a037f339f193f63cbe152f0ea9964679c231
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420493"
---
# <a name="data-warehouse-units-dwus"></a>Data Warehouse Units (DWUs)

Recommendations on choosing the ideal number of data warehouse units (DWUs) to optimize price and performance, and how to change the number of units.

## <a name="what-are-data-warehouse-units"></a>What are Data Warehouse Units

A [SQL pool](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) represents a collection of analytic resources that are being provisioned when using [SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse). Analytic resources are defined as a combination of CPU, memory and IO. These three resources are bundled into units of compute scale called Data Warehouse Units (DWUs). Единица DWU представляет собой абстрактный, нормализованный объем вычислительных ресурсов и производительности. A change to your service level alters the number of DWUs that are available to the system, which in turn adjusts the performance, and the cost, of your system.

For higher performance, you can increase the number of data warehouse units. For less performance, reduce data warehouse units. Использование хранилища и операции вычисления оплачиваются отдельно, независимо от изменений количества единиц использования хранилища данных.

Performance for data warehouse units is based on these workload metrics:

- How fast a standard data warehousing query can scan a large number of rows and then perform a complex aggregation. Это требует интенсивного использования операций ввода-вывода и ресурсов ЦП.
- How fast the data warehouse can ingest data from Azure Storage Blobs or Azure Data Lake. Это сетевая операция, которая требует интенсивного использования ресурсов ЦП.
- How fast the [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL command can copy a table. Эта операция предусматривает чтение данных из хранилища, их распространение в различные узлы устройства и повторную запись в хранилище. Эта операция выполняется ЦП и требует интенсивного использования операций ввода-вывода и сетевых ресурсов.

При увеличении единиц DWU происходит следующее:

- линейное изменение производительности системы для операций сканирования, агрегирования и инструкций CTAS;
- увеличение количества модулей чтения и записи при загрузке с помощью PolyBase;
- увеличение максимального количества параллельных запросов и слотов выдачи.

## <a name="service-level-objective"></a>Целевой уровень обслуживания

Цель уровня обслуживания (SLO) — это параметр масштабируемости, который определяет уровень затрат и производительности хранилища данных. The service levels for Gen2 SQL pool are measured in data warehouse units (DWU), for example DW2000c.

In T-SQL, the SERVICE_OBJECTIVE setting determines the service level for your SQL pool.

```sql
CREATE DATABASE mySQLDW
( EDITION = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="capacity-limits"></a>Ограничения емкости

Для каждого сервера SQL (например, myserver.database.windows.net) предусмотрена квота [DTU (единицы передачи данных)](../sql-database/sql-database-what-is-a-dtu.md), которая позволяет применить только определенное число единиц использования хранилища данных. Дополнительные сведения см. в статье об [ограничениях емкости для управления рабочей нагрузкой](sql-data-warehouse-service-capacity-limits.md#workload-management).

## <a name="how-many-data-warehouse-units-do-i-need"></a>How many data warehouse units do I need

Оптимальное количество единиц использования хранилища данных существенно зависит от рабочей нагрузки и объема данных, загруженных в систему.

Шаги для поиска оптимального количества единиц DWU рабочей нагрузки:

1. Начните, выбрав меньшее число DWU.
2. Отслеживайте производительность приложения по мере загрузки тестовых данных в систему, сравнивая ее с количеством выбранных DWU.
3. Определите любые дополнительные требования для периодов пиковой активности. Workloads that show significant peaks and troughs in activity may need to be scaled frequently.

SQL Analytics is a scale-out system that can provision vast amounts of compute and query sizeable quantities of data. Чтобы ознакомиться с реальными возможностями масштабирования, особенно при использовании большого количества DWU, мы рекомендуем во время масштабирования выполнить масштабирование набора данных, чтобы проверить, достаточно ли используется данных для ЦП. Для проверки масштабирования мы рекомендуем использовать как минимум 1 TБ.

> [!NOTE]
>
> Высокий уровень параллелизма только увеличивает производительность обработки запросов, если нагрузку можно разделить между вычислительными узлами. Если обнаружится, что масштабирование не влияет на производительность, вам может потребоваться настроить конструктор таблиц или запросы. Инструкции по настройке запросов см. в статье [Памятка для хранилища данных SQL Azure](sql-data-warehouse-overview-manage-user-queries.md).

## <a name="permissions"></a>Разрешения

Чтобы изменить единицы использования хранилища данных, требуются разрешения, описанные в статье об [инструкции ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql).

Встроенные роли для ресурсов Azure, такие как участник базы данных SQL и участник SQL Server, позволяют изменять параметры DWU.

## <a name="view-current-dwu-settings"></a>для просмотра текущих параметров DWU;

Чтобы просмотреть текущие параметры DWU, сделайте следующее:

1. Откройте обозреватель объектов SQL Server в Visual Studio.
2. Подключитесь к базе данных master, связанной с логическим сервером Базы данных SQL.
3. Выберите в sys.database_service_objectives динамическое административное представление. Вот пример:

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Изменение единиц использования хранилища данных

### <a name="azure-portal"></a>портала Azure

To change DWUs:

1. Откройте [портал Azure](https://portal.azure.com), затем откройте нужную базу данных и щелкните **Масштаб**.

2. В колонке **Масштаб** передвиньте ползунок влево или вправо, чтобы изменить число единиц DWU.

3. В нижней части страницы нажмите кнопку **Save**. Появится сообщение с подтверждением. Щелкните **Да** для подтверждения или **Нет** для отмены.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

To change the DWUs, use the [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell cmdlet. The following example sets the service level objective to DW1000c for the database MySQLDW that is hosted on server MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Дополнительные сведения см. в статье [Использование командлетов PowerShell и интерфейсов REST API при работе с хранилищем данных SQL](sql-data-warehouse-reference-powershell-cmdlets.md).

### <a name="t-sql"></a>T-SQL

With T-SQL you can view the current DWU settings, change the settings, and check the progress.

Изменение DWU:

1. Подключитесь к базе данных master, связанной с логическим сервером Базы данных SQL.
2. Используйте оператор TSQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql). The following example sets the service level objective to DW1000c for the database MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

### <a name="rest-apis"></a>API-интерфейсы REST

Чтобы изменить число DWU, используйте REST API [создания или обновления базы данных](/rest/api/sql/databases/createorupdate). The following example sets the service level objective to DW1000c for the database MySQLDW, which is hosted on server MyServer. Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000c
    }
}
```

Дополнительные примеры REST API см. в статье [REST API для хранилища данных Azure SQL](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>Проверка состояния изменений единиц DWU

Изменение количества единиц DWU может занять несколько минут. При выполнении автоматического масштабирования можно реализовать логику, позволяющую приступать к выполнению другого действия только после завершения определенных операций.

Проверка состояния базы данных с использованием различных конечных точек позволяет правильно реализовать автоматизацию этих операций. На портале отображается уведомление о завершении операции и текущее состояние баз данных, однако здесь нельзя проверять состояние программным способом.

Вы не можете проверить состояние базы данных для операций масштабирования с помощью портала Azure.

Чтобы проверить состояние изменений DWU, сделайте следующее:

1. Подключитесь к базе данных master, связанной с логическим сервером Базы данных SQL.

1. Отправьте запрос ниже, чтобы проверить состояние базы данных.

    ```sql
    SELECT    *
    FROM      sys.databases
    ;
    ```
    
1. Отправьте запрос ниже, чтобы проверить состояние операции.

    ```sql
    SELECT    *
    FROM      sys.dm_operation_status
    WHERE     resource_type_desc = 'Database'
    AND       major_resource_id = 'MySQLDW'
    ;
    ```
    
This DMV returns information about various management operations on your SQL pool such as the operation and the state of the operation, which is either IN_PROGRESS or COMPLETED.

## <a name="the-scaling-workflow"></a>Масштабирование рабочего процесса

When you start a scale operation, the system first kills all open sessions, rolling back any open transactions to ensure a consistent state. Операции масштабирования можно выполнить только после завершения отката транзакций.  

- For a scale-up operation, the system detaches all compute nodes, provisions the additional compute nodes, and then reattaches to the storage layer.
- For a scale-down operation, the system detaches all compute nodes and then reattaches only the needed nodes to the storage layer.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об управлении производительностью см. в статьях [Классы ресурсов для управления рабочими нагрузками](resource-classes-for-workload-management.md) и [Memory and concurrency limits for Azure SQL Data Warehouse](memory-concurrency-limits.md) (Ограничения памяти и параллелизма для хранилища данных SQL Azure).
