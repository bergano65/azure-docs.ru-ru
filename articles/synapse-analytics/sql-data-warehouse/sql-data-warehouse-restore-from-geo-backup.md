---
title: Восстановление хранилища данных из гео-резервного копирования
description: Как руководство по геовосстановлению пула S'L.
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
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745073"
---
# <a name="geo-restore-for-sql-pool"></a>Гео-восстановление для бассейна S'L

В этой статье вы научитесь восстанавливать свой пул s'L из гео-резервного копирования через портал Azure и PowerShell.

## <a name="before-you-begin"></a>Подготовка к работе

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Проверьте ресурсы DTU.** Каждый пул S'L размещается сервером S'L (например, myserver.database.windows.net), который имеет по умолчанию квоту DTU. Удостоверьте, что сервер S'L имеет достаточно оставшихся квот DTU для восстановления базы данных. Чтобы узнать, как вычислить необходимое количество DTU или запросить дополнительные единицы DTU, ознакомьтесь с разделом [Создание запроса в службу поддержки для хранилища данных SQL](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Восстановление из географического региона Лазурного региона через PowerShell

Для восстановления от гео-резервного копирования, используйте [Get-AzSqlDatabaseGeoBack up](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) и [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) cmdlet.

> [!NOTE]
> Можно выполнить геовосстановление в хранилище 2-го поколения! Для этого в качестве необязательного параметра укажите имя ServiceObjectiveName 2-го поколения (например, DW1000**c**).
>

1. Перед тем, как начать, не забудьте [установить Azure PowerShell.](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
2. Откройте PowerShell.
3. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.
4. Выберите подписку, содержащую хранилище данных, подкоторыминое восстановление.
5. Получите хранилище данных, который вы хотите восстановить.
6. Создайте запрос на восстановление хранилища данных.
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

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Восстановление из географического региона Лазурного региона через портал Azure

Выполните последующие действия, изложенные ниже, чтобы восстановить пул S'L из гео-резервного копирования:

1. Вопийте на свой [портал Azure.](https://portal.azure.com/)
2. Щелкните **+ Create a resource** (+ Создать ресурс).

   ![Новый DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Нажмите **на базы данных,** а затем — аналитика Azure Synapse (ранее S'L DW).

   ![Новый DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Заполните информацию, запрашиваемую во вкладке **Основы,** и нажмите **Далее: Дополнительные настройки.**

   ![Основы](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. Для **использования существующего** параметра данных выберите **резервное копирование** и выберите соответствующую резервную приготовитечку из параметров прокрутки вниз. Нажмите **Обзор и Создайте**.

   ![резервная копия](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. После восстановления хранилища данных убедитесь, что **статус** находится в сети.

## <a name="next-steps"></a>Next Steps

- [Восстановление существующего пула S'L](sql-data-warehouse-restore-active-paused-dw.md)
- [Восстановление удаленного пула S'L](sql-data-warehouse-restore-deleted-dw.md)
