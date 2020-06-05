---
title: Управляемый экземпляр — восстановление до точки во времени
description: Восстановление базы данных SQL в управляемом экземпляре до предыдущей точки во времени.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 21af96e8527bb3fc18666c0353ab3bdbe2a010f1
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773808"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>Восстановление базы данных SQL в управляемом экземпляре до предыдущей точки во времени

Используйте восстановление до точки во времени, чтобы создать базу данных в качестве копии другой базы данных из прошлого. В этой статье описывается, как выполнить восстановление базы данных до точки во времени в управляемом экземпляре базы данных SQL Azure.

Восстановление до точки во времени удобно использовать в таких сценариях восстановления, как инциденты, вызванные ошибками, неправильно загруженные данные или удаление важных данных. Его также можно использовать для тестирования или аудита. Файлы резервных копий хранятся в течение 7–35 дней, в зависимости от параметров базы данных.

Восстановление до точки во времени позволяет восстановить базу данных:

- из существующей базы данных;
- из удаленной базы данных;
- в том же управляемом экземпляре или в другом управляемом экземпляре. 

## <a name="limitations"></a>Ограничения

Восстановление до точки во времени в управляемый экземпляр имеет следующие ограничения:

- При восстановлении из одного управляемого экземпляра в другой оба экземпляра должны находиться в одной подписке и регионе. Восстановление между регионами и между подписками в настоящее время не поддерживается.
- Восстановление всего управляемого экземпляра до точки во времени невозможно. В этой статье описывается единственная возможная операция: восстановление до точки во времени базы данных, размещенной в управляемом экземпляре.

> [!WARNING]
> Учитывайте размер хранилища управляемого экземпляра. В зависимости от размера восстанавливаемых данных в хранилище экземпляра может не хватить места. Если для восстанавливаемых данных недостаточно места, используйте другой подход.

В следующей таблице приведены сценарии восстановления до точки во времени для управляемых экземпляров:

|           |Восстановление существующей базы данных в том же управляемом экземпляре| Восстановление существующей базы данных в другом управляемом экземпляре|Восстановление удаленной базы данных в том же управляемом экземпляре|Восстановление удаленной базы данных в другом управляемом экземпляре|
|:----------|:----------|:----------|:----------|:----------|
|**Портал Azure**| Да|Нет |Да|Нет|
|**Azure CLI**|Да |Да |Нет|Нет|
|**PowerShell**| Да|Да |Да|Да|

## <a name="restore-an-existing-database"></a>Восстановление существующей базы данных

Восстановите существующую базу данных в том же экземпляре с помощью портала Azure, PowerShell или Azure CLI. Чтобы восстановить базу данных в другом экземпляре, используйте PowerShell или Azure CLI, чтобы указать свойства целевого управляемого экземпляра и группы ресурсов. Если эти параметры не заданы, база данных будет восстановлена в существующем экземпляре по умолчанию. Портал Azure в настоящее время не поддерживает восстановление в другом экземпляре.

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. Войдите на [портал Azure](https://portal.azure.com). 
2. Перейдите к управляемому экземпляру и выберите базу данных, которую требуется восстановить.
3. На странице базы данных выберите **Восстановить**:

    ![Восстановление базы данных на портале Azure](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. На странице **Восстановление** выберите дату и время для восстановления базы данных.
5. Чтобы восстановить базу данных, нажмите **Подтвердить**. Это действие запускает процесс восстановления, который создает новую базу данных и заполняет ее данными из исходной базы данных на указанный момент времени. Дополнительные сведения о процессе восстановления см. в статье [Время восстановления](sql-database-recovery-using-backups.md#recovery-time).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Если у вас еще не установлен Azure PowerShell, см. раздел [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Чтобы восстановить базу данных с помощью PowerShell, укажите значения параметров в следующей команде. Затем выполните команду:

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

Чтобы восстановить базу данных в другом управляемом экземпляре, также укажите имена целевой группы ресурсов и целевого управляемого экземпляра:  

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

Дополнительные сведения см. в разделе [Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Если вы еще не установили Azure CLI, обратитесь к разделу [Установка Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Чтобы восстановить базу данных с Azure CLI, укажите значения параметров в следующей команде. Затем выполните команду:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Чтобы восстановить базу данных в другом управляемом экземпляре, также укажите имена целевой группы ресурсов и управляемого экземпляра:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Подробное описание доступных параметров см. в [документации по CLI для восстановления базы данных в управляемом экземпляре](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore).

---

## <a name="restore-a-deleted-database"></a>восстановлением удаленной базы данных;

Восстановление удаленной базы данных можно выполнить с помощью PowerShell или портала Azure. Чтобы восстановить удаленную базу данных в том же экземпляре, используйте портал Azure или PowerShell. Чтобы восстановить удаленную базу данных в другом экземпляре, используйте PowerShell. 

### <a name="portal"></a>Портал 


Чтобы восстановить управляемую базу данных с помощью портала Azure, откройте страницу обзора управляемого экземпляра и выберите **Удаленные базы данных**. Выберите удаленную базу данных, которую требуется восстановить, и введите имя новой базы данных, которая будет создана из данных, восстановленных из резервной копии.

  ![Снимок экрана: восстановление удаленной базы данных экземпляра SQL Azure](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="powershell"></a>PowerShell

Чтобы восстановить базу данных в том же экземпляре, обновите значения параметров, а затем выполните следующую команду PowerShell: 

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

Чтобы восстановить базу данных в другом управляемом экземпляре, также укажите имена целевой группы ресурсов и целевого управляемого экземпляра:

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

Чтобы перезаписать существующую базу данных, выполните следующие действия.

1. Удалите существующую базу данных, которую требуется перезаписать.
2. Переименуйте базу данных, восстановленную до точки во времени, присвоив ей имя удаленной базы данных.

### <a name="drop-the-original-database"></a>Удаление исходной базы данных

Вы можете удалить базу данных с помощью портала Azure, PowerShell или Azure CLI.

Можно также удалить базу данных, подключившись к управляемому экземпляру напрямую, запустив SQL Server Management Studio (SSMS), а затем выполнив следующую команду Transact-SQL (T-SQL):

```sql
DROP DATABASE WorldWideImporters;
```

Для подключения к базе данных в управляемом экземпляре воспользуйтесь одним из следующих методов:

- [SSMS/Azure Data Studio через виртуальную машину Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Точка-сеть](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Общедоступная конечная точка](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[Портал](#tab/azure-portal)

На портале Azure выберите базу данных из управляемого экземпляра, а затем выберите **Удалить**.

   ![Удаление базы данных на портале Azure](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Используйте следующую команду PowerShell для удаления существующей базы данных из управляемого экземпляра:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Используйте следующую команду Azure CLI для удаления существующей базы данных из управляемого экземпляра:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Изменение имени новой базы данных на имя исходной базы данных

Подключитесь к управляемому экземпляру напрямую и запустите SQL Server Management Studio. Затем выполните следующий запрос Transact-SQL (T-SQL). Запрос изменит имя восстановленной базы данных на имя удаленной базы данных, которую предполагается перезаписать.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Для подключения к базе данных в управляемом экземпляре воспользуйтесь одним из следующих методов:

- [Виртуальная машина Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Точка-сеть](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Общедоступная конечная точка](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об [автоматических резервных копиях](sql-database-automated-backups.md).
