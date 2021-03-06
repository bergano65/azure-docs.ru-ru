---
title: Единицы хранилища данных (Dwu) в Azure синапсе Analytics (прежнее название — SQL DW)
description: Рекомендации по выбору оптимального количества единиц использования хранилища данных (Dwu) для оптимизации цены и производительности, а также изменения числа единиц.
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
# <a name="data-warehouse-units-dwus"></a>Единицы хранилища данных (Dwu)

Рекомендации по выбору оптимального количества единиц использования хранилища данных (Dwu) для оптимизации цены и производительности, а также изменения числа единиц.

## <a name="what-are-data-warehouse-units"></a>Что такое единицы хранилища данных

[Пул SQL](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) представляет коллекцию аналитических ресурсов, которые подготавливаются при использовании [SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse). Аналитические ресурсы определяются как сочетание ЦП, памяти и операций ввода-вывода. Эти три ресурса объединены в единицы масштабирования вычислений, которые называются единицами использования хранилища данных (Dwu). Единица DWU представляет собой абстрактный, нормализованный объем вычислительных ресурсов и производительности. Изменение уровня обслуживания изменяет количество DWU, доступных системе, что, в свою очередь, корректирует производительность и стоимость системы.

Для повышения производительности можно увеличить число единиц использования хранилища данных. Для уменьшения производительности уменьшите число единиц хранилища данных. Использование хранилища и операции вычисления оплачиваются отдельно, независимо от изменений количества единиц использования хранилища данных.

Производительность единиц работы с хранилищем данных зависит от этих метрик рабочей нагрузки:

- Скорость, с которой стандартный запрос хранилища данных может сканировать большое количество строк, а затем выполнять комплексное агрегирование. Это требует интенсивного использования операций ввода-вывода и ресурсов ЦП.
- Скорость приема данных в хранилище данных из больших двоичных объектов службы хранилища Azure или Azure Data Lake. Это сетевая операция, которая требует интенсивного использования ресурсов ЦП.
- Скорость, с которой команда t-SQL [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) может копировать таблицу. Эта операция предусматривает чтение данных из хранилища, их распространение в различные узлы устройства и повторную запись в хранилище. Эта операция выполняется ЦП и требует интенсивного использования операций ввода-вывода и сетевых ресурсов.

При увеличении единиц DWU происходит следующее:

- линейное изменение производительности системы для операций сканирования, агрегирования и инструкций CTAS;
- увеличение количества модулей чтения и записи при загрузке с помощью PolyBase;
- увеличение максимального количества параллельных запросов и слотов выдачи.

## <a name="service-level-objective"></a>Целевой уровень обслуживания

Цель уровня обслуживания (SLO) — это параметр масштабируемости, который определяет уровень затрат и производительности хранилища данных. Уровни обслуживания для пула SQL Gen2 измеряются в единицах хранилища данных (DWU), например DW2000c.

В T-SQL параметр SERVICE_OBJECTIVE определяет уровень обслуживания для пула SQL.

```sql
CREATE DATABASE mySQLDW
( EDITION = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="capacity-limits"></a>Ограничения емкости

Для каждого сервера SQL (например, myserver.database.windows.net) предусмотрена квота [DTU (единицы передачи данных)](../sql-database/sql-database-what-is-a-dtu.md), которая позволяет применить только определенное число единиц использования хранилища данных. Дополнительные сведения см. в статье об [ограничениях емкости для управления рабочей нагрузкой](sql-data-warehouse-service-capacity-limits.md#workload-management).

## <a name="how-many-data-warehouse-units-do-i-need"></a>Сколько единиц хранилища данных требуется

Оптимальное количество единиц использования хранилища данных существенно зависит от рабочей нагрузки и объема данных, загруженных в систему.

Шаги для поиска оптимального количества единиц DWU рабочей нагрузки:

1. Начните, выбрав меньшее число DWU.
2. Отслеживайте производительность приложения по мере загрузки тестовых данных в систему, сравнивая ее с количеством выбранных DWU.
3. Определите любые дополнительные требования для периодов пиковой активности. Рабочие нагрузки, отображающие значительные пиковые значения и спадов в действии, могут быть часто масштабированы.

SQL Analytics — это масштабируемая система, которая позволяет подготавливать большие объемы вычислений и запрашивать немалым количество данных. Чтобы ознакомиться с реальными возможностями масштабирования, особенно при использовании большого количества DWU, мы рекомендуем во время масштабирования выполнить масштабирование набора данных, чтобы проверить, достаточно ли используется данных для ЦП. Для проверки масштабирования мы рекомендуем использовать как минимум 1 TБ.

> [!NOTE]
>
> Высокий уровень параллелизма только увеличивает производительность обработки запросов, если нагрузку можно разделить между вычислительными узлами. Если обнаружится, что масштабирование не влияет на производительность, вам может потребоваться настроить конструктор таблиц или запросы. Инструкции по настройке запросов см. в статье [Памятка для хранилища данных SQL Azure](sql-data-warehouse-overview-manage-user-queries.md).

## <a name="permissions"></a>Разрешения

Чтобы изменить единицы использования хранилища данных, требуются разрешения, описанные в статье об [инструкции ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql).

Встроенные роли для ресурсов Azure, такие как участник базы данных SQL и участник SQL Server, позволяют изменять параметры DWU.

## <a name="view-current-dwu-settings"></a>Просмотр текущих параметров DWU

Чтобы просмотреть текущие параметры DWU, сделайте следующее:

1. Откройте обозреватель объектов SQL Server в Visual Studio.
2. Подключитесь к базе данных master, связанной с логическим сервером Базы данных SQL.
3. Выберите в sys.database_service_objectives динамическое административное представление. Пример:

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Изменение единиц использования хранилища данных

### <a name="azure-portal"></a>портале Azure

Чтобы изменить DWU, сделайте следующее:

1. Откройте [портал Azure](https://portal.azure.com), откройте нужную базу данных и щелкните **Масштаб**.

2. В колонке **Масштаб** передвиньте ползунок влево или вправо, чтобы изменить число единиц DWU.

3. Выберите команду **Сохранить**. Появится сообщение с подтверждением. Щелкните **Да** для подтверждения или **Нет** для отмены.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Чтобы изменить DWU, используйте командлет PowerShell [Set-азсклдатабасе](/powershell/module/az.sql/set-azsqldatabase) . В следующем примере задается цель уровня обслуживания DW1000c для базы данных MySQLDW, размещенной на сервере MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Дополнительные сведения см. в статье [Использование командлетов PowerShell и интерфейсов REST API при работе с хранилищем данных SQL](sql-data-warehouse-reference-powershell-cmdlets.md).

### <a name="t-sql"></a>T-SQL

С помощью T-SQL можно просмотреть текущие параметры DWU, изменить параметры и проверить ход выполнения.

Изменение DWU:

1. Подключитесь к базе данных master, связанной с логическим сервером Базы данных SQL.
2. Используйте оператор TSQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) . В следующем примере задается цель уровня обслуживания DW1000c для базы данных MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

### <a name="rest-apis"></a>Интерфейсы API REST

Чтобы изменить число единиц DWU, используйте REST API для [создания или обновления базы данных](/rest/api/sql/databases/createorupdate) . В следующем примере устанавливается цель уровня обслуживания DW1000c для базы данных MySQLDW, которая размещается на сервере MyServer. Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

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
    
Это динамическое административное представление возвращает сведения о различных операциях управления в пуле SQL, таких как операция и состояние операции, которая либо IN_PROGRESS, либо завершена.

## <a name="the-scaling-workflow"></a>Масштабирование рабочего процесса

При запуске операции масштабирования система сначала завершает все открытые сеансы и откатывает все открытые транзакции, чтобы обеспечить целостность состояния. Операции масштабирования можно выполнить только после завершения отката транзакций.  

- Для операции масштабирования система отсоединяет все расчетные узлы, подготавливает дополнительные расчетные узлы, а затем повторно подключается к уровню хранилища.
- Для операции уменьшения масштаба система отсоединяет все расчетные узлы, а затем повторно подключает только необходимые узлы к уровню хранилища.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об управлении производительностью см. в статьях [Классы ресурсов для управления рабочими нагрузками](resource-classes-for-workload-management.md) и [Memory and concurrency limits for Azure SQL Data Warehouse](memory-concurrency-limits.md) (Ограничения памяти и параллелизма для хранилища данных SQL Azure).
