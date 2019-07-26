---
title: Определяемые пользователем точки восстановления | Документация Майкрософт
description: Как создать точку восстановления хранилище данных SQL Azure.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: bb5a267d1c7058193f1d7c8ed4087f5c0ebab267
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68426594"
---
# <a name="user-defined-restore-points"></a>Определенные пользователем точки восстановления

Из этой статьи вы узнаете, как создать новую определяемую пользователем точку восстановления для хранилища данных SQL Azure с помощью PowerShell и портал Azure.

## <a name="create-user-defined-restore-points-through-powershell"></a>Создание определяемых пользователем точек восстановления с помощью PowerShell

Чтобы создать определенную пользователем точку восстановления, используйте командлет PowerShell [New-азсклдатабасересторепоинт][New-AzSqlDatabaseRestorePoint] .

1. Перед началом убедитесь, что [установлен Azure PowerShell][Install Azure PowerShell].
2. Откройте PowerShell.
3. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.
4. Выберите подписку, содержащую базу данных, которую нужно восстановить.
5. Создайте точку восстановления для немедленной копии хранилища данных.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. Просмотрите список всех существующих точек восстановления.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Создание определяемых пользователем точек восстановления с помощью портал Azure

Пользовательские точки восстановления также можно создавать с помощью портал Azure.

1. Войдите в учетную запись [портал Azure][Azure portal] .

2. Перейдите в хранилище данных SQL, для которого вы хотите создать точку восстановления.

3. На панели слева выберите **Обзор** и щелкните **+ создать точку восстановления**. Если кнопка Создать точку восстановления не включена, убедитесь, что хранилище данных не приостановлено.

    ![Новая точка восстановления](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Укажите имя для определенной пользователем точки восстановления и нажмите кнопку **Применить**. Срок хранения по умолчанию для определенных пользователем точек восстановления составляет семь дней.

    ![Имя точки восстановления](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Следующие шаги

- [Восстановление существующего хранилища данных][Restore an existing data warehouse]
- [Восстановление удаленного хранилища данных][Restore a deleted data warehouse]
- [Восстановление из хранилища геоархивных данных][Restore from a geo-backup data warehouse]

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[PowerShelldoc]:./sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-powershell
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md
<!--MSDN references-->
[New-AzSqlDatabaseRestorePoint]: https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
