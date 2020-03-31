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
ms.openlocfilehash: 4390ed39c86e041d3fbd776415f0ffbe71f605bd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350170"
---
# <a name="geo-restore-for-sql-pool"></a>Гео-восстановление для бассейна S'L

В этой статье вы научитесь восстанавливать свой пул s'L из гео-резервного копирования через портал Azure и PowerShell.

## <a name="before-you-begin"></a>Перед началом

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Проверьте ресурсы DTU.** Каждый пул S'L размещается сервером S'L (например, myserver.database.windows.net), который имеет по умолчанию квоту DTU. Удостоверьте, что сервер S'L имеет достаточно оставшихся квот DTU для восстановления базы данных. Чтобы узнать, как вычислить необходимое количество DTU или запросить дополнительные единицы DTU, ознакомьтесь с разделом [Создание запроса в службу поддержки для хранилища данных SQL](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Восстановление из географического региона Лазурного региона через PowerShell

Для восстановления от гео-резервного копирования, используйте [Get-AzSqlDatabaseGeoBack up](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup) и [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) cmdlet.

> [!NOTE]
> Можно выполнить геовосстановление в хранилище 2-го поколения! Для этого в качестве необязательного параметра укажите имя ServiceObjectiveName 2-го поколения (например, DW1000**c**).
>

1. Перед тем, как начать, не забудьте [установить Azure PowerShell.](https://docs.microsoft.com/powershell/azure/overview)
2. Откройте PowerShell.
2. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.
3. Выберите подписку, содержащую хранилище данных, подкоторыминое восстановление.
4. Получите хранилище данных, который вы хотите восстановить.
5. Создайте запрос на восстановление хранилища данных.
6. Проверьте состояние геовосстановленного хранилища данных.
7. Чтобы настроить хранилище данных после восстановления, см. раздел [Настройка базы данных после восстановления]( ../../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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
1. Щелкните **+ Create a resource** (+ Создать ресурс). 

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