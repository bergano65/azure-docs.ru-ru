---
title: Восстановление хранилища данных из географической резервной копии
description: Руководство по географической восстановлению хранилища данных SQL Azure.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 69ba3ed981a27dfff41ea9ea52e1da769a9366c4
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759641"
---
# <a name="geo-restore-azure-sql-data-warehouse"></a>Географическое восстановление хранилища данных SQL Azure

Из этой статьи вы узнаете, как восстановить хранилище данных из географической резервной копии с помощью портал Azure и PowerShell.

## <a name="before-you-begin"></a>Перед началом работы

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Проверьте ресурсы DTU.** Каждое хранилище данных SQL размещается на сервере SQL Server (например, myserver.database.windows.net), который имеет квоту DTU по умолчанию. Убедитесь, что SQL Server имеет достаточное количество оставшихся квот DTU для восстанавливаемой базы данных. Чтобы узнать, как вычислить необходимое количество DTU или запросить дополнительные единицы DTU, ознакомьтесь с разделом [Создание запроса в службу поддержки для хранилища данных SQL](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Восстановление из географического региона Azure с помощью PowerShell

Чтобы выполнить восстановление из географической резервной копии, используйте командлет [Get-азсклдатабасежеобаккуп](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup) и [RESTORE-азсклдатабасе](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) .

> [!NOTE]
> Можно выполнить геовосстановление в хранилище 2-го поколения! Для этого в качестве необязательного параметра укажите имя ServiceObjectiveName 2-го поколения (например, DW1000**c**).
>

1. Перед началом убедитесь, что [установлен Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Откройте PowerShell.
2. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.
3. Выберите подписку, содержащую восстанавливаемое хранилище данных.
4. Получите хранилище данных, которое необходимо восстановить.
5. Создайте запрос восстановления для хранилища данных.
6. Проверьте состояние геовосстановленного хранилища данных.
7. Чтобы настроить хранилище данных после восстановления, см. раздел [Настройка базы данных после восстановления]( ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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

Выполните действия, описанные ниже, чтобы восстановить хранилище данных SQL Azure из географической резервной копии.

1. Войдите в учетную запись [портал Azure](https://portal.azure.com/) .
1. Щелкните **+ создать ресурс** и найдите хранилище данных SQL и нажмите кнопку **создать**.

    ![Создать хранилище данных](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)
1. Заполните сведения, запрошенные на вкладке " **основы** ", и нажмите кнопку " **Далее": дополнительные параметры**.

    ![Основы](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)
1. Для параметра **использовать существующий параметр данных** выберите **резервное копирование** и выберите соответствующую резервную копию в параметрах прокрутки вниз. Щелкните **проверить и создать**.
 
   ![backup](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)
2. После восстановления хранилища данных убедитесь, что **состояние** находится в сети.

## <a name="next-steps"></a>Следующие шаги
- [Восстановление существующего хранилища данных](sql-data-warehouse-restore-active-paused-dw.md)
- [Восстановление удаленного хранилища данных](sql-data-warehouse-restore-deleted-dw.md)