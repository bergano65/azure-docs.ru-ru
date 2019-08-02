---
title: Управление долгосрочным хранением резервных копий базы данных SQL Azure | Документация Майкрософт
description: Узнайте, как сохранять автоматически создаваемые резервные копии в хранилище SQL Azure и восстанавливать их.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 04/17/2019
ms.openlocfilehash: 38ecd7797452c9a16b859da921287b8026f0660d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567799"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Управление долгосрочным хранением резервных копий базы данных SQL Azure

В Базе данных SQL Azure можно настроить отдельную базу данных или базу данных в пуле с политикой [долгосрочного хранения резервных копий](sql-database-long-term-retention.md) (LTR) таким образом, чтобы обеспечивалось автоматическое хранение больших двоичных объектов в Azure на протяжении до 10 лет. После этого появляется возможность восстановления базы данных с помощью резервных копий на портале Azure или через PowerShell.

> [!IMPORTANT]
> [Управляемый экземпляр Базы данных SQL Azure](sql-database-managed-instance.md) в настоящее время не поддерживает долгосрочное хранение резервных копий.

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>Настройка политик долгосрочного хранения и восстановление резервных копий с помощью портала Azure

В следующих разделах показано, как настроить долгосрочное хранение, просматривать резервные копии с моделью долгосрочного хранения, а также восстанавливать резервные копии из этой модели на портале Azure.

### <a name="configure-long-term-retention-policies"></a>Настройка политик долгосрочного хранения

Вы можете настроить базу данных SQL для [сохранения автоматически создаваемых резервных копий](sql-database-long-term-retention.md) на срок больший, чем срок хранения для вашего уровня служб. 

1. На портале Azure выберите свой сервер SQL и щелкните **Управления резервными копиями**. На вкладке **Настройка политик** *установите флажок рядом с базой данных, для которой требуется задать или изменить политики долгосрочного хранения резервных копий*. Если флажок рядом с базой данных не выбран, изменения политики не будут применяться к этой базе данных.  

   ![управление ссылкой резервных копий](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. На панели **Configure policies** (Настройка политик) выберите сохранение еженедельных, ежемесячных или ежегодных резервных копий и укажите срок хранения для каждого из вариантов. 

   ![Настройка политик](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. По завершении операций нажмите кнопку **Применить**.

> [!IMPORTANT]
> При включении политики долгосрочного хранения резервных копий может потребоваться до 7 дней, чтобы первая резервная копия стала видимой и доступной для восстановления. Дополнительные сведения о резервном копировании LTR каданце см. в статье [долгосрочное хранение резервных копий](sql-database-long-term-retention.md).

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Просмотр резервных копий и восстановление из резервной копии с помощью портала Azure

Просмотрите резервные копии, которые сохраняются в указанной базе данных под управлением политики долгосрочного хранения (LTR), и выполните восстановление из этих резервных копий. 

1. На портале Azure выберите свой сервер SQL и щелкните **Управления резервными копиями**. На вкладке **Доступные архивы** выберите базу данных, для которой нужно просмотреть доступные резервные копии.

   ![Выбор базы данных](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. На панели **Доступные архивы** просмотрите доступные резервные копии. 

   ![Просмотр резервных копий](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Выберите резервную копию, на основе которой требуется выполнить восстановление, и задайте новое название базы данных.

   ![восстановление](./media/sql-database-long-term-retention/ltr-restore.png)

5. Нажмите кнопку **ОК**, чтобы восстановить базу данных из резервной копии, расположенной в хранилище SQL Azure, в новую базу данных.

6. На панели инструментов щелкните значок уведомления, чтобы просмотреть состояние задания восстановления.

   ![Ход выполнения задания восстановления](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. После завершения задания откройте страницу **Базы данных SQL**, чтобы просмотреть восстановленную базу данных.

> [!NOTE]
> Здесь вы можете подключиться к восстановленной базе данных с помощью SQL Server Management Studio и выполнить необходимые задания, например [извлечь часть данных из восстановленной базы данных, чтобы скопировать их в имеющуюся базу данных, или удалить имеющуюся базу данных и присвоить ее имя восстановленной базе данных](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>Настройка политик долгосрочного хранения и восстановление резервных копий с помощью PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка предназначена для модуля AZ. SQL. Эти командлеты см. в разделе [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле AZ и в модулях AzureRm существенно идентичны.

В следующем разделе показано, как настроить долгосрочное хранение резервных копий, просматривать резервные копии в хранилище SQL Azure и выполнять восстановление из резервной копии в хранилище SQL Azure с помощью PowerShell.


### <a name="rbac-roles-to-manage-long-term-retention"></a>Роли RBAC для управления долгосрочным хранением

Чтобы управлять резервными копиями LTR, необходимо иметь роль 
- владельца подписки или
- участника SQL Server в области **Подписки** или
- участника SQL Server в области **Подписки**.

Если необходим более детальный контроль, можно создать пользовательские роли RBAC и назначить их в области **Подписки**. 

Для **Get-азсклдатабаселонгтермретентионбаккуп** и **RESTORE-азсклдатабасе** роль должна иметь следующие разрешения:

Microsoft.Sql/locations/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read
 
Для **Remove-азсклдатабаселонгтермретентионбаккуп** роль должна иметь следующие разрешения:

Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete


### <a name="create-an-ltr-policy"></a>Создание политики LTR

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

# get the server
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Просмотр политик LTR
В этом примере показан процесс выведения списка политик LTR на сервере.

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Удаление политики LTR
В этом примере показано, как удалить политику LTR из базы данных.

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Просмотр резервных копий LTR

В этом примере показано, как вывести список резервных копий LTR на сервере. 

```powershell
# Get the list of all LTR backups in a specific Azure region 
# The backups are grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest
# backup first.  
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location 

# Get the list of LTR backups from the Azure region under 
# the named server. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# Get the LTR backups for a specific database from the Azure region under the named server 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Удаление резервных копий LTR

В этом примере показано, как удалить резервную копию LTR из списка резервных копий.

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```
> [!IMPORTANT]
> Удаление резервной копии LTR отменить нельзя. Можно настроить уведомления о каждом удалении в Azure Monitor, выполнив фильтрацию по операции Deletes a long term retention backup (Удаление резервной копии долгосрочного хранения). В журнале действий содержатся сведения о том, кто и когда выполнил запрос. Подробные сведения см. в статье [Создание, просмотр и управление оповещениями журнала действий с помощью Azure Monitor](../azure-monitor/platform/alerts-activity-log.md).
>

### <a name="restore-from-ltr-backups"></a>Восстановление из резервных копий LTR
В этом примере показано, как выполнить восстановление из резервной копии LTR. Обратите внимание, что интерфейс не меняется, но параметр идентификатора ресурса теперь требует наличия идентификатора ресурса резервной копии LTR. 

```powershell
# Restore LTR backup as an S3 database
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> Здесь вы можете подключиться к восстановленной базе данных с помощью SQL Server Management Studio и выполнить необходимые задания, например извлечь часть данных из восстановленной базы данных, чтобы скопировать их в имеющуюся базу данных или удалить имеющуюся базу данных и присвоить ее имя восстановленной базе данных. Ознакомьтесь с [восстановлением до точки во времени](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о резервных копиях базы данных, создаваемых автоматически службой, см. в [этой статье](sql-database-automated-backups.md).
- Дополнительные сведения о долгосрочном хранении резервных копий см. в статье [Хранение резервных копий базы данных SQL Azure до 10 лет](sql-database-long-term-retention.md).
