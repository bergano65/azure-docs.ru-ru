---
title: Точки восстановления, определяемые пользователем
description: Как создать точку восстановления для пула S'L.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d056b30c44ced5f3e8ce9041e2366290bee485da
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350143"
---
# <a name="user-defined-restore-points"></a>Точки восстановления, определяемые пользователем

В этой статье вы научитесь создавать новую точку восстановления, определяемую пользователем, для пула S'L в Azure Synapse Analytics с помощью PowerShell и портала Azure.

## <a name="create-user-defined-restore-points-through-powershell"></a>Создание точек восстановления, определяемых пользователем, через PowerShell

Для создания определяемой пользователем точки восстановления используйте смдлет [New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0) PowerShell.

1. Перед тем, как начать, не забудьте [установить Azure PowerShell.](https://docs.microsoft.com/powershell/azure/overview)
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

6. Смотрите список всех существующих точек восстановления.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Создание точек восстановления, определяемых пользователем, через портал Azure

Точки восстановления, определяемые пользователем, также могут быть созданы через портал Azure.

1. Вопийте на свой [портал Azure.](https://portal.azure.com/)

2. Перейдите к пулу S'L, для которого необходимо создать точку восстановления.

3. Выберите **Обзор** из левой панели, выберите **новую точку восстановления.** Если кнопка «Новая точка восстановления» не включена, убедитесь, что пул S'L не приостанавливаемый.

    ![Новая точка восстановления](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Укажите имя для точки восстановления, определяемой пользователем, и нажмите **Apply**. Точки восстановления, определяемые пользователем, имеют период удержания по умолчанию в семь дней.

    ![Имя точки восстановления](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Восстановление существующего пула S'L](sql-data-warehouse-restore-active-paused-dw.md)
- [Восстановление удаленного пула S'L](sql-data-warehouse-restore-deleted-dw.md)
- [Восстановление из пула георезервного копирования S'L](sql-data-warehouse-restore-from-geo-backup.md)

