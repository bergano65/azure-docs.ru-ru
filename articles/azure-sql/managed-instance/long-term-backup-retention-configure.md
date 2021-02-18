---
title: 'Управляемый экземпляр Azure SQL: долгосрочное хранение резервных копий (PowerShell)'
description: Узнайте, как хранить и восстанавливать автоматические резервные копии в отдельных контейнерах хранилища BLOB-объектов Azure для Управляемый экземпляр Azure SQL с помощью PowerShell.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 04/29/2020
ms.openlocfilehash: bb74a2e271473666332c627f6ad4324ca597e40c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593351"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Управление Azure SQL Управляемый экземпляр долгосрочного хранения резервных копий (PowerShell)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

В Управляемый экземпляр Azure SQL можно настроить политику [долгосрочного хранения резервных копий](../database/long-term-retention-overview.md#sql-managed-instance-support) (LTR) как ограниченную общедоступную функцию предварительной версии. Это позволяет автоматически хранить резервные копии баз данных в отдельных контейнерах хранилища BLOB-объектов Azure до 10 лет. Затем можно восстановить базу данных, используя эти резервные копии с помощью PowerShell.

   > [!IMPORTANT]
   > LTR для управляемых экземпляров в настоящее время ограничен предварительной версией и доступен для подписок EA и CSP в зависимости от регистра. Чтобы запросить регистрацию, создайте запрос в [службу поддержки Azure](https://azure.microsoft.com/support/create-ticket/). Для типа проблемы выберите техническая проблема, для службы выберите Управляемый экземпляр Базы данных SQL и для типа проблемы выберите **резервное копирование, восстановление и непрерывность бизнес-процессов/долгосрочное хранение резервных копий**. В запросе необходимо указать, что вы хотите зарегистрировать в ограниченном общедоступном предварительном просмотре LTR для управляемого экземпляра.

В следующем разделе показано, как настроить долгосрочное хранение резервных копий, просматривать резервные копии в хранилище SQL Azure и выполнять восстановление из резервной копии в хранилище SQL Azure с помощью PowerShell.

## <a name="azure-roles-to-manage-long-term-retention"></a>Роли Azure для управления долгосрочным хранением

Для **Get-азсклинстанцедатабаселонгтермретентионбаккуп** и **RESTORE-азсклинстанцедатабасе** необходимо иметь одну из следующих ролей:

- Роль владельца подписки или
- Роль участника Управляемый экземпляр или
- Пользовательская роль со следующими разрешениями:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

Для **Remove-азсклинстанцедатабаселонгтермретентионбаккуп** необходимо иметь одну из следующих ролей:

- Роль владельца подписки или
- Пользовательская роль со следующим разрешением:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> Роль участника Управляемый экземпляр не имеет разрешения на удаление резервных копий слева направо.

Разрешения RBAC для Azure можно предоставить в *подписке* или области *группы ресурсов* . Однако для доступа к резервным копиям LTR, принадлежащим удаленному экземпляру, разрешение должно быть предоставлено в области действия *подписки* этого экземпляра.

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

## <a name="create-an-ltr-policy"></a>Создание политики LTR

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount

Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W' 
    YearlyRetention = 'P5Y' 
    WeekOfYear = '16'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

## <a name="view-ltr-policies"></a>Просмотр политик LTR

В этом примере показано, как вывести список политик LTR в экземпляре для отдельной базы данных.

```powershell
# gets the current version of LTR policy for a database
$LTRPolicies = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup
}
Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy 
```

В этом примере показано, как вывести список политик LTR для всех баз данных в экземпляре.

```powershell
# gets the current version of LTR policy for all of the databases on an instance

$Databases = Get-AzSqlInstanceDatabase -ResourceGroupName $resourceGroup -InstanceName $instanceName

$LTRParams = @{
    InstanceName = $instanceName
    ResourceGroupName = $resourceGroup
}

foreach($database in $Databases.Name){
    Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRParams  -DatabaseName $database
 }
```

## <a name="clear-an-ltr-policy"></a>Удаление политики LTR

В этом примере показано, как удалить политику LTR из базы данных.

```powershell
# remove the LTR policy from a database
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    RemovePolicy = $true
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

## <a name="view-ltr-backups"></a>Просмотр резервных копий LTR

В этом примере показано, как перечислить резервные копии на LTR в пределах экземпляра.

```powershell

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$LTRBackupParam = @{
    Location = $instance.Location 
    DatabaseState = 'Live'
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# only list the latest LTR backup for each database
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    OnlyLatestPerDatabase = $true
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 
```

## <a name="delete-ltr-backups"></a>Удаление резервных копий LTR

В этом примере показано, как удалить резервную копию LTR из списка резервных копий.

```powershell
# remove the earliest backup
# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Удаление резервной копии LTR отменить нельзя. Чтобы удалить резервную копию LTR после удаления экземпляра, необходимо иметь разрешение на область действия подписки. Вы можете настроить уведомления о каждом удалении в Azure Monitor путем фильтрации для операции "Удаление резервной копии долгосрочного хранения". В журнале действий содержатся сведения о том, кто и когда выполнил запрос. Подробные сведения см. в статье [Создание, просмотр и управление оповещениями журнала действий с помощью Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md).

## <a name="restore-from-ltr-backups"></a>Восстановление из резервных копий LTR

В этом примере показано, как выполнить восстановление из резервной копии LTR. Обратите внимание, что интерфейс не меняется, но параметр идентификатора ресурса теперь требует наличия идентификатора ресурса резервной копии LTR.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbname
    OnlyLatestPerDatabase = $true
}
$ltrBackup = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 

$RestoreLTRParam = @{
    TargetInstanceName          = $instanceName 
    TargetResourceGroupName     = $resourceGroup 
    TargetInstanceDatabaseName  = $dbName
    FromLongTermRetentionBackup = $true
    ResourceId                  = $ltrBackup.ResourceId 
}
Restore-AzSqlInstanceDatabase @RestoreLTRParam
```

> [!IMPORTANT]
> Для восстановления из резервной копии LTR после удаления экземпляра необходимо иметь разрешения, ограниченные подпиской на экземпляр, и эта подписка должна быть активной. Необходимо также опустить необязательный параметр-ResourceGroupName.

> [!NOTE]
> Здесь вы можете подключиться к восстановленной базе данных с помощью SQL Server Management Studio и выполнить необходимые задания, например извлечь часть данных из восстановленной базы данных, чтобы скопировать их в имеющуюся базу данных или удалить имеющуюся базу данных и присвоить ее имя восстановленной базе данных. Ознакомьтесь с [восстановлением до точки во времени](../database/recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о резервных копиях базы данных, создаваемых автоматически службой, см. в [этой статье](../database/automated-backups-overview.md).
- Дополнительные сведения о долгосрочном хранении резервных копий см. в статье [Хранение резервных копий базы данных SQL Azure до 10 лет](../database/long-term-retention-overview.md).
