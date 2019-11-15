---
title: Пример для PowerShell. Обновление схемы синхронизации компонента "Синхронизация данных SQL"
description: Пример скрипта Azure PowerShell для обновления схемы синхронизации компонента "Синхронизация данных SQL"
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: d8c4f21630afa4a57d3c3886819ec2842e3cb681
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691465"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Использование PowerShell для обновления схемы синхронизации в существующей группе синхронизации

Этот пример скрипта PowerShell обновляет схему синхронизации в существующей группе синхронизации компонента "Синхронизация данных SQL". Если вы синхронизируете несколько таблиц, этот скрипт поможет вам эффективно обновить схему синхронизации. В этом примере показано использование скрипта **UpdateSyncSchema**, который можно найти в репозитории GitHub (файл [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1)).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать PowerShell локально, для работы с этим руководством вам понадобится AZ PowerShell 1.4.0 или более поздней версии. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

Общие сведения о синхронизации данных SQL см. в статье [Синхронизация данных в нескольких облачных и локальных базах данных с помощью синхронизации данных SQL Azure](../sql-database-sync-data.md).

> [!IMPORTANT]
> Служба "Синхронизация данных SQL Azure" пока **не** поддерживает Управляемый экземпляры Базы данных SQL Azure.

## <a name="sample-script"></a>Пример скрипта

### <a name="example-1---add-all-tables-to-the-sync-schema"></a>Пример 1. Добавление всех таблиц в схему синхронизации

Следующий пример обновляет схему базы данных и добавляет все допустимые таблицы в центральную базу данных для синхронизации схемы.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="example-2---add-and-remove-tables-and-columns"></a>Пример 2. Добавление и удаление таблиц и столбцов

Следующий пример добавляет `[dbo].[Table1]` и `[dbo].[Table2].[Column1]` в схему синхронизации и удаляет `[dbo].[Table3]`.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Параметры скрипта

Скрипт **UpdateSyncSchema** содержит следующие параметры:

| Параметр | Примечания |
|---|---|
| $SubscriptionId | Подписка, в которой создана группа синхронизации. |
| $ResourceGroupName | Группа ресурсов, в которой создана группа синхронизации.|
| $ServerName | Имя сервера центральной базы данных.|
| $DatabaseName | Имя центральной базы данных. |
| $SyncGroupName | Имя группы синхронизации |
| $MemberName | Укажите имя элемента, если вы хотите загрузить схему базы данных из элемента синхронизации, а не из центральной базы данных. Если вы хотите загрузить схему базы данных из центральной базы данных, оставьте этот параметр пустым. |
| $TimeoutInSeconds | Время ожидания до того, как скрипт обновит схему базы данных. По умолчанию это 900 секунд. |
| $RefreshDatabaseSchema | Укажите, должен ли скрипт обновлять схему базы данных. Если предыдущая конфигурация схемы базы данных была изменена (например, при добавлении новой таблицы или нового столбца), вам необходимо обновить схему до ее перенастройки. Значение по умолчанию — false. |
| $AddAllTables | Если это значение равно true, все допустимые таблицы и столбцы добавляются в схему синхронизации. Значения $TablesAndColumnsToAdd и $TablesAndColumnsToRemove игнорируются. |
| $TablesAndColumnsToAdd | Укажите таблицы или столбцы для добавления в схему синхронизации. Имя каждой таблицы или каждого столбца должно содержать имя схемы. Например: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Чтобы указать несколько имен таблиц или столбцов, разделите их запятыми (,). |
| $TablesAndColumnsToRemove | Укажите таблицы или столбцы для удаления из схемы синхронизации. Имя каждой таблицы или каждого столбца должно содержать имя схемы. Например: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Чтобы указать несколько имен таблиц или столбцов, разделите их запятыми (,). |
|||

## <a name="script-explanation"></a>Описание скрипта

Скрипт **UpdateSyncSchema** содержит следующие команды: Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [Get-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncgroup) | Возвращает сведения о группе синхронизации. |
| [Update-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncgroup) | Создает группу синхронизации. |
| [Get-AzSqlSyncMember](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncmember) | Возвращает сведения об элементе синхронизации. |
| [Get-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncschema) | Возвращает сведения о схеме синхронизации. |
| [Update-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncschema) | Обновляет схему синхронизации. |
|||

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для Базы данных SQL Azure можно найти в разделе [Примеры Azure PowerShell для Базы данных SQL Azure](../sql-database-powershell-samples.md).

Дополнительные сведения о синхронизации данных SQL:

-   Обзор: [Синхронизация данных в нескольких облачных и локальных базах данных с помощью функции синхронизации данных SQL Azure](../sql-database-sync-data.md).
-   Настройка синхронизации данных
    - На портале: [Руководство по настройке синхронизации данных SQL между базой данных SQL Azure и локальной базой данных SQL Server](../sql-database-get-started-sql-data-sync.md)
    - С помощью PowerShell
        -  [Использование PowerShell для синхронизации данных между несколькими базами данных SQL Azure](sql-database-sync-data-between-sql-databases.md)
        -  [Использование PowerShell для синхронизации данных между базой данных SQL Azure и локальной базой данных SQL Server](sql-database-sync-data-between-azure-onprem.md)
-   Агент синхронизации данных: [Агент синхронизации данных для синхронизации данных SQL Azure](../sql-database-data-sync-agent.md).
-   Рекомендации: [Рекомендации по синхронизации данных SQL](../sql-database-best-practices-data-sync.md).
-   Мониторинг: [Мониторинг синхронизации данных SQL с помощью журналов Azure Monitor](../sql-database-sync-monitor-oms.md)
-   Устранение неполадок: [Устранение неполадок с синхронизацией данных SQL](../sql-database-troubleshoot-data-sync.md).
-   Обновление схемы синхронизации
    -   С помощью Transact-SQL: [Автоматическая репликация изменений схемы при синхронизации данных SQL Azure](../sql-database-update-sync-schema.md).

Дополнительные сведения о Базе данных SQL:

-   [Обзор Базы данных SQL](../sql-database-technical-overview.md)
-   [Управление жизненным циклом базы данных](https://msdn.microsoft.com/library/jj907294.aspx)
