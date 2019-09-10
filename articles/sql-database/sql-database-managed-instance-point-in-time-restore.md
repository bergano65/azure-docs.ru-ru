---
title: Управляемый экземпляр базы данных SQL — восстановление до точки во времени | Документация Майкрософт
description: Восстановление базы данных в управляемом экземпляре SQL до предыдущей точки во времени.
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
ms.openlocfilehash: 67f13d16dcf6bbe4fa13fe3a6e78d3e4d61e1999
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862139"
---
# <a name="restore-a-sql-managed-instance-database-to-a-previous-point-in-time"></a>Восстановление базы данных управляемого экземпляра SQL до предыдущей точки во времени

Восстановление до точки во времени (PITR) позволяет создать базу данных в качестве копии другой базы данных в определенный момент времени в прошлом. В этой статье описывается, как выполнить восстановление базы данных на момент времени в управляемом экземпляре.

Восстановление до точки во времени может использоваться в сценариях восстановления, например в инцидентах, вызванных ошибками, неправильной загруженностью данных, удалении важных данных и других проблемах, а также в целях тестирования или аудита. В зависимости от параметров базы данных файлы резервных копий хранятся в течение от 7 до 35 дней.

Восстановление до точки во времени можно использовать для выполнения следующих действий:

- Восстановление базы данных из существующей базы данных.
- Восстановление базы данных из удаленной базы данных.

Кроме того, с помощью управляемого экземпляра восстановление до точки во времени можно использовать для выполнения следующих действий: 

- Восстановление базы данных в том же управляемом экземпляре.
- Восстановление базы данных в другой управляемый экземпляр.


> [!NOTE]
> Восстановление всего управляемого экземпляра на момент времени невозможно. Что можно и объясняет в этой статье, это восстановление базы данных, размещенной на управляемом экземпляре, на момент времени.


## <a name="limitations"></a>Ограничения

При восстановлении в другой управляемый экземпляр оба экземпляра должны находиться в одной подписке и регионе. Восстановление между регионами и между подписками в настоящее время не поддерживается.

> [!WARNING]
> Будьте внимательны с размером хранилища управляемого экземпляра — в зависимости от размера восстанавливаемых данных может потребоваться хранилище экземпляров. Если недостаточно места для восстановленных данных, используйте другой подход.

В следующей таблице приведены сценарии восстановления на момент времени для управляемого экземпляра.

|           |Восстановление существующей базы данных| Восстановление существующей базы данных|Восстановить удаленную базу данных| Восстановить удаленную базу данных|
|:----------|:----------|:----------|:----------|:----------|
|Назначение| Тот же MI|Другой MI |Тот же MI|Другой MI |
|портала Azure| Да|Нет |Нет|Нет|
|Azure CLI|Да |Да |Нет|Нет|
|PowerShell| Да|Да |Да|Да|


## <a name="restore-existing-database"></a>Восстановить существующую базу данных

Восстановите существующую базу данных в том же экземпляре с помощью портал Azure, PowerShell или Azure CLI. Восстановите базу данных в другой экземпляр с помощью PowerShell или Azure CLI, указав свойства целевого управляемого экземпляра и группы ресурсов. Если эти параметры не указаны, по умолчанию база данных будет восстановлена в существующий экземпляр. Восстановление в другой экземпляр в настоящее время не поддерживается с помощью портал Azure. 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

1. Войдите на [портал Azure](https://portal.azure.com). 
1. Перейдите к управляемому экземпляру и выберите базу данных, которую необходимо восстановить. 
1. На странице База данных выберите **восстановить** . 

    ![Восстановить существующую базу данных](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

1. На странице **Восстановление** выберите точку для даты и времени в журнале, в который необходимо восстановить базу данных.
1. Нажмите кнопку **подтвердить** , чтобы восстановить базу данных. При этом запускается процесс восстановления, который создает новую базу данных и заполняется данными из исходной базы данных на нужный момент времени. Дополнительные сведения о процессе восстановления см. в разделе [время восстановления](sql-database-recovery-using-backups.md#recovery-time). 

1. Найти управляемый экземпляр
1. Выберите базу данных, которую необходимо восстановить
1. На экране базы данных щелкните действие восстановления.
1. На экране восстановления выберите дату и время точки в журнале, в который восстанавливается база данных.
1. После подтверждения процесс восстановления начнется и, в зависимости от размера базы данных, будет создана новая база данных и заполнена данными из исходной базы данных в нужный момент времени. Сведения о длительности процесса восстановления см. в статье восстановление с помощью резервных копий.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Если вы еще не установили Azure PowerShell, см. статью [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Чтобы восстановить базу данных с помощью PowerShell, обновите параметры, указав значения, и выполните следующую команду:

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

Чтобы восстановить базу данных на другой управляемый экземпляр, задайте целевое имя группы ресурсов и имя целевого управляемого экземпляра.  

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

Дополнительные сведения см. в разделе [RESTORE-азсклинстанцедатабасе](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase).


# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

Если вы еще не установили Azure CLI, см. статью [установка Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Чтобы восстановить базу данных с помощью Azure CLI, обновите параметры, указав значения, и выполните следующую команду:


```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```


Чтобы восстановить базу данных на другой управляемый экземпляр, задайте целевое имя группы ресурсов и имя целевого управляемого экземпляра.  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Подробное описание доступных параметров см. в разделе [CLI управляемого экземпляра](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore). 

---

## <a name="restore-a-deleted-database"></a>Восстановление удаленной базы данных. 
 
Восстановление удаленной базы данных можно выполнить только с помощью PowerShell. Базу данных можно восстановить в том же экземпляре или в другом экземпляре. 

Чтобы восстановить удаленную базу данных с помощью PowerShell, обновите параметры, указав значения, и выполните следующую команду:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"

$deleted_db = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
            -InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName 

$pointInTime = "2018-06-27T08:51:39.3882806Z"
$properties = New-Object System.Object
$properties | Add-Member -type NoteProperty -name CreateMode -Value "PointInTimeRestore"
$properties | Add-Member -type NoteProperty -name RestorePointInTime -Value $pointInTime
$properties | Add-Member -type NoteProperty -name RestorableDroppedDatabaseId -Value $deleted_db.Id
```

Чтобы восстановить удаленную базу данных в другом экземпляре, измените имя группы ресурсов и имя управляемого экземпляра.

Параметр location должен соответствовать расположению группы ресурсов и управляемого экземпляра.

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-existing-database"></a>Перезаписать существующую базу данных 
 
Для перезаписи существующей базы данных необходимо также выполнить следующие действия.

1. Удалите существующую базу данных, которую требуется перезаписать.
1. Переименуйте восстановленную базу данных на момент времени в имя базы данных, которая была удалена. 


### <a name="drop-original-database"></a>УДАЛИТЬ исходную базу данных 
 
Удаление базы данных можно выполнить с помощью портал Azure, PowerShell или Azure CLI. 

Можно также удалить базу данных, подключившись к управляемому экземпляру напрямую, запустив SQL Server Management Studio (SSMS) и выполнив приведенную ниже команду Transact-SQL (T-SQL).

```sql
DROP DATABASE WorldWideImporters;
```

Для подключения к базе данных управляемого экземпляра используйте один из следующих методов. 

- [Виртуальная машина SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Точка-сеть](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Общедоступная конечная точка](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

В портал Azure выберите базу данных из управляемого экземпляра и нажмите кнопку **Удалить**.

   ![Восстановить существующую базу данных](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Используйте следующую команду PowerShell для удаления существующей базы данных из управляемого экземпляра: 

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

Используйте следующую команду Azure CLI, чтобы удалить существующую базу данных из управляемого экземпляра. 

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---


### <a name="alter-new-database-name-to-original"></a>ИЗМЕНИТЬ имя новой базы данных на исходное

Подключитесь непосредственно к управляемому экземпляру, запустите SQL Server Management Studio, а затем выполните следующий запрос Transact-SQL (T-SQL), чтобы изменить имя восстановленной базы данных на удаленную базу данных, которую вы предполагали перезаписать. 


```sql
ALTER WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```


Для подключения к базе данных управляемого экземпляра используйте один из следующих методов. 

- [Виртуальная машина SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Точка-сеть](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Общедоступная конечная точка](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о [долгосрочном хранении](sql-database-long-term-retention.md) и [автоматической архивации](sql-database-automated-backups.md). 
