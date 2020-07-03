---
title: Восстановление хранилища данных из географической резервной копии
description: Руководство по географическиму восстановлению пула SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7e0980a9142dc966916d5a4df898ea53b0ddeae5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745073"
---
# <a name="geo-restore-for-sql-pool"></a>Геовосстановление для пула SQL

Из этой статьи вы узнаете, как восстановить пул SQL из географической резервной копии с помощью портал Azure и PowerShell.

## <a name="before-you-begin"></a>Подготовка к работе

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Проверьте ресурсы DTU.** Каждый пул SQL размещается на сервере SQL Server (например, myserver.database.windows.net), который имеет квоту DTU по умолчанию. Убедитесь, что SQL Server имеет достаточное количество оставшихся квот DTU для восстанавливаемой базы данных. Чтобы узнать, как вычислить необходимое количество DTU или запросить дополнительные единицы DTU, ознакомьтесь с разделом [Создание запроса в службу поддержки для хранилища данных SQL](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Восстановление из географического региона Azure с помощью PowerShell

Чтобы выполнить восстановление из географической резервной копии, используйте командлет [Get-азсклдатабасежеобаккуп](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) и [RESTORE-азсклдатабасе](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

> [!NOTE]
> Можно выполнить геовосстановление в хранилище 2-го поколения! Для этого в качестве необязательного параметра укажите имя ServiceObjectiveName 2-го поколения (например, DW1000**c**).
>

1. Перед началом убедитесь, что [установлен Azure PowerShell](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Откройте PowerShell.
3. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.
4. Выберите подписку, содержащую восстанавливаемое хранилище данных.
5. Получите хранилище данных, которое необходимо восстановить.
6. Создайте запрос восстановления для хранилища данных.
7. Проверьте состояние геовосстановленного хранилища данных.
8. Чтобы настроить хранилище данных после восстановления, см. раздел [Настройка базы данных после восстановления]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

Восстановленная база данных будет поддерживать прозрачное шифрование данных, если исходная база данных поддерживает прозрачное шифрование данных.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Восстановление из географического региона Azure с помощью портал Azure

Выполните действия, описанные ниже, чтобы восстановить пул SQL из географической резервной копии.

1. Войдите в учетную запись [портал Azure](https://portal.azure.com/) .
2. Щелкните **+ Create a resource** (+ Создать ресурс).

   ![Создать хранилище данных](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Щелкните **базы данных** , а затем * * Azure синапсе Analytics (ранее SQL DW) * *.

   ![Новый DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Заполните сведения, запрошенные на вкладке " **основы** ", и нажмите кнопку " **Далее": дополнительные параметры**.

   ![Основы](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. Для параметра **использовать существующий параметр данных** выберите **резервное копирование** и выберите соответствующую резервную копию в параметрах прокрутки вниз. Щелкните **проверить и создать**.

   ![резервная копия](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. После восстановления хранилища данных убедитесь, что **состояние** находится в сети.

## <a name="next-steps"></a>Дальнейшие шаги

- [Восстановление существующего пула SQL](sql-data-warehouse-restore-active-paused-dw.md)
- [Восстановление удаленного пула SQL](sql-data-warehouse-restore-deleted-dw.md)
