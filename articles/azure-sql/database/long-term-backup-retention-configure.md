---
title: База данных SQL Azure. Управление долгосрочным хранением резервных копий
description: Узнайте, как хранить и восстанавливать автоматически создаваемые резервные копии базы данных SQL Azure в службе хранилища Azure (до 10 лет) с помощью портал Azure и PowerShell.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 04/14/2020
ms.openlocfilehash: 4da2572e453339baa3f5e2663a7965fff2035e40
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87531308"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Управление долгосрочным хранением резервных копий базы данных SQL Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

В базе данных SQL Azure можно настроить для базы данных политику [долгосрочного хранения резервных копий](long-term-retention-overview.md) (LTR), чтобы автоматически хранить резервные копии базы данных в отдельных контейнерах хранилища BLOB-объектов Azure до 10 лет. После этого появляется возможность восстановления базы данных с помощью резервных копий на портале Azure или через PowerShell. Вы также можете настроить долгосрочное хранение для [управляемый экземпляр Azure SQL](../managed-instance/long-term-backup-retention-configure.md) , но в настоящее время оно ограничено общедоступной предварительной версией.

## <a name="using-the-azure-portal"></a>Использование портала Azure

В следующих разделах показано, как настроить долгосрочное хранение, просматривать резервные копии с моделью долгосрочного хранения, а также восстанавливать резервные копии из этой модели на портале Azure.

### <a name="configure-long-term-retention-policies"></a>Настройка политик долгосрочного хранения

Вы можете настроить базу данных SQL для [сохранения автоматически создаваемых резервных копий](long-term-retention-overview.md) на срок больший, чем срок хранения для вашего уровня служб.

1. В портал Azure выберите свой экземпляр SQL Server и щелкните **Управление резервными копиями**. На вкладке **Настройка политик** установите флажок рядом с базой данных, для которой требуется задать или изменить политики долгосрочного хранения резервных копий. Если флажок рядом с базой данных не выбран, изменения политики не будут применяться к этой базе данных.  

   ![управление ссылкой резервных копий](./media/long-term-backup-retention-configure/ltr-configure-ltr.png)

2. На панели **Configure policies** (Настройка политик) выберите сохранение еженедельных, ежемесячных или ежегодных резервных копий и укажите срок хранения для каждого из вариантов.

   ![Настройка политик](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

3. По завершении операций нажмите кнопку **Применить**.

> [!IMPORTANT]
> При включении политики долгосрочного хранения резервных копий может потребоваться до 7 дней, чтобы первая резервная копия стала видимой и доступной для восстановления. Дополнительные сведения о резервном копировании LTR каданце см. в статье [долгосрочное хранение резервных копий](long-term-retention-overview.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Просмотр резервных копий и восстановление из резервной копии

Просмотр резервных копий, которые хранятся для определенной базы данных с политикой LTR, и восстановление из этих резервных копий.

1. В портал Azure выберите свой сервер и щелкните **Управление резервными копиями**. На вкладке **Доступные архивы** выберите базу данных, для которой нужно просмотреть доступные резервные копии.

   ![Выбор базы данных](./media/long-term-backup-retention-configure/ltr-available-backups-select-database.png)

1. На панели **Доступные архивы** просмотрите доступные резервные копии.

   ![Просмотр резервных копий](./media/long-term-backup-retention-configure/ltr-available-backups.png)

1. Выберите резервную копию, на основе которой требуется выполнить восстановление, и задайте новое название базы данных.

   ![Восстановление](./media/long-term-backup-retention-configure/ltr-restore.png)

1. Нажмите кнопку **ОК** , чтобы восстановить базу данных из резервной копии в службе хранилища Azure в новую базу данных.

1. На панели инструментов щелкните значок уведомления, чтобы просмотреть состояние задания восстановления.

   ![Ход выполнения задания восстановления](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. После завершения задания откройте страницу **Базы данных SQL**, чтобы просмотреть восстановленную базу данных.

> [!NOTE]
> Здесь вы можете подключиться к восстановленной базе данных с помощью SQL Server Management Studio и выполнить необходимые задания. Например, вы можете [извлечь часть данных из восстановленной базы данных, чтобы скопировать их в существующую базу данных, или удалить существующую базу данных и присвоить ее имя восстановленной базе данных](recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>Использование PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка предназначена для модуля AZ. SQL. Эти командлеты см. в разделе [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле AZ и в модулях AzureRm существенно идентичны.

В следующих разделах показано, как использовать PowerShell для настройки долгосрочного хранения резервных копий, просмотра резервных копий в службе хранилища Azure и восстановления из резервной копии в службе хранилища Azure.

### <a name="azure-roles-to-manage-long-term-retention"></a>Роли Azure для управления долгосрочным хранением

Для **Get-азсклдатабаселонгтермретентионбаккуп** и **RESTORE-азсклдатабасе**необходимо иметь одну из следующих ролей:

- Роль владельца подписки или
- Роль участника SQL Server или
- Пользовательская роль со следующими разрешениями:

   Microsoft. SQL/Locations/Лонгтермретентионбаккупс/Read Microsoft. SQL/Locations/Лонгтермретентионсерверс/Лонгтермретентионбаккупс/Read Microsoft. SQL/Locations/Лонгтермретентионсерверс/longTermRetentionDatabases/longTermRetentionBackups/Read

Для **Remove-азсклдатабаселонгтермретентионбаккуп**необходимо иметь одну из следующих ролей:

- Роль владельца подписки или
- Пользовательская роль со следующим разрешением:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> Роль участника SQL Server не имеет разрешения на удаление резервных копий слева направо.

Разрешения RBAC можно предоставить в *подписке* или области *группы ресурсов* . Однако для доступа к резервным копиям LTR, принадлежащим удаленному серверу, разрешение должно быть предоставлено в области действия *подписки* этого сервера.

- Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

### <a name="create-an-ltr-policy"></a>Создание политики LTR

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Просмотр политик LTR

В этом примере показан процесс выведения списка политик LTR на сервере.

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $serverName | `
    Get-AzSqlDatabaseLongTermRetentionPolicy

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup
```

### <a name="clear-an-ltr-policy"></a>Удаление политики LTR

В этом примере показано, как удалить политику LTR из базы данных.

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Просмотр резервных копий LTR

В этом примере показано, как вывести список резервных копий LTR на сервере.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
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
> Удаление резервной копии LTR отменить нельзя. Чтобы удалить резервную копию LTR после удаления сервера, необходимо иметь разрешение на область подписки. Вы можете настроить уведомления о каждом удалении в Azure Monitor путем фильтрации для операции "Удаление резервной копии долгосрочного хранения". В журнале действий содержатся сведения о том, кто и когда выполнил запрос. Подробные сведения см. в статье [Создание, просмотр и управление оповещениями журнала действий с помощью Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md).

### <a name="restore-from-ltr-backups"></a>Восстановление из резервных копий LTR

В этом примере показано, как выполнить восстановление из резервной копии LTR. Обратите внимание, что этот интерфейс не изменился, но для параметра идентификатора ресурса теперь требуется идентификатор ресурса резервного копирования LTR.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Для восстановления из резервной копии LTR после удаления сервера необходимо иметь разрешения, ограниченные подпиской сервера, и эта подписка должна быть активной. Необходимо также опустить необязательный параметр-ResourceGroupName.

> [!NOTE]
> Здесь вы можете подключиться к восстановленной базе данных с помощью SQL Server Management Studio и выполнить необходимые задания, например извлечь часть данных из восстановленной базы данных, чтобы скопировать их в имеющуюся базу данных или удалить имеющуюся базу данных и присвоить ее имя восстановленной базе данных. Ознакомьтесь с [восстановлением до точки во времени](recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о резервных копиях базы данных, создаваемых автоматически службой, см. в [этой статье](automated-backups-overview.md).
- Дополнительные сведения о долгосрочном хранении резервных копий см. в статье [Хранение резервных копий базы данных SQL Azure до 10 лет](long-term-retention-overview.md).
