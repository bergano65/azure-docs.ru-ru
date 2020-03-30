---
title: Управляемый экземпляр - Восстановление point-in-time (PITR)
description: Восстановление базы данных S'L в управляемом экземпляре к предыдущему моменту времени.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 27f465e6864d0ff639e825c8a816d86648bd8853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268812"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>Восстановление базы данных S'L в управляемом экземпляре к предыдущему моменту времени

Используйте восстановление point-in-time (PITR) для создания базы данных в качестве копии другой базы данных некоторого времени в прошлом. В этой статье описывается, как вовремя восстановить базу данных в управляемом экземпляре базы данных Azure S'L.

Восстановление point-in-time полезно в сценариях восстановления, таких как инциденты, вызванные ошибками, неправильно загруженные данные или удаление важных данных. Вы также можете использовать его просто для тестирования или аудита. Файлы резервного копирования хранятся в течение 7-35 дней, в зависимости от настроек базы данных.

Восстановление point-in-time может восстановить базу данных:

- из существующей базы данных.
- из удаленной базы данных.
- в тот же управляемый экземпляр или в другой управляемый экземпляр. 

## <a name="limitations"></a>Ограничения

Момент времени восстановления управляемого экземпляра имеет следующие ограничения:

- При восстановлении из одного управляемого экземпляра в другой оба экземпляра должны находиться в одной подписке и в регионе. Кросс-регион и восстановление кросс-подписки в настоящее время не поддерживаются.
- Точечное восстановление целого управляемого экземпляра невозможно. В этой статье объясняется только то, что возможно: своевременное восстановление базы данных, размещенной в управляемом экземпляре.

> [!WARNING]
> Будьте в курсе размера хранилища управляемого экземпляра. В зависимости от размера данных, которые будут восстановлены, может исчердаться хранилище экземпляров. Если восстановленных данных недостаточно, используйте другой подход.

В следующей таблице показаны сценарии восстановления моментов для управляемых экземпляров:

|           |Восстановление существующего DB в тот же управляемый экземпляр| Восстановление существующего DB в другой управляемый экземпляр|Восстановление отброшенного DB в тот же управляемый экземпляр|Восстановление отпаженного DB в другой управляемый экземпляр|
|:----------|:----------|:----------|:----------|:----------|
|**Портал Azure**| Да|нет |Да|нет|
|**Лазурный CLI**|Да |Да |нет|нет|
|**PowerShell**| Да|Да |Да|Да|

## <a name="restore-an-existing-database"></a>Восстановление существующей базы данных

Восстановить существующую базу данных в тот же экземпляр с помощью портала Azure, PowerShell или Azure CLI. Чтобы восстановить базу данных в другой экземпляр, используйте PowerShell или Azure CLI, чтобы можно было указать свойства для целевого управляемого экземпляра и группы ресурсов. Если не указать эти параметры, база данных будет восстановлена в существующем экземпляре по умолчанию. Портал Azure в настоящее время не поддерживает восстановление другого экземпляра.

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. Войдите на [портал Azure](https://portal.azure.com). 
2. Перейдите в управляемый экземпляр и выберите базу данных, которую вы хотите восстановить.
3. Выберите **Восстановление** на странице базы данных:

    ![Восстановление базы данных с помощью портала Azure](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. На странице **«Восстановление»** выберите точку для даты и времени восстановления базы данных.
5. Выберите **Подтверждение** для восстановления базы данных. Это действие запускает процесс восстановления, который создает новую базу данных и заполняет ее данными из исходной базы данных в указанный момент времени. Для получения дополнительной информации [Recovery time](sql-database-recovery-using-backups.md#recovery-time)о процессе восстановления см.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Если у вас еще нет установки Azure PowerShell, [см.](https://docs.microsoft.com/powershell/azure/install-az-ps)

Чтобы восстановить базу данных с помощью PowerShell, укажите значения для параметров в следующей команде. Затем запустите команду:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

Чтобы восстановить базу данных в другой управляемый экземпляр, также укажите имена целевой группы ресурсов и целевой управляемый экземпляр:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Для получения подробной информации [см.](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Если у вас еще нет установки Azure CLI, [см.](/cli/azure/install-azure-cli?view=azure-cli-latest)

Чтобы восстановить базу данных с помощью Azure CLI, укажите значения для параметров в следующей команде. Затем запустите команду:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Чтобы восстановить базу данных в другой управляемый экземпляр, также укажите имена целевой группы ресурсов и управляемого экземпляра:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Подробное объяснение доступных параметров см. [CLI documentation for restoring a database in a managed instance](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)

---

## <a name="restore-a-deleted-database"></a>восстановлением удаленной базы данных;

Восстановление удаленной базы данных можно с помощью портала PowerShell или Azure. Чтобы восстановить удаленную базу данных в том же экземпляре, используйте либо портал Azure, либо PowerShell. Чтобы восстановить удаленную базу данных в другой экземпляр, используйте PowerShell. 

### <a name="portal"></a>Портал 


Чтобы восстановить управляемую базу данных с помощью портала Azure, откройте страницу обзора управляемого экземпляра и выберите **удаленные базы данных.** Выберите удаленную базу данных, которую необходимо восстановить, и введите имя для новой базы данных, которая будет создана с данными, восстановленными из резервного копирования.

  ![Скриншот восстановленной удаленной базы данных экземпляров Azure S'L](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="powershell"></a>PowerShell

Чтобы восстановить базу данных в том же экземпляре, обновите значения параметров, а затем запустите следующую команду PowerShell: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

Чтобы восстановить базу данных в другой управляемый экземпляр, также укажите имена целевой группы ресурсов и целевой управляемый экземпляр:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>Перезапись существующей базы данных

Чтобы перезаписать существующую базу данных, необходимо:

1. Бросьте существующую базу данных, которую вы хотите перезаписать.
2. Переименуй базу данных, восстановленную по времени, в название базы данных, которую вы уронили.

### <a name="drop-the-original-database"></a>Удаление исходной базы данных

Вы можете отказаться от базы данных с помощью портала Azure, PowerShell или Azure CLI.

Вы также можете отказаться от базы данных, подключившись к управляемому экземпляру напрямую, запустив студию управления серверами S'L (SSMS), а затем запустив следующую команду Transact-S'L (T-S'L):

```sql
DROP DATABASE WorldWideImporters;
```

Используйте один из следующих методов для подключения к базе данных в управляемом экземпляре:

- [Студия данных SSMS/Azure с помощью виртуального автомата Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Точка-сеть](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Общедоступная конечная точка](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[Портал](#tab/azure-portal)

На портале Azure выберите базу данных из управляемого экземпляра, а затем выберите **Удалить**.

   ![Удалить базу данных с помощью портала Azure](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Используйте следующую команду PowerShell, чтобы упасть из существующей базы данных из управляемого экземпляра:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Используйте следующую команду Azure CLI для отброса существующей базы данных из управляемого экземпляра:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Изменить новое имя базы данных в соответствии с исходным именем базы данных

Подключитесь непосредственно к управляемому экземпляру и запустите студию управления серверами S'L. Затем запустите следующий запрос «Трансакт-СЗЛ» (Т-СЗЛ). Запрос изменит название восстановленной базы данных на имя упавной базы данных, которую вы собираетесь переписать.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Используйте один из следующих методов для подключения к базе данных в управляемом экземпляре:

- [Виртуальная машина Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Точка-сеть](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Общедоступная конечная точка](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте об [автоматизированных резервных копированиях](sql-database-automated-backups.md).
