---
title: Восстановление Хранилища данных SQL Azure | Документация Майкрософт
description: Сведения о восстановлении Хранилища данных SQL Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: ebbcbcc3d0934800980b7d8e00895b11ff2747b7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838941"
---
# <a name="restoring-azure-sql-data-warehouse"></a>Восстановление Хранилища данных SQL Azure 
В этой статье вы узнаете, как выполнить следующие задачи с помощью портала Azure и PowerShell:

- создание точки восстановления;
- восстановление из автоматической точки восстановления или точки восстановления, определенной пользователем;
- восстановление из удаленной базы данных;
- восстановление из геоизбыточной резервной копии;
- создание копии хранилища данных из точки восстановления, определенной пользователем.

## <a name="before-you-begin"></a>Перед началом работы

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Проверьте ресурсы DTU.**  Каждое хранилище данных SQL размещается на сервере SQL Server (например, myserver.database.windows.net), которому выделена квота DTU по умолчанию.  Перед восстановлением хранилища данных SQL убедитесь, что у сервера SQL Server осталась достаточная квота DTU для восстанавливаемой базы данных. Чтобы узнать, как вычислить необходимое количество DTU или запросить дополнительные единицы DTU, ознакомьтесь с разделом [Создание запроса в службу поддержки][Request a DTU quota change].

## <a name="restore-through-powershell"></a>Восстановление с помощью PowerShell

## <a name="install-powershell"></a>Установка PowerShell
Чтобы использовать Azure PowerShell с хранилищем данных SQL, необходимо установить Azure PowerShell.  Узнать версию, выполнив **Get-Module - ListAvailable-Name Az**. Дополнительные сведения об установке последней версии Azure PowerShell см. в статье [Как установить и настроить Azure PowerShell][How to install and configure Azure PowerShell].

## <a name="restore-an-active-or-paused-database-using-powershell"></a>Восстановление активной или приостановленной базы данных с помощью PowerShell
Восстановление базы данных из точки восстановления используйте [восстановления AzSqlDatabase] [ Restore-AzSqlDatabase] командлета PowerShell.

1. Откройте Windows PowerShell.

2. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.

3. Выберите подписку, содержащую базу данных, которую нужно восстановить.

4. Выведите список точек восстановления для базы данных.

5. Выберите нужные точки восстановления с помощью свойства RestorePointCreationDate.

   > [!NOTE]
   > При восстановлении вы можете указать другое имя ServiceObjectiveName (DWU) или другой сервер, находящийся в другом регионе.

6. Восстановите базу данных в желаемой точке восстановления.

7. Убедитесь, что восстановленная база данных подключена к сети.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> Чтобы настроить базу данных после восстановления, см. раздел [Настройка базы данных после восстановления][Configure your database after recovery].
>

## <a name="copy-your-data-warehouse-with-user-defined-restore-points-using-powershell"></a>Копирование хранилища данных с точками восстановления, определенными пользователем, с помощью PowerShell
Восстановление базы данных из точки восстановления, определяемые пользователем используйте [восстановления AzSqlDatabase] [ Restore-AzSqlDatabase] командлета PowerShell.

1. Откройте Windows PowerShell.
2. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.
3. Выберите подписку, содержащую базу данных, которую нужно восстановить.
4. Создайте точку восстановления для немедленного копирования базы данных.
5. Присвойте базе данных временное имя.
6. Получите последнюю точку восстановления с помощью указанной метки RestorePointLabel.
7. Получите идентификатор ресурса базы данных для запуска восстановления.
8. Восстановите базу данных в желаемой точке восстановления.
9. Убедитесь, что восстановленная база данных подключена к сети.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$TempDatabaseName = "<YourTemporaryDatabaseName>"
$Label = "<YourRestorePointLabel"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

# Rename the database to a temporary name
Set-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -NewName $TempDatabaseName

# Get the most recent restore point with the specified label
$LabelledRestorePoint = Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName | where {$_.RestorePointLabel -eq $Label} | sort {$_.RestorePointCreationDate} | select -Last 1

# Get the resource id of the database
$ResourceId = (Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName).ResourceId

# Restore the database to its original name from the labelled restore point from the temporary database
$RestoredDatabase = Restore-AzSqlDatabase -FromPointInTimeBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ResourceId $ResourceId -PointInTime $LabelledRestorePoint.RestorePointCreationDate -TargetDatabaseName $DatabaseName

# Verify the status of restored database
$RestoredDatabase.status

# The original temporary database can be deleted at this point

```

## <a name="restore-a-deleted-database-using-powershell"></a>Восстановление удаленной базы данных с помощью PowerShell
Чтобы восстановить удаленную базу данных, используйте [восстановления AzSqlDatabase] [ Restore-AzSqlDatabase] командлета.

1. Откройте Windows PowerShell.
2. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.
3. Выберите подписку, содержащую удаленную базу данных, которую нужно восстановить.
4. Получите конкретную удаленную базу данных.
5. Восстановите удаленную базу данных.
6. Убедитесь, что восстановленная база данных подключена к сети.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> Чтобы настроить базу данных после восстановления, см. раздел [Настройка базы данных после восстановления][Configure your database after recovery].
>

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Восстановление из географического региона Azure с помощью PowerShell
Чтобы восстановить базу данных, используйте [восстановления AzSqlDatabase] [ Restore-AzSqlDatabase] командлета.

> [!NOTE]
> Можно выполнить геовосстановление в хранилище 2-го поколения! Для этого в качестве необязательного параметра укажите имя ServiceObjectiveName 2-го поколения (например, DW1000**c**).
>

1. Откройте Windows PowerShell.
2. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.
3. Выберите подписку, содержащую базу данных, которую нужно восстановить.
4. Получите базу данных, которую требуется восстановить.
5. Создайте запрос на восстановление базы данных.
6. Проверьте состояние геовосстановленной базы данных.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Чтобы настроить базу данных после восстановления, см. раздел [Настройка базы данных после восстановления][Configure your database after recovery].
>

Восстановленная база данных будет поддерживать прозрачное шифрование данных, если исходная база данных поддерживает прозрачное шифрование данных.

## <a name="restore-through-the-azure-portal"></a>Восстановление с помощью портала Azure

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Создание определяемой пользователем точки восстановления с помощью портала Azure
1. Войдите на [портал Azure][Azure portal].

2. Перейдите в хранилище данных SQL, для которого вы хотите создать точку восстановления.

3. В верхней области колонки обзора выберите **Новая точка восстановления**.

    ![Новая точка восстановления](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)

4. Укажите имя точки восстановления.

    ![Имя точки восстановления](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Восстановление активной или приостановленной базы данных с помощью портала Azure
1. Войдите на [портал Azure][Azure portal].
2. Перейдите в хранилище данных SQL, из которого требуется выполнить восстановление.
3. В верхней области колонки обзора выберите **Восстановить**.

    ![ Обзор восстановления](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)

4. Выберите **Точки автоматического восстановления** или **Определенные пользователем точки восстановления**.

    ![Точки автоматического восстановления](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)

5. Для определенных пользователем точек восстановления **выберите имеющуюся** или **создайте новую**.

    ![Определяемые пользователем точки восстановления](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Восстановление удаленной базы данных на портале Azure
1. Войдите на [портал Azure][Azure portal].
2. Перейдите на SQL Server, где размещалась удаленная база данных.
3. Выберите значок удаленных баз данных в содержании.

    ![Удаленные базы данных](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_0.png)

4. Выберите удаленную базу данных, которую хотите восстановить.

    ![Выбор удаленных баз данных](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_1.png)

5. Укажите новое имя базы данных.

    ![Указание нового имени базы данных](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_2.png)

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
