---
title: Восстановление удаленного пула S'L
description: Как руководство для восстановления удаленного пула S'L.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 251fdb83e848aaac3a5391320df23149ce1bce33
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633048"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Восстановление удаленного пула S'L с помощью аналитики Azure Synapse

В этой статье вы научитесь восстанавливать S'L с помощью портала Azure или PowerShell.

## <a name="before-you-begin"></a>Подготовка к работе

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Проверьте ресурсы DTU.** Каждый пул S'L размещается сервером S'L (например, myserver.database.windows.net), который имеет по умолчанию квоту DTU.  Удостоверьте, что сервер S'L имеет достаточно оставшихся квот DTU для восстановления базы данных. Чтобы узнать, как вычислить необходимое количество DTU или запросить дополнительные единицы DTU, ознакомьтесь с разделом [Создание запроса в службу поддержки для хранилища данных SQL](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Восстановление удаленного хранилища данных через PowerShell

Для восстановления удаленного пула S'L используйте cmdlet [Restore-AzSqlDatabase.](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) Если соответствующий логический сервер также был удален, вы не можете восстановить этот хранилище данных.

1. Перед тем, как начать, не забудьте [установить Azure PowerShell.](https://docs.microsoft.com/powershell/azure/overview)
2. Откройте PowerShell.
3. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.
4. Выберите подписку, содержащую удаленный хранилище данных, который будет восстановлен.
5. Получите специальный удаленный хранилище данных.
6. Восстановление удаленного хранилища данных
    1. Чтобы восстановить удаленный хранилище данных S'L на другом логическом сервере, не забудьте указать другое логическое имя сервера.  Этот логический сервер также может находиться в другой группе ресурсов и регионе.
    1. Чтобы восстановить другую подписку, используйте кнопку [Move](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) для перемещения логического сервера в другую подписку.
7. Убедитесь, что восстановленный хранилище данных находится в режиме онлайн.
8. После завершения восстановления можно настроить восстановленный хранилище данных, после [настройки базы данных после восстановления.](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Восстановление удаленной базы данных на портале Azure

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Перейдите на сервер S'L, на котором размещался удаленный хранилище данных.
3. Выберите значок **удаленных баз данных** в таблице содержимого.

    ![Удаленные базы данных](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Выберите удаленный хранилище данных S'L, который необходимо восстановить.

    ![Выбор удаленных баз данных](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Указать новое **имя базы данных** и нажмите **OK**

    ![Указание нового имени базы данных](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Next Steps

- [Восстановление существующего пула S'L](sql-data-warehouse-restore-active-paused-dw.md)
- [Восстановление из пула георезервного копирования S'L](sql-data-warehouse-restore-from-geo-backup.md)
