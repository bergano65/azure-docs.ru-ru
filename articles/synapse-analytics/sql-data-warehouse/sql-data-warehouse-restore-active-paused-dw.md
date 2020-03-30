---
title: Восстановление существующего хранилища данных
description: Как руководство по восстановлению существующего пула S'L.
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
ms.openlocfilehash: 635b65d16ae9a59816506023d323243f043ce7da
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350386"
---
# <a name="restore-an-existing-sql-pool"></a>Восстановление существующего пула S'L

В этой статье вы узнаете, как восстановить существующий пул S'L в Azure Synapse Analytics с помощью портала Azure и PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Проверьте ресурсы DTU.** Каждый пул размещается сервером S'L (например, myserver.database.windows.net), который имеет по умолчанию квоту DTU. Проверка сервера S'L имеет достаточно оставшихся dTU квоты для базы данных восстанавливается. Чтобы узнать, как вычислить необходимое количество DTU или запросить дополнительные единицы DTU, ознакомьтесь с разделом [Создание запроса в службу поддержки для хранилища данных SQL](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Перед началом

1. Убедитесь в том, чтобы [установить Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Имейте существующую точку восстановления, которую вы хотите восстановить. Если требуется создать новое восстановление, [см.](sql-data-warehouse-restore-points.md)

## <a name="restore-an-existing-sql-pool-through-powershell"></a>Восстановление существующего пула S'L через PowerShell

Для восстановления существующего пула S'L из точки восстановления используйте смдлет [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) PowerShell.

1. Откройте PowerShell.

2. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.

3. Выберите подписку, содержащую базу данных, которую нужно восстановить.

4. Перечислите точки восстановления для пула S'L.

5. Выберите нужные точки восстановления с помощью свойства RestorePointCreationDate.

6. Восстановить пул S'L в нужной точке восстановления с помощью [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) PowerShell cmdlet.
        1. Чтобы восстановить пул S'L на другом логическом сервере, не забудьте указать другое логическое имя сервера.  Этот логический сервер также может находиться в другой группе ресурсов и регионе.
        2. Чтобы восстановить другую подписку, используйте кнопку "Движение", чтобы переместить логический сервер в другую подписку.

7. Убедитесь, что восстановленный пул S'L находится в сети.

8. После завершения восстановления можно настроить восстановленный пул S'L, после [настройки базы данных после восстановления.](../../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)

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

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceTargetGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-sql-pool-through-the-azure-portal"></a>Восстановление существующего пула S'L через портал Azure

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Перейдите к пулу S'L, который вы хотите восстановить.
3. В верхней области колонки обзора выберите **Восстановить**.

    ![ Обзор восстановления](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Выберите **Точки автоматического восстановления** или **Определенные пользователем точки восстановления**. Если пул S'L не имеет автоматических точек восстановления, подождите несколько часов или создайте определенную пользователем точку восстановления перед восстановлением. Для очков восстановления, определяемых пользователем, выберите существующую или создайте новую. Для **Server**можно выбрать логический сервер в другой группе ресурсов и регионе или создать новый. После предоставления всех параметров, нажмите **Обзор и Восстановление**.

    ![Точки автоматического восстановления](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Next Steps
- [Восстановление удаленного пула S'L](sql-data-warehouse-restore-deleted-dw.md)
- [Восстановление из пула георезервного копирования S'L](sql-data-warehouse-restore-from-geo-backup.md)

 