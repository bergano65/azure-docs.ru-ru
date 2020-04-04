---
title: PowerShell & REST APIs
description: Найдите верхние cmdlets PowerShell для пула Azure Synapse Analytics S'L, включая способы приостановки и возобновления базы данных.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 644ad931879c1953560abf13ac0eda58f3d814d8
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633066"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>PowerShell & REST AAP для пула Azure Synapse Analytics S'L

Многие административные задачи пула Azure Synapse Analytics S'L можно управлять с помощью cmdlets Azure PowerShell или REST AIS.  Ниже приведены некоторые примеры того, как использовать команды PowerShell для автоматизации общих задач в вашем пуле S'L.  Хорошие примеры использования REST приведены в статье [Управление вычислительными ресурсами в хранилище данных SQL Azure (REST)](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Приступая к работе с командлетами Azure PowerShell

1. Откройте Windows PowerShell.
2. В командной строке PowerShell выполните приведенные далее команды, чтобы войти в Azure Resource Manager Azure и выбрать свою подписку.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Пример хранилища данных паузы

Приостанавливает базу данных с именем Database02, размещенную на сервере с именем Server01.  Сервер находится в группе ресурсов Azure с именем ResourceGroup1.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Вариация, этот пример трубы извлеченный объект [suspend-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase).  В результате база данных приостанавливается. Последняя команда отображает результаты.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Запуск примера хранилища данных

Возобновляет работу базы данных с именем Database02, размещенную на сервере с именем Server01. Сервер находится в группе ресурсов с именем ResourceGroup1.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Как вариант, в этом примере извлекается база данных с именем Database02 с сервера Server01, который находится в группе ресурсов с именем ResourceGroup1. Он просажёт извлеченный объект в [Resume-AzSqlDatabase.](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Обратите внимание, что если вашим сервером является foo.database.windows.net, в командлетах PowerShell в качестве -ServerName используйте значение "foo".

## <a name="other-supported-powershell-cmdlets"></a>Другие поддерживаемые командлеты PowerShell

Эти cmdlets PowerShell поддерживаются хранилищем данных Azure Synapse Analytics.

* [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/get-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Новый-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* «Restore-AzSqlDatabase/powershell/module/az.sql/restore-azsqldatabase?toc/azure/synapse/synapse&/synapse/breadsldatabase/breadcrumb/toc.json)
* [Резюме-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Приостановить-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные примеры PowerShell см. в указанных далее документах.

* [Создание хранилища данных с помощью PowerShell](create-data-warehouse-powershell.md)
* [Восстановление базы данных](sql-data-warehouse-restore-points.md)

Для других задач, которые могут быть автоматизированы с PowerShell, с&м. Не все cmdlets базы данных Azure S'L поддерживаются для хранилища данных Azure Synapse Analytics. Список задач, которые можно автоматизировать [Operations for Azure SQL Database](/rest/api/sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)с помощью REST, см.
