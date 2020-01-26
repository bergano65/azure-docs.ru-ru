---
title: Восстановление существующего хранилища данных
description: Руководство по восстановлению существующего хранилища данных SQL Azure.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d0bcf9ca6373984989d24efd2af4ffbbb19c5548
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759692"
---
# <a name="restore-an-existing-azure-sql-data-warehouse"></a>Восстановление существующего хранилища данных SQL Azure

В этой статье вы узнаете, как восстановить существующее хранилище данных SQL с помощью портал Azure и PowerShell:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Проверьте ресурсы DTU.** Каждое хранилище данных SQL размещается на сервере SQL Server (например, myserver.database.windows.net), который имеет квоту DTU по умолчанию. Убедитесь, что SQL Server имеет достаточное количество оставшихся квот DTU для восстанавливаемой базы данных. Чтобы узнать, как вычислить необходимое количество DTU или запросить дополнительные единицы DTU, ознакомьтесь с разделом [Создание запроса в службу поддержки для хранилища данных SQL](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Перед началом работы

1. Не забудьте [установить Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Наличие существующей точки восстановления, из которой необходимо выполнить восстановление. Если вы хотите создать новое восстановление, см. [руководство по созданию новой точки восстановления, определенной пользователем](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-data-warehouse-through-powershell"></a>Восстановление существующего хранилища данных с помощью PowerShell

Чтобы восстановить существующее хранилище данных из точки восстановления, используйте командлет PowerShell [RESTORE-азсклдатабасе](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) .

1. Откройте PowerShell.

2. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.

3. Выберите подписку, содержащую базу данных, которую нужно восстановить.

4. Перечислите точки восстановления для хранилища данных.

5. Выберите нужные точки восстановления с помощью свойства RestorePointCreationDate.

6. Восстановите хранилище данных в нужную точку восстановления с помощью командлета PowerShell [RESTORE-азсклдатабасе](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) .
        1. Чтобы восстановить хранилище данных SQL на другой логический сервер, убедитесь, что указано другое имя логического сервера.  Этот логический сервер также может находиться в другой группе ресурсов и регионе.
        2. Чтобы выполнить восстановление в другую подписку, используйте кнопку "Переместить", чтобы переместить логический сервер в другую подписку.

7. Убедитесь, что восстановленное хранилище данных подключено.

8. После завершения восстановления можно настроить восстановленное хранилище данных, следуя [настройке базы данных после восстановления](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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

## <a name="restore-an-existing-data-warehouse-through-the-azure-portal"></a>Восстановление существующего хранилища данных с помощью портал Azure

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Перейдите к хранилищу данных SQL, из которого требуется выполнить восстановление.
3. В верхней области колонки обзора выберите **Восстановить**.

    ![ Обзор восстановления](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Выберите **Точки автоматического восстановления** или **Определенные пользователем точки восстановления**. Если в хранилище данных нет точек автоматического восстановления, подождите несколько часов или создайте определенную пользователем точку восстановления перед восстановлением. Для определяемых пользователем точек восстановления выберите существующую или создайте новую. Для **сервера**можно выбрать логический сервер в другой группе ресурсов и регионе или создать новый. После ввода всех параметров щелкните **Проверка и восстановление**.

    ![Точки автоматического восстановления](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Следующие шаги
- [Восстановление удаленного хранилища данных](sql-data-warehouse-restore-deleted-dw.md)
- [Восстановление из хранилища геоархивных данных](sql-data-warehouse-restore-from-geo-backup.md)

 