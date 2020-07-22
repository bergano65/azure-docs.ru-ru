---
title: 'Управляемый экземпляр Azure SQL: долгосрочное хранение резервных копий (PowerShell)'
description: Узнайте, как хранить и восстанавливать автоматические резервные копии в отдельных контейнерах хранилища BLOB-объектов Azure для Управляемый экземпляр Azure SQL с помощью PowerShell.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 04/29/2020
ms.openlocfilehash: b628ca7f676c3eab80e11da124f4d6aa7ebd52a1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708801"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Управление Azure SQL Управляемый экземпляр долгосрочного хранения резервных копий (PowerShell)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

В Управляемый экземпляр Azure SQL можно настроить политику [долгосрочного хранения резервных копий](../database/long-term-retention-overview.md#sql-managed-instance-support) (LTR) как ограниченную общедоступную функцию предварительной версии. Это позволяет автоматически хранить резервные копии баз данных в отдельных контейнерах хранилища BLOB-объектов Azure до 10 лет. Затем можно восстановить базу данных, используя эти резервные копии с помощью PowerShell.

   > [!IMPORTANT]
   > LTR для управляемых экземпляров в настоящее время ограничен предварительной версией и доступен для подписок EA и CSP в зависимости от регистра. Чтобы запросить регистрацию, создайте запрос в [службу поддержки Azure](https://azure.microsoft.com/support/create-ticket/). Для типа проблемы выберите техническая проблема, для службы выберите Управляемый экземпляр Базы данных SQL и для типа проблемы выберите **резервное копирование, восстановление и непрерывность бизнес-процессов/долгосрочное хранение резервных копий**. В запросе необходимо указать, что вы хотите зарегистрировать в ограниченном общедоступном предварительном просмотре LTR для управляемого экземпляра.

В следующем разделе показано, как настроить долгосрочное хранение резервных копий, просматривать резервные копии в хранилище SQL Azure и выполнять восстановление из резервной копии в хранилище SQL Azure с помощью PowerShell.

## <a name="rbac-roles-to-manage-long-term-retention"></a>Роли RBAC для управления долгосрочным хранением

Для **Get-азсклинстанцедатабаселонгтермретентионбаккуп** и **RESTORE-азсклинстанцедатабасе**необходимо иметь одну из следующих ролей:

- Роль владельца подписки или
- Роль участника Управляемый экземпляр или
- Пользовательская роль со следующими разрешениями:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

Для **Remove-азсклинстанцедатабаселонгтермретентионбаккуп**необходимо иметь одну из следующих ролей:

- Роль владельца подписки или
- Пользовательская роль со следующим разрешением:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> Роль участника Управляемый экземпляр не имеет разрешения на удаление резервных копий слева направо.

Разрешения RBAC можно предоставить в *подписке* или области *группы ресурсов* . Однако для доступа к резервным копиям LTR, принадлежащим удаленному экземпляру, разрешение должно быть предоставлено в области действия *подписки* этого экземпляра.

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
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

## <a name="view-ltr-policies"></a>Просмотр политик LTR

В этом примере показано, как вывести список политик LTR в экземпляре.

```powershell
# gets the current version of LTR policy for the database
$ltrPolicies = Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup
```

## <a name="clear-an-ltr-policy"></a>Удаление политики LTR

В этом примере показано, как удалить политику LTR из базы данных.

```powershell
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

## <a name="view-ltr-backups"></a>Просмотр резервных копий LTR

В этом примере показано, как перечислить резервные копии на LTR в пределах экземпляра.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName

# get the LTR backups for a specific database from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -OnlyLatestPerDatabase
```

## <a name="delete-ltr-backups"></a>Удаление резервных копий LTR

В этом примере показано, как удалить резервную копию LTR из списка резервных копий.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Удаление резервной копии LTR отменить нельзя. Чтобы удалить резервную копию LTR после удаления экземпляра, необходимо иметь разрешение на область действия подписки. Вы можете настроить уведомления о каждом удалении в Azure Monitor путем фильтрации для операции "Удаление резервной копии долгосрочного хранения". В журнале действий содержатся сведения о том, кто и когда выполнил запрос. Подробные сведения см. в статье [Создание, просмотр и управление оповещениями журнала действий с помощью Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md).

## <a name="restore-from-ltr-backups"></a>Восстановление из резервных копий LTR

В этом примере показано, как выполнить восстановление из резервной копии LTR. Обратите внимание, что интерфейс не меняется, но параметр идентификатора ресурса теперь требует наличия идентификатора ресурса резервной копии LTR.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
Restore-AzSqlInstanceDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId `
   -TargetInstanceName $instanceName -TargetResourceGroupName $resourceGroup -TargetInstanceDatabaseName $dbName
```

> [!IMPORTANT]
> Для восстановления из резервной копии LTR после удаления экземпляра необходимо иметь разрешения, ограниченные подпиской на экземпляр, и эта подписка должна быть активной. Необходимо также опустить необязательный параметр-ResourceGroupName.

> [!NOTE]
> Здесь вы можете подключиться к восстановленной базе данных с помощью SQL Server Management Studio и выполнить необходимые задания, например извлечь часть данных из восстановленной базы данных, чтобы скопировать их в имеющуюся базу данных или удалить имеющуюся базу данных и присвоить ее имя восстановленной базе данных. Ознакомьтесь с [восстановлением до точки во времени](../database/recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о резервных копиях базы данных, создаваемых автоматически службой, см. в [этой статье](../database/automated-backups-overview.md).
- Дополнительные сведения о долгосрочном хранении резервных копий см. в статье [Хранение резервных копий базы данных SQL Azure до 10 лет](../database/long-term-retention-overview.md).
