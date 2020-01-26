---
title: Определенные пользователем точки восстановления
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
ms.custom: seo-lt-2019
ms.openlocfilehash: bf092b5b6c6eb88b565a940de56d614426e34d8e
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759607"
---
# <a name="user-defined-restore-points"></a>Определенные пользователем точки восстановления

Из этой статьи вы узнаете, как создать новую определяемую пользователем точку восстановления для хранилища данных SQL Azure с помощью PowerShell и портал Azure.

## <a name="create-user-defined-restore-points-through-powershell"></a>Создание определяемых пользователем точек восстановления с помощью PowerShell

Чтобы создать определенную пользователем точку восстановления, используйте командлет PowerShell [New-азсклдатабасересторепоинт](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0) .

1. Перед началом убедитесь, что [установлен Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
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

1. Войдите в учетную запись [портал Azure](https://portal.azure.com/) .

2. Перейдите к хранилищу данных SQL, для которого необходимо создать точку восстановления.

3. На панели слева выберите **Обзор** и щелкните **+ создать точку восстановления**. Если кнопка Создать точку восстановления не включена, убедитесь, что хранилище данных не приостановлено.

    ![Новая точка восстановления](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Укажите имя для определенной пользователем точки восстановления и нажмите кнопку **Применить**. Срок хранения по умолчанию для определенных пользователем точек восстановления составляет семь дней.

    ![Имя точки восстановления](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Восстановление существующего хранилища данных](sql-data-warehouse-restore-active-paused-dw.md)
- [Восстановление удаленного хранилища данных](sql-data-warehouse-restore-deleted-dw.md)
- [Восстановление из хранилища геоархивных данных](sql-data-warehouse-restore-from-geo-backup.md)

